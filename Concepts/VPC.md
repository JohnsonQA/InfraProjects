# VPC & CIDR – Quick README (Interview + Concept Notes)

## What is a VPC?

A **VPC (Virtual Private Cloud)** is your **isolated private network in AWS** where you launch resources like EC2, databases, etc.

* You control IP ranges, subnets, routing, and security
* Works like your own **data center inside AWS**

---

## What is CIDR?

**CIDR (Classless Inter-Domain Routing)** defines a **range of IP addresses**

Example:

```
10.0.0.0/16
```

* `10.0.0.0` → starting IP
* `/16` → number of bits used for **network**

### Key Rule:

```
Total IPs = 2^(32 - CIDR)
```

Examples:

* `/16` → 65,536 IPs
* `/24` → 256 IPs

---

## How CIDR Works in VPC

### 1. VPC CIDR

Defines the **total IP space available**

Example:

```
10.0.0.0/16
```

Range:

```
10.0.0.0 → 10.0.255.255
```

---

### 2. Subnets

Subnets are **smaller chunks of the VPC CIDR**

Example:

```
10.0.1.0/24
10.0.2.0/24
```

### Rules:

* Must be inside VPC CIDR
* Must NOT overlap
* Each subnet gets its own IP range

---

## Public vs Private Subnet

A subnet is **not inherently public or private**

It depends on **route table configuration**:

* **Public Subnet**

  * Has route to Internet Gateway (IGW)
  * Instances can have public IP

* **Private Subnet**

  * No direct IGW route
  * Can use NAT Gateway for outbound internet

---

## Why Use `10.0.0.0` for VPC?

Because it belongs to **private IP ranges**, defined by
Internet Assigned Numbers Authority

These are safe for internal use and not exposed to the public internet.

---

## Private IP Ranges (Important)

```
10.0.0.0/8        → Large networks (commonly used in AWS)
172.16.0.0/12     → Medium size
192.168.0.0/16    → Small networks (home/office)
```

---

## Why Not Use Random IP (like 11.0.0.0)?

* It is a **public IP range**
* Owned on the internet
* Can cause:

  * Routing conflicts
  * VPN issues
  * Connectivity problems

---

## AWS Important Points

* Each subnet reserves **5 IPs** (not usable)
* CIDR planning is important (hard to change later)
* Avoid overlap with:

  * Other VPCs
  * VPN networks
  * Local networks

---

## Quick Summary (Interview Ready)

* VPC = private network
* CIDR = IP range definition
* `/N` = network bits, rest host bits
* Subnets = smaller divisions of VPC
* Public/Private = decided by routing
* Use only **private IP ranges** for VPC

---

## Simple Analogy

* VPC = whole land
* Subnets = plots inside land
* CIDR = size of land/plots

---
