---
date: "2026-11-25T21:21:00.00Z"
published: true
slug: AttentionIsAllYouNeed
tags:
  - Transformers
  - Attention
  - Deep Learning
time_to_read: 6
title: Attention Is All You Need (Vaswani et al., 2017)
description: A from-scratch PyTorch implementation of the Transformer from Attention Is All You Need
type: post
---

```python
import math

import marimo as mo
import torch
import torch.nn as nn
import torch.nn.functional as F
```

A from-scratch PyTorch implementation of the Transformer, one building block per section,
with a check after each block.

$$\mathrm{Attention}(Q,K,V)=\mathrm{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right)V$$

## 1. Scaled dot-product attention

Dividing by $\sqrt{d_k}$ keeps the dot products from growing with $d_k$, which would push
the softmax into regions with tiny gradients. A boolean `mask` (True = may attend) sets
blocked scores to $-\infty$ before the softmax.

```python
def scaled_dot_product_attention(q, k, v, mask=None):
    d_k = q.size(-1)
    scores = q @ k.transpose(-2, -1) / math.sqrt(d_k)
    if mask is not None:
        scores = scores.masked_fill(~mask, float("-inf"))
    weights = F.softmax(scores, dim=-1)
    return weights @ v, weights
```

```python
# check: matches PyTorch's reference implementation, and weights sum to 1
_q, _k, _v = (torch.randn(2, 4, 5, 8) for _ in range(3))
_out, _w = scaled_dot_product_attention(_q, _k, _v)
assert torch.allclose(_out, F.scaled_dot_product_attention(_q, _k, _v), atol=1e-5)
assert torch.allclose(_w.sum(-1), torch.ones(2, 4, 5))
"scaled dot-product attention: OK"
```

## 2. Multi-head attention

Instead of one attention over $d_{model}$ dimensions, project into $h$ heads of size
$d_k=d_{model}/h$, attend in each, concatenate, and project back:

$$
\mathrm{MultiHead}(Q,K,V)=\mathrm{Concat}(\mathrm{head}_1,\dots,\mathrm{head}_h)W^O,\quad
\mathrm{head}_i=\mathrm{Attention}(QW_i^Q,KW_i^K,VW_i^V)
$$

```python
class MultiHeadAttention(nn.Module):
    def __init__(self, d_model: int, n_heads: int, dropout: float = 0.1):
        super().__init__()
        assert d_model % n_heads == 0
        self.n_heads = n_heads
        self.d_k = d_model // n_heads
        self.w_q = nn.Linear(d_model, d_model)
        self.w_k = nn.Linear(d_model, d_model)
        self.w_v = nn.Linear(d_model, d_model)
        self.w_o = nn.Linear(d_model, d_model)
        self.dropout = nn.Dropout(dropout)

    def _split(self, x):
        b, t, _ = x.shape
        return x.view(b, t, self.n_heads, self.d_k).transpose(1, 2)

    def forward(self, q, k, v, mask=None):
        b, t, _ = q.shape
        q, k, v = self._split(self.w_q(q)), self._split(self.w_k(k)), self._split(self.w_v(v))
        if mask is not None:
            mask = mask.unsqueeze(1)
        out, self.attn = scaled_dot_product_attention(q, k, v, mask)
        out = out.transpose(1, 2).reshape(b, t, -1)
        return self.dropout(self.w_o(out))
```

```python
_mha = MultiHeadAttention(32, 4, dropout=0.0)
_x = torch.randn(2, 6, 32)
assert _mha(_x, _x, _x).shape == (2, 6, 32)
# causal mask: output at position t must not depend on later positions
_causal = torch.tril(torch.ones(6, 6, dtype=torch.bool)).expand(2, 6, 6)
_y1 = _mha(_x, _x, _x, _causal)
_x2 = _x.clone()
_x2[:, 4:] = torch.randn(2, 2, 32)
_y2 = _mha(_x2, _x2, _x2, _causal)
assert torch.allclose(_y1[:, :4], _y2[:, :4], atol=1e-5)
"multi-head attention + causal masking: OK"
```

## 3. Position-wise feed-forward network and sinusoidal positional encoding

$$\mathrm{FFN}(x)=\max(0,xW_1+b_1)W_2+b_2$$

Attention has no notion of order, so the paper adds fixed sinusoids to the embeddings:

$$
PE_{(pos,2i)}=\sin\!\left(\frac{pos}{10000^{2i/d_{model}}}\right),\quad
PE_{(pos,2i+1)}=\cos\!\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$

```python
class FeedForward(nn.Module):
    def __init__(self, d_model: int, d_ff: int, dropout: float = 0.1):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(d_model, d_ff), nn.ReLU(), nn.Dropout(dropout), nn.Linear(d_ff, d_model)
        )

    def forward(self, x):
        return self.net(x)

class PositionalEncoding(nn.Module):
    def __init__(self, d_model: int, max_len: int = 5000, dropout: float = 0.1):
        super().__init__()
        self.dropout = nn.Dropout(dropout)
        pos = torch.arange(max_len).unsqueeze(1)
        div = torch.exp(torch.arange(0, d_model, 2) * (-math.log(10000.0) / d_model))
        pe = torch.zeros(max_len, d_model)
        pe[:, 0::2] = torch.sin(pos * div)
        pe[:, 1::2] = torch.cos(pos * div)
        self.register_buffer("pe", pe)

    def forward(self, x):
        return self.dropout(x + self.pe[: x.size(1)])
```

```python
_pe = PositionalEncoding(16, dropout=0.0).pe
assert abs(_pe[3, 0] - math.sin(3)) < 1e-6
assert abs(_pe[3, 1] - math.cos(3)) < 1e-6
assert abs(_pe[5, 2] - math.sin(5 / 10000 ** (2 / 16))) < 1e-6
"positional encoding matches the formula: OK"
```

## 4. Encoder and decoder layers, full model

Each sub-layer is wrapped as $\mathrm{LayerNorm}(x+\mathrm{Dropout}(\mathrm{Sublayer}(x)))$
(post-norm, as in the paper). The decoder adds masked self-attention and encoder-decoder
attention. Embeddings are scaled by $\sqrt{d_{model}}$, and the output projection shares
weights with the target embedding.

```python
class EncoderLayer(nn.Module):
    def __init__(self, d_model, n_heads, d_ff, dropout):
        super().__init__()
        self.attn = MultiHeadAttention(d_model, n_heads, dropout)
        self.ff = FeedForward(d_model, d_ff, dropout)
        self.n1, self.n2 = nn.LayerNorm(d_model), nn.LayerNorm(d_model)
        self.drop = nn.Dropout(dropout)

    def forward(self, x, src_mask):
        x = self.n1(x + self.drop(self.attn(x, x, x, src_mask)))
        return self.n2(x + self.drop(self.ff(x)))

class DecoderLayer(nn.Module):
    def __init__(self, d_model, n_heads, d_ff, dropout):
        super().__init__()
        self.self_attn = MultiHeadAttention(d_model, n_heads, dropout)
        self.cross_attn = MultiHeadAttention(d_model, n_heads, dropout)
        self.ff = FeedForward(d_model, d_ff, dropout)
        self.n1, self.n2, self.n3 = (nn.LayerNorm(d_model) for _ in range(3))
        self.drop = nn.Dropout(dropout)

    def forward(self, x, memory, src_mask, tgt_mask):
        x = self.n1(x + self.drop(self.self_attn(x, x, x, tgt_mask)))
        x = self.n2(x + self.drop(self.cross_attn(x, memory, memory, src_mask)))
        return self.n3(x + self.drop(self.ff(x)))

class Transformer(nn.Module):
    def __init__(self, src_vocab, tgt_vocab, d_model=512, n_heads=8, n_layers=6,
                 d_ff=2048, dropout=0.1, pad_idx=0):
        super().__init__()
        self.d_model, self.pad_idx = d_model, pad_idx
        self.src_emb = nn.Embedding(src_vocab, d_model, padding_idx=pad_idx)
        self.tgt_emb = nn.Embedding(tgt_vocab, d_model, padding_idx=pad_idx)
        self.pos = PositionalEncoding(d_model, dropout=dropout)
        self.enc = nn.ModuleList(EncoderLayer(d_model, n_heads, d_ff, dropout) for _ in range(n_layers))
        self.dec = nn.ModuleList(DecoderLayer(d_model, n_heads, d_ff, dropout) for _ in range(n_layers))
        self.out = nn.Linear(d_model, tgt_vocab, bias=False)
        self.out.weight = self.tgt_emb.weight
        for p in self.parameters():
            if p.dim() > 1:
                nn.init.xavier_uniform_(p)

    def encode(self, src, src_mask):
        x = self.pos(self.src_emb(src) * math.sqrt(self.d_model))
        for layer in self.enc:
            x = layer(x, src_mask)
        return x

    def decode(self, tgt, memory, src_mask, tgt_mask):
        x = self.pos(self.tgt_emb(tgt) * math.sqrt(self.d_model))
        for layer in self.dec:
            x = layer(x, memory, src_mask, tgt_mask)
        return x

    def masks(self, src, tgt):
        src_mask = (src != self.pad_idx).unsqueeze(1)
        t = tgt.size(1)
        causal = torch.tril(torch.ones(t, t, dtype=torch.bool, device=tgt.device))
        tgt_mask = (tgt != self.pad_idx).unsqueeze(1) & causal
        return src_mask, tgt_mask

    def forward(self, src, tgt):
        src_mask, tgt_mask = self.masks(src, tgt)
        memory = self.encode(src, src_mask)
        return self.out(self.decode(tgt, memory, src_mask, tgt_mask))
```

```python
_m = Transformer(20, 20, d_model=32, n_heads=4, n_layers=2, d_ff=64, dropout=0.0).eval()
_src = torch.randint(1, 20, (3, 7))
_tgt = torch.randint(1, 20, (3, 5))
assert _m(_src, _tgt).shape == (3, 5, 20)
# decoder is autoregressive: changing future target tokens leaves earlier logits unchanged
_tgt2 = _tgt.clone()
_tgt2[:, 3:] = torch.randint(1, 20, (3, 2))
assert torch.allclose(_m(_src, _tgt)[:, :3], _m(_src, _tgt2)[:, :3], atol=1e-5)
"full Transformer shapes + causality: OK"
```

## 5. Toy experiment: learn to reverse a sequence

The paper trains on translation with the warmup schedule
$lrate=d_{model}^{-0.5}\min(step^{-0.5},\,step\cdot warmup^{-1.5})$ and Adam with
$\beta=(0.9,0.98)$, $\epsilon=10^{-9}$. Translation is too heavy for a notebook, so we
use the same schedule on a task a small model solves in seconds.

```python
steps = mo.ui.slider(100, 1500, value=600, step=100, label="training steps")
steps
```

```python
import torch.nn.functional as _F

torch.manual_seed(0)
PAD, BOS, EOS, V, L = 0, 1, 2, 14, 8
d_model, warmup = 64, 200

def batch(n=64):
    body = torch.randint(3, V, (n, L))
    src = torch.cat([body, torch.full((n, 1), EOS)], 1)
    rev = body.flip(1)
    tgt_in = torch.cat([torch.full((n, 1), BOS), rev], 1)
    tgt_out = torch.cat([rev, torch.full((n, 1), EOS)], 1)
    return src, tgt_in, tgt_out

model = Transformer(V, V, d_model=d_model, n_heads=4, n_layers=2, d_ff=128, dropout=0.1)
opt = torch.optim.Adam(model.parameters(), lr=1.0, betas=(0.9, 0.98), eps=1e-9)
sched = torch.optim.lr_scheduler.LambdaLR(
    opt, lambda s: d_model**-0.5 * min((s + 1) ** -0.5, (s + 1) * warmup**-1.5)
)
losses = []
for _ in range(steps.value):
    s, ti, to = batch()
    logits = model(s, ti)
    loss = _F.cross_entropy(logits.reshape(-1, V), to.reshape(-1), label_smoothing=0.1)
    opt.zero_grad()
    loss.backward()
    opt.step()
    sched.step()
    losses.append(loss.item())

model.eval()
s, ti, to = batch(256)
with torch.no_grad():
    acc = (model(s, ti).argmax(-1) == to).float().mean().item()
mo.md(f"Final loss **{losses[-1]:.3f}** (label smoothing 0.1 floors it above 0), "
      f"teacher-forced token accuracy **{acc:.1%}**.")
```

```python
import json

mo.ui.table([{"step": i, "loss": round(l, 3)} for i, l in enumerate(losses) if i % 50 == 0])
```

```python
def greedy(src, max_len=L + 1):
    src_mask = (src != 0).unsqueeze(1)
    memory = model.encode(src, src_mask)
    ys = torch.full((src.size(0), 1), BOS)
    for _ in range(max_len):
        _, tm = model.masks(src, ys)
        nxt = model.out(model.decode(ys, memory, src_mask, tm))[:, -1].argmax(-1, keepdim=True)
        ys = torch.cat([ys, nxt], 1)
    return ys[:, 1:]

_s, _, _to = batch(200)
with torch.no_grad():
    _pred = greedy(_s)
_exact = (_pred == _to).all(1).float().mean().item()
mo.md(f"Greedy decoding, exact-match on 200 fresh sequences: **{_exact:.1%}**. "
      f"Example: `{_s[0,:-1].tolist()}` → `{_pred[0,:-1].tolist()}`")
```
