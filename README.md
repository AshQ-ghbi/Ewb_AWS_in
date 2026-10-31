# AWS VPC Peering: Private Workload Connectivity

## 📄 Project Cover
* **Name:** Ashish Kumar
* **Batch:** October 2026
* **Project Title:** AWS VPC Peering
* **Region:** ap-south-1 (Mumbai)
* **Completion Date:** October 2026

---

## 🏗️ Architecture & Resource Inventory

| Resource Name | VPC / Subnet | IPv4 CIDR / Address | Actual AWS ID |
| :--- | :--- | :--- | :--- |
| `ewb-vpc-a` | VPC A | `10.10.0.0/16` | `vpc-0e454b4479b778e07` |
| `ewb-a-public` | VPC A / Public | `10.10.1.0/24` | `subnet-0dc978e9f2fec4ec2` |
| `ewb-a-private` | VPC A / Private | `10.10.2.0/24` | `subnet-0d68aa0abb7aa2a30` |
| `ewb-vpc-b` | VPC B | `10.20.0.0/16` | `vpc-08f4b10c2877ec749` |
| `ewb-b-private` | VPC B / Private | `10.20.1.0/24` | `subnet-0e3207c0ca1471599` |
| `ewb-igw-a` | VPC A Gateway | N/A | `igw-0c75f7abe55a3a5e9` |
| `ewb-rt-a-public` | VPC A Route Table | `0.0.0.0/0` -> IGW | `rtb-0e7f691a481e174bc` |
| `ewb-rt-a-private` | VPC A Route Table | Local only | `rtb-05fb2d5e9ea307649` |
| `ewb-rt-b-private` | VPC B Route Table | Local only | `rtb-034e9bc7e8c98ad2e` |
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
<img src="screenshots/Screenshot 2026-10-31 102103.png" alt="Checkpoint 01A - Public Route Table Routes" width="100%" />

*Figure 1.1: Route tables overview with `ewb-rt-a-public` selected, showing default route `0.0.0.0/0` targeting `ewb-igw-a`.*

<img src="screenshots/Screenshot 2026-10-31 102245.png" alt="Checkpoint 01B - Public Subnet Association" width="100%" />

*Figure 1.2: `ewb-rt-a-public` explicit subnet association to `ewb-a-public` (`10.10.1.0/24`).*

<img src="screenshots/Screenshot 2026-10-31 102445.png" alt="Checkpoint 01C - Private Subnet Association" width="100%" />

*Figure 1.3: `ewb-rt-a-private` explicit subnet association to `ewb-a-private` (`10.10.2.0/24`).*

<img src="screenshots/Screenshot 2026-10-31 102403.png" alt="Checkpoint 01D - Private Route Local Only" width="100%" />

*Figure 1.4: `ewb-rt-a-private` route list confirming strictly local route (`10.10.0.0/16`) without internet access.*

<details>
<summary>🔍 Click to view intermediate creation & association proofs</summary>

#### VPC A Creation
<img src="screenshots/Screenshot 2026-10-31 002102.png" alt="VPC A Details" width="100%" />

#### VPC B Creation
<img src="screenshots/Screenshot 2026-10-31 002617.png" alt="VPC B Details" width="100%" />

#### VPC A Subnets Creation
<img src="screenshots/Screenshot 2026-10-31 003917.png" alt="VPC A Subnets" width="100%" />

#### VPC B Subnet Creation
<img src="screenshots/Screenshot 2026-10-31 004524.png" alt="VPC B Subnet" width="100%" />

#### Internet Gateway Creation
<img src="screenshots/Screenshot 2026-10-31 004748.png" alt="IGW Creation" width="100%" />

#### Internet Gateway Attachment
<img src="screenshots/Screenshot 2026-10-31 004859.png" alt="IGW Attached" width="100%" />

#### Public Route Table Creation
<img src="screenshots/Screenshot 2026-10-31 005447.png" alt="Public Route Table Initial" width="100%" />

#### Public Route Table Subnet Association
<img src="screenshots/Screenshot 2026-10-31 005556.png" alt="Public Route Table Association" width="100%" />

#### Public Route Table Update Success
<img src="screenshots/Screenshot 2026-10-31 005722.png" alt="Public Route Update" width="100%" />

#### Private Route Table A Creation
<img src="screenshots/Screenshot 2026-10-31 005831.png" alt="Private RT A Created" width="100%" />

#### Private Route Table A Association Success
<img src="screenshots/Screenshot 2026-10-31 005912.png" alt="Private RT A Associated" width="100%" />

#### Private Route Table B Creation
<img src="screenshots/Screenshot 2026-10-31 010101.png" alt="Private RT B Created" width="100%" />

#### Private Route Table B Association Success
<img src="screenshots/Screenshot 2026-10-31 101737.png" alt="Private RT B Associated" width="100%" />

</details>

---

### Checkpoint 02: EC2 Instances (Public vs Private IPs)
*(Pending)*

---

### Checkpoint 03: Active Peering Connection
*(Pending)*

---

### Checkpoint 04: Route Tables (Peering Routes)
*(Pending)*

---

## 🧪 Testing Evidence

### Test 01: Baseline Failure (Pre-Peering)
*(Pending)*

### Test 02 & 03: Bi-Directional Ping Success (Post-Peering)
*(Pending)*

**Routing Explanation:**
* **Forward Path:** Traffic from `ewb-app-a` targeting `10.20.0.0/16` matches the `ewb-rt-a-private` route pointing to the peering connection (`pcx-`), crossing the peering link into VPC B.
* **Return Path:** The reply from `ewb-app-b` targeting `10.10.0.0/16` matches the `ewb-rt-b-private` route pointing back to the same peering connection (`pcx-`), ensuring return packets reach VPC A.

### Test 04: Private SSH Access (ProxyJump)
*(Pending)*

### Test 05: Controlled Failure & Recovery
*(Pending)*

**Controlled Failure Explanation:**
When the inbound ICMP Echo request rule is removed from `ewb-sg-app-b`, the pings from `ewb-app-a` time out. Because AWS security groups are stateful and deny traffic by default, explicit inbound rules must exist for the specific source IP (`10.10.2.10/32`). Re-adding the rule restores connectivity.

---

## 🧹 Cleanup & Conclusion

*(Pending)*

**What I Learned:**
*(To be completed at the end of the lab)*.
