---
date: "2024-10-10T12:49:00.00Z"
published: true
slug: Scaling
tags:
  - system design
  - horizontal scaling
  - vertical scaling
time_to_read: 5
title: Horizontal Scaling vs Vertical Scaling
description: When to do horizontal scaling vs vertical scaling.
type: post
---

The ability to buy a bigger machine or buy more machines is called
scalability

1. buy bigger machine --> Vertical Scaling
2. buy more machines --> Horizontal Scaling

<table>
    <tr>
        <td>Horizontal Scaling</td>
        <td>Vertical Scaling</td>
    </tr>
    <tr>
        <td>Load Balancing Required</td>
        <td>N/A</td>
    </tr>
    <tr>
        <td>Resilient</td>
        <td>Single point of Failure</td>
    </tr>
    <tr>
        <td>Network calls</td>
        <td>Inter process communication</td>
    </tr>
    <tr>
        <td>Data inconsistency</td>
        <td>Consistent</td>
    </tr>
    <tr>
        <td>scales well</td>
        <td>hardware limit</td>
    </tr>
</table>
![Scaling](../public/images/scaling.jpg)
