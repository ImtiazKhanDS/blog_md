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

| Horizontal Scaling      | Vertical Scaling            |
| ----------------------- | --------------------------- |
| Load Balancing Required | N/A                         |
| Resilient               | Single point of Failure     |
| Network calls           | Inter process communication |
| Data inconsistency      | Consistent                  |
| scales well             | hardware limit              |

![Scaling](../public/images/scaling.jpg)
