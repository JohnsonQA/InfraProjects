# NAT Gateway (NGW) — Public vs Private + Use Cases

## What is NAT Gateway

* A managed AWS service that allows **instances in private subnets** to access external networks.
* Works only for **outbound traffic** (no inbound connections allowed).

---

## Public NAT Gateway (what you usually use)

### Definition

* A NAT Gateway created in a **public subnet** with an **Elastic IP (EIP)** attached.
* Routes traffic from private subnet → internet via Internet Gateway.

### Flow

Private EC2 → NAT Gateway → Internet Gateway → Internet

### Use Cases

* Access internet from private instances (yum update, apt install, etc.)
* Download packages, updates
* Call external APIs (payment gateways, third-party services)
* Access AWS services **without VPC endpoint**

### Notes

* Requires:

  * Public subnet
  * Internet Gateway
  * Elastic IP
* **Costs money** (hourly + data)

---

## Private NAT Gateway (less commonly discussed)

### Definition

* A NAT Gateway **without direct internet exposure**
* Used for **private-to-private communication** (no Internet Gateway involved)

### Flow

Private EC2 → NAT Gateway → Another VPC / On-prem (via Transit Gateway / VPN / Direct Connect)

### Use Cases

* When you want instances to:

  * Access **other private networks**
  * Reach services in another VPC
  * Connect to on-prem systems securely
* Used in **advanced architectures** (multi-VPC, hybrid cloud)

---

## Key Difference

| Feature          | Public NAT Gateway | Private NAT Gateway     |
| ---------------- | ------------------ | ----------------------- |
| Internet access  | ✅ Yes              | ❌ No                    |
| Elastic IP       | ✅ Required         | ❌ Not required          |
| Internet Gateway | ✅ Required         | ❌ Not used              |
| Use case         | Internet access    | Private network routing |
| Common usage     | Very common        | Advanced setups         |

---

## Interview one-liner

* “Public NAT Gateway is used to access the internet from private subnets, while Private NAT Gateway is used for routing traffic between private networks without internet exposure.”

---

## Real-world tip

* In most real projects → you’ll use **Public NAT Gateway + VPC Endpoints**
* Private NAT Gateway is used in **complex enterprise architectures**
