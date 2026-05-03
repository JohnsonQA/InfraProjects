# VPC Endpoint vs NAT Gateway — Quick Notes

## NAT Gateway

* Used by **private subnets** to access the internet.
* Traffic flow: Private EC2 → NAT Gateway → Internet Gateway → AWS Service/Internet.
* Required for accessing external services or AWS services **without endpoints**.
* **Costly** (hourly + data processing charges).
* Traffic goes outside VPC (via IGW), but still within AWS-managed network.

---

## VPC Endpoint

* Enables **private access to AWS services** without using NAT or Internet Gateway.
* Traffic stays **inside AWS network (private routing)**.

### Types:

* **Gateway Endpoint**

  * Used for: Amazon S3, Amazon DynamoDB
  * Uses **Route Table**
  * **Free of cost**
  * Best for file/data access

* **Interface Endpoint (PrivateLink)**

  * Used for: AWS Lambda, Amazon RDS, etc.
  * Uses **ENI (private IP inside subnet)**
  * **Paid**
  * Used for API/compute service access

---

## Key Difference

| Feature           | NAT Gateway             | VPC Endpoint                      |
| ----------------- | ----------------------- | --------------------------------- |
| Internet required | Yes                     | No                                |
| Cost              | High                    | Free (Gateway) / Paid (Interface) |
| Traffic path      | Via IGW                 | Inside AWS                        |
| Use case          | General internet access | Private AWS service access        |

---

## One-line summary

* **NAT Gateway** → Access internet from private subnet
* **VPC Endpoint** → Access AWS services privately without internet
