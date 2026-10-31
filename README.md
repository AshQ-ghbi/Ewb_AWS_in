# AWS VPC Peering: Private Workload Connectivity

## 📄 Project Cover
* **Name:** Ashish Kumar
* **Batch:** October 2026
* **Project Title:** AWS VPC Peering
* **Region:** ap-south-1 (Mumbai)
* **Completion Date:** October 2026

---

## 🏗️ Architecture & Resource Inventory

**(Placeholder: Insert Architecture Diagram Here)**

| Resource Name | VPC / Subnet | IPv4 CIDR / Address | Actual AWS ID |
| :--- | :--- | :--- | :--- |
| `ewb-vpc-a` | VPC A | `10.10.0.0/16` | `vpc-0e454b4479b778e07` |
| `ewb-a-public` | VPC A / Public | `10.10.1.0/24` | `subnet-0dc978e9f2fec4ec2` |
| `ewb-a-private` | VPC A / Private | `10.10.2.0/24` | `subnet-0d68aa0abb7aa2a30` |
| `ewb-vpc-b` | VPC B | `10.20.0.0/16` | `vpc-08f4b10c2877ec749` |
| `ewb-b-private` | VPC B / Private | `10.20.1.0/24` | `subnet-0e3207c0ca1471599` |
| `ewb-igw-a` | VPC A Gateway | N/A | `igw-0c75f7abe55a3a5e9` |
| `ewb-rt-a-public` | VPC A Route Table | `0.0.0.0/0` -> IGW | `rtb-0e7f691a481e174bc` |
| `ewb-rt-a-private`| VPC A Route Table | Local only | `rtb-05fb2d5e9ea307649` |
| `ewb-rt-b-private`| VPC B Route Table | Local only | `rtb-034e9bc7e8c98ad2e` |
| `ewb-bastion` | `ewb-a-public` | `10.10.1.10` | *(Pending launch)* |
| `ewb-app-a` | `ewb-a-private` | `10.10.2.10` | *(Pending launch)* |
| `ewb-app-b` | `ewb-b-private` | `10.20.1.10` | *(Pending launch)* |
| `ewb-a-b-peer` | Peering Connection | N/A | *(Pending creation)* |

*Note: All resources are tagged with `Project = EWB-VPC-Peering` and `Student = Ashish Kumar`.*

---
## 📸 Checkpoints & Verification

### Checkpoint 01: Subnet Associations & Public Default Route
* **Scope:** Network Foundation & Custom Route Tables for VPC A & VPC B.
* **Verification:** `ewb-a-public` explicitly associated with `ewb-rt-a-public` containing default route `0.0.0.0/0` targeting `ewb-igw-a`; `ewb-a-private` and `ewb-b-private` explicitly associated with their respective private route tables containing only local routes.

#### Primary Evidence
![Checkpoint 01A - Public Route Table Routes](screenshots/Screenshot 2026-10-31 102103.png)
*Figure 1.1: Route tables overview with `ewb-rt-a-public` selected, showing default route `0.0.0.0/0` targeting `ewb-igw-a`.*

![Checkpoint 01B - Public Subnet Association](screenshots/Screenshot 2026-10-31 102245.png)
*Figure 1.2: `ewb-rt-a-public` explicit subnet association to `ewb-a-public` (`10.10.1.0/24`).*

![Checkpoint 01C - Private Subnet Association](screenshots/Screenshot 2026-10-31 102445_2.png)
*Figure 1.3: `ewb-rt-a-private` explicit subnet association to `ewb-a-private` (`10.10.2.0/24`).*

![Checkpoint 01D - Private Route Local Only](screenshots/Screenshot 2026-10-31 102403.png)
*Figure 1.4: `ewb-rt-a-private` route list confirming strictly local route (`10.10.0.0/16`) without internet access.*

<details>
<summary>🔍 Click to view intermediate creation & association proofs</summary>

#### VPC A Creation
![VPC A Details](screenshots/Screenshot 2026-10-31 002102_2.jpg)

#### VPC B Creation
![VPC B Details](screenshots/Screenshot 2026-10-31 002617_2.jpg)

#### VPC A Subnets Creation
![VPC A Subnets](screenshots/Screenshot 2026-10-31 003917_2.png)

#### VPC B Subnet Creation
![VPC B Subnet](screenshots/Screenshot 2026-10-31 004524_2.png)

#### Internet Gateway Creation
![IGW Creation](screenshots/Screenshot 2026-10-31 004748_2.png)

#### Internet Gateway Attachment
![IGW Attached](screenshots/Screenshot 2026-10-31 004859_2.png)

#### Public Route Table Creation
![Public Route Table Initial](screenshots/Screenshot 2026-10-31 005447_2.png)

#### Public Route Table Subnet Association
![Public Route Table Association](screenshots/Screenshot 2026-10-31 005556_2.png)

#### Public Route Table Update Success
![Public Route Update](screenshots/Screenshot 2026-10-31 005722_2.png)

#### Private Route Table A Creation
![Private RT A Created](screenshots/Screenshot 2026-10-31 005831.png)

#### Private Route Table A Association Success
![Private RT A Associated](screenshots/Screenshot 2026-10-31 005912.png)

#### Private Route Table B Creation
![Private RT B Created](screenshots/Screenshot 2026-10-31 010101.png)

#### Private Route Table B Association Success
![Private RT B Associated](screenshots/Screenshot 2026-10-31 101737.png)

</details>

---

### Checkpoint 02: EC2 Instances (Public vs Private IPs)
**(Placeholder: Insert Screenshot 02 - showing App-A and App-B have no public IP)**

### Checkpoint 03: Active Peering Connection
**(Placeholder: Insert Screenshot 03 - showing pcx- Active status and VPC IDs)**

### Checkpoint 04: Route Tables (Peering Routes)
**(Placeholder: Insert Screenshot 04 - showing the three route tables with pcx- routes)**

---

## 🧪 Testing Evidence

### Test 01: Baseline Failure (Pre-Peering)
**(Placeholder: Insert Screenshot 05 - Ping 100% packet loss)**

### Test 02 & 03: Bi-Directional Ping Success (Post-Peering)
**(Placeholder: Insert Screenshot 06 - 0% packet loss forward & reverse)**

**Routing Explanation:**
*   **Forward Path:** Traffic from `ewb-app-a` targeting `10.20.0.0/16` matches the `ewb-rt-a-private` route pointing to `pcx-...`, crossing the peering connection into VPC B.
*   **Return Path:** The reply from `ewb-app-b` targeting `10.10.0.0/16` matches the `ewb-rt-b-private` route pointing to the same `pcx-...`, ensuring stateful ICMP/SSH traffic can complete the round trip.

### Test 04: Private SSH Access (ProxyJump)
**(Placeholder: Insert Screenshot 07 - SSH connection to App B via Bastion)**

### Test 05: Controlled Failure & Recovery
**(Placeholder: Insert Screenshot 08 - Ping failure after rule removal, and recovery)**

**Controlled Failure Explanation:**
When the inbound ICMP Echo request rule is removed from `ewb-sg-app-b`, the pings from `ewb-app-a` time out. Because AWS security groups are stateful and default-deny, explicit inbound allowances must exist for the exact source IP (`10.10.2.10/32`). Restoring the rule immediately restores connectivity.

---

## 🧹 Cleanup & Conclusion

**(Placeholder: Insert Screenshot 09 - Resources terminated/deleted)**

**What I Learned:**
(To be completed at the end of the lab).
