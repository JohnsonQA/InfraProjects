```text
                                             ┌────────────────────────────┐
                                             │        INTERNET            │
                                             └─────────────┬──────────────┘
                                                           │
                                                           ▼
                                           ┌────────────────────────────┐
                                           │        Route53 DNS         │
                                           │      infralabx.space       │
                                           └─────────────┬──────────────┘
                                                           │
                                                           ▼
                                           ┌────────────────────────────┐
                                           │      ACM SSL CERTIFICATE   │
                                           │         HTTPS : 443        │
                                           └─────────────┬──────────────┘
                                                           │
                                                           ▼

═══════════════════════════════════════════════════════════════════════════════════════
                               VPC : 10.0.0.0/16
═══════════════════════════════════════════════════════════════════════════════════════


      ┌──────────────────────── us-east-1a ─────────────────────────┐
      │                                                             │
      │   PUBLIC SUBNET : 10.0.1.0/24                               │
      │                                                             │
      │   ┌────────────────────────────────────────────────────┐    │
      │   │            Application Load Balancer               │    │
      │   │                                                    │    │
      │   │   Listener : HTTP  80                              │    │
      │   │   Listener : HTTPS 443                             │    │
      │   │                                                    │    │
      │   │   Security Group :                                 │    │
      │   │      Allow 80 from Internet                        │    │
      │   │      Allow 443 from Internet                       │    │
      │   └───────────────────────┬────────────────────────────┘    │
      │                           │                                 │
      │                           ▼                                 │
      │                ┌──────────────────────┐                     │
      │                │     Target Group     │                     │
      │                │     Port : 8000      │                     │
      │                │ Health : /login      │                     │
      │                └──────────┬───────────┘                     │
      │                           │                                 │
      │───────────────────────────┼─────────────────────────────────│
      │                           │                                 │
      │   PRIVATE SUBNET : 10.0.3.0/24                             │
      │                                                             │
      │   ┌────────────────────────────────────────────────────┐    │
      │   │              Auto Scaling Group                    │    │
      │   │                                                    │    │
      │   │   Desired Capacity : 1                             │    │
      │   │   Minimum Capacity : 1                             │    │
      │   │   Maximum Capacity : 3                             │    │
      │   └───────────────────────┬────────────────────────────┘    │
      │                           │                                 │
      │                           ▼                                 │
      │            ┌─────────────────────────────┐                  │
      │            │      EC2 APP INSTANCE       │                  │
      │            │                             │                  │
      │            │   Amazon Linux 2023         │                  │
      │            │                             │                  │
      │            │   Gunicorn Flask App        │                  │
      │            │   Running on : 8000         │                  │
      │            │                             │                  │
      │            │   Installed using :         │                  │
      │            │   Launch Template +         │                  │
      │            │   user_data                 │                  │
      │            └──────────────┬──────────────┘                  │
      │                           │                                 │
      │                           ▼                                 │
      │                                                             │
      │   RDS SUBNET : 10.0.5.0/24                                 │
      │                                                             │
      │      ┌──────────────────────────────────────┐               │
      │      │       Amazon RDS PostgreSQL          │               │
      │      │                                      │               │
      │      │   DB Name : mydb                     │               │
      │      │   Port    : 5432                     │               │
      │      │                                      │               │
      │      │   Public Access : Disabled           │               │
      │      └──────────────────────────────────────┘               │
      │                                                             │
      └─────────────────────────────────────────────────────────────┘



      ┌──────────────────────── us-east-1b ─────────────────────────┐
      │                                                             │
      │   PUBLIC SUBNET : 10.0.2.0/24                               │
      │                                                             │
      │      ┌──────────────────────────────────────┐               │
      │      │             NAT Gateway              │               │
      │      │                                      │               │
      │      │         Attached Elastic IP          │               │
      │      └──────────────────────────────────────┘               │
      │                                                             │
      │─────────────────────────────────────────────────────────────│
      │                                                             │
      │   PRIVATE SUBNET : 10.0.4.0/24                              │
      │                                                             │
      │      ┌──────────────────────────────────────┐               │
      │      │        Future ASG Instances          │               │
      │      │                                      │               │
      │      │  Auto-created during scaling         │               │
      │      └──────────────────────────────────────┘               │
      │                                                             │
      │─────────────────────────────────────────────────────────────│
      │                                                             │
      │   RDS SUBNET : 10.0.6.0/24                                  │
      │                                                             │
      │      ┌──────────────────────────────────────┐               │
      │      │       Secondary RDS Subnet           │               │
      │      │                                      │               │
      │      │  Used for High Availability          │               │
      │      └──────────────────────────────────────┘               │
      │                                                             │
      └─────────────────────────────────────────────────────────────┘



═══════════════════════════════════════════════════════════════════════════════════════
                            SUPPORTING AWS SERVICES
═══════════════════════════════════════════════════════════════════════════════════════


          ┌────────────────────────────────────────────────────┐
          │                S3 Gateway Endpoint                 │
          │                                                    │
          │  Attached to Private Route Table                   │
          │  Allows private EC2 → S3 access                    │
          │  Without public internet                           │
          └───────────────────────┬────────────────────────────┘
                                  │
                                  ▼
                    ┌──────────────────────────────────┐
                    │          Amazon S3               │
                    │                                  │
                    │   InfraProjects.zip              │
                    │   RDS Backup .dump Files         │
                    └──────────────────────────────────┘



          ┌────────────────────────────────────────────────────┐
          │            Systems Manager (SSM)                   │
          │                                                    │
          │  Used Instead of SSH                               │
          │  Secure EC2 Access Without Bastion                 │
          └────────────────────────────────────────────────────┘
```

---

# 🧠 Architecture Explanation

---

# 🌍 Public Subnets

Used for internet-facing resources:

✅ ALB
✅ NAT Gateway

Reason:

* Must communicate with internet

---

# 🔒 Private Subnets

Used for:

✅ Application EC2 instances

Reason:

* Backend servers should never be public

---

# 🗄️ RDS Subnets

Used only for:

✅ PostgreSQL database

Reason:

* Database must remain isolated and secure

---

# 🚀 Traffic Flow

```text
User
 ↓
Route53
 ↓
ALB (HTTPS)
 ↓
Target Group
 ↓
Private EC2
 ↓
RDS
```

---

# 🔄 Auto Scaling Flow

If traffic increases:

```text
ASG launches new EC2 automatically
        ↓
Launch Template executes
        ↓
user_data installs app
        ↓
ALB health check passes
        ↓
Traffic starts flowing
```

---

# 📦 Backup Flow

```text
RDS
 ↓
pg_dump
 ↓
.dump file
 ↓
S3 Bucket
```

---

# 🔐 Security Design

| Resource | Public? |
| -------- | ------- |
| ALB      | ✅ Yes   |
| EC2      | ❌ No    |
| RDS      | ❌ No    |

---

# 🎯 Final Result

You built:

✅ Production-grade AWS architecture
✅ Highly available infrastructure
✅ Auto-scaling backend
✅ Secure private networking
✅ HTTPS secured application
✅ Managed database
✅ Automated deployment system
✅ Backup solution

```
```
