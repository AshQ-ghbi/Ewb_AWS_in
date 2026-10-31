# Ewb AWS Internship - Project

## AWS VPC Peering: Private Workload Connectivity

## 📄 Project Cover
- **Name:** Ashish Kumar
- **College:** Jai Narain College of Technology, Bhopal
- **Batch:** October 2026
- **Project Title:** AWS VPC Peering
- **Region:** ap-south-1 (Mumbai)
- **Completion Date:** 28 October, 2026
- **Mail:** kr.ashish535.hd@gmail.com

---

## 📐 Architecture Diagram

```text
+----------------------------------------------------------------------------------------------------+
| ap-south-1 (Mumbai)                                                                                |
|                                                                                                    |
|  +-------------------------------------------------+     +---------------------------------------+ |
|  | VPC A (10.10.0.0/16)                            |     | VPC B (10.20.0.0/16)                  | |
|  |                                                 |     |                                       | |
|  |  +----------------------+                       |     |                                       | |
|  |  | Public Subnet        |                       |     |                                       | |
|  |  | 10.10.1.0/24         |                       |     |                                       | |
|  |  |                      |                       |     |                                       | |
|  |  | [ ewb-bastion ]      |                       |     |                                       | |
|  |  | 10.10.1.10 (Public)  |                       |     |                                       | |
|  |  +----------+-----------+                       |     |                                       | |
|  |             |                                   |     |                                       | |
|  |  +----------v-----------+    VPC Peering        |     |  +---------------------------------+  | |
|  |  | Private Subnet A     |    Connection         |     |  | Private Subnet B                |  | |
|  |  | 10.10.2.0/24         | (pcx-0d42ebe35f2e2ad10)     |  | 10.20.1.0/24                    |  | |
|  |  |                      |                       |     |  |                                 |  | |
|  |  | [ ewb-app-a ]        |<=====================>|<===>|  | [ ewb-app-b ]                   |  | |
|  |  | 10.10.2.10 (Private) |   Bi-Directional ICMP |     |  | 10.20.1.10 (Private)            |  | |
|  |  +----------------------+   & ProxyJump SSH     |     |  +---------------------------------+  | |
|  +-------------------------------------------------+     +---------------------------------------+ |
+----------------------------------------------------------------------------------------------------+
```
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
| `ewb-sg-bastion` | VPC A | TCP 22 from Admin workstation | `sg-097cc93cf9549c0b4` |
| `ewb-sg-app-a` | VPC A | TCP 22 from Bastion, ICMP from App-B | `sg-042039d8c190f26e7` |
| `ewb-sg-app-b` | VPC B | TCP 22 from Bastion, ICMP from App-A | `sg-0f87b021d94ea64be` |
| `ewb-lab` | Key Pair | RSA | `key-09d05457d3d40574c` |
| `ewb-bastion` | `ewb-a-public` | `10.10.1.10` / `65.2.152.151` | `i-0fd7f5f80df75d803` |
| `ewb-app-a` | `ewb-a-private` | `10.10.2.10` / No Public IP | `i-0cacc5d4eefc7935d` |
| `ewb-app-b` | `ewb-b-private` | `10.20.1.10` / No Public IP | `i-0dbb102baba19380e` |
| `ewb-a-b-peer` | Peering Connection | N/A | `pcx-0d42ebe35f2e2ad10` |

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

## 👇

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

## 🛡️ Security Group Configuration

Least-privilege inbound firewall rules applied to isolate workloads:

<img src="screenshots/Screenshot 2026-10-31 111431.png" alt="Bastion Security Group" width="100%" />

*Figure 2.1: `ewb-sg-bastion` inbound rule allowing SSH (TCP 22) exclusively from workstation public IP (`106.222.212.35/32`).*

<img src="screenshots/Screenshot 2026-10-31 112110.png" alt="App A Security Group" width="100%" />

*Figure 2.2: `ewb-sg-app-a` inbound rules allowing SSH (TCP 22) from Bastion (`10.10.1.10/32`) and ICMP Echo Request from App-B (`10.20.1.10/32`).*

<img src="screenshots/Screenshot 2026-10-31 113028.png" alt="App B Security Group" width="100%" />

*Figure 2.3: `ewb-sg-app-b` inbound rules allowing SSH (TCP 22) from Bastion (`10.10.1.10/32`) and ICMP Echo Request from App-A (`10.10.2.10/32`).*



### Checkpoint 02: EC2 Instances (Public vs Private Workload Isolation)
* **Scope:** Verification of compute instance provisioning and IP exposure[cite: 7, 11].
* **Verification:** `ewb-bastion` provisioned in public subnet with public IP `65.2.152.151`; `ewb-app-a` and `ewb-app-b` deployed strictly private with no public IPv4 addresses assigned[cite: 4, 7, 11, 52].

<img src="screenshots/Screenshot 2026-10-31 123411.png" alt="Checkpoint 02 - EC2 Instances Inventory and Isolation" width="100%" />

*Figure 2.4: Running EC2 instances showing 3/3 status checks passed, valid private IPv4 assignments, and public IP isolation on private application hosts[cite: 7, 11, 52].*

## 👇

<details>
<summary>🔍 Click to view Key Pair setup proof</summary>

#### EC2 Key Pair Creation
<img src="screenshots/Screenshot 2026-10-31 114203.png" alt="EC2 Key Pair" width="100%" />

</details>
---

### Checkpoint 03: Active Peering Connection
* **Scope:** Verification of inter-VPC peering status between VPC A and VPC B[cite: 9, 28].
* **Verification:** `ewb-a-b-peer` accepted and transitioned to `Active` status linking requester `ewb-vpc-a` to accepter `ewb-vpc-b`[cite: 9, 28].

<img src="screenshots/Screenshot 2026-10-31 132356.png" alt="Checkpoint 03 - Active VPC Peering Connection" width="100%" />

*Figure 3.1: Active VPC peering connection (`ewb-a-b-peer`) establishing private cross-VPC network connectivity[cite: 4, 9].*

---

### Checkpoint 04: Route Tables (Peering Routes)
* **Scope:** Cross-VPC route propagation targeting the active peering connection.
* **Verification:** `ewb-rt-a-private` routes `10.20.0.0/16` to `pcx-0d42ebe35f2e2ad10`; `ewb-rt-b-private` routes `10.10.0.0/16` to `pcx-0d42ebe35f2e2ad10`.

<img src="screenshots/Screenshot 2026-10-31 133331.png" alt="Checkpoint 04A - ewb-rt-a-private Peering Route" width="100%" />

*Figure 4.1: `ewb-rt-a-private` updated with active destination `10.20.0.0/16` targeting peering connection `pcx-0d42ebe35f2e2ad10`.*

<img src="screenshots/Screenshot 2026-10-31 134702.png" alt="Checkpoint 04B - ewb-rt-b-private Peering Route" width="100%" />

*Figure 4.2: `ewb-rt-b-private` updated with active destination `10.10.0.0/16` targeting peering connection `pcx-0d42ebe35f2e2ad10`.*

---

## 🧪 Testing Evidence

### Test 01: Baseline Failure (Pre-Peering Isolation)
* **Scope:** Proving private subnets across unpeered VPCs cannot communicate by default.
* **Result:** `ewb-app-a` (`10.10.2.10`) targeting `ewb-app-b` (`10.20.1.10`) results in `100% packet loss`.

<img src="screenshots/Screenshot 2026-10-31 130830.png" alt="Test 01 - Pre-Peering Baseline Failure" width="100%" />

*Figure 3.1: Terminal output from `ewb-app-a` executing `ping -c 4 10.20.1.10`, demonstrating complete packet loss prior to VPC peering.*

---

### Test 02: Forward Ping (Post-Peering Verification)
* **Scope:** Validating forward ICMP packet delivery across peering connection.
* **Result:** `ewb-app-a` (`10.10.2.10`) pinging `ewb-app-b` (`10.20.1.10`) achieves `0% packet loss` with average latency of 0.344 ms.

<img src="screenshots/Screenshot 2026-10-31 135928.png" alt="Test 02 - Forward Ping Success" width="100%" />

*Figure 5.2: Terminal output from `ewb-app-a` pinging `10.20.1.10`, confirming successful routing via peering connection `pcx-0d42ebe35f2e2ad10`.*

---

### Test 03: Reverse Ping (Post-Peering Verification)
* **Scope:** Validating reverse ICMP packet delivery and symmetric routing.
* **Result:** `ewb-app-b` (`10.20.1.10`) pinging `ewb-app-a` (`10.10.2.10`) achieves `0% packet loss` with average latency of 0.217 ms.

<img src="screenshots/Screenshot 2026-10-31 141126.png" alt="Test 03 - Reverse Ping Success" width="100%" />

*Figure 5.3: Terminal output from `ewb-app-b` pinging `10.10.2.10`, confirming symmetric bi-directional route propagation.*

---

### Test 04: Private SSH Access (ProxyJump)
* **Scope:** End-to-end administrative reachability into completely private workloads without public IPv4 assignment.
* **Verification:** Local OpenSSH client established jump access through `ewb-bastion` (`65.2.152.151`) into `ewb-app-b` (`10.20.1.10`) on private port 22, verified in Figure 5.3 terminal header.

---

### Test 05: Controlled Failure & Recovery
* **Scope:** Validating security group stateful inspection and enforcement across peering connections.
* **Failure Execution:** Inbound ICMP Echo Request rule removed from `ewb-sg-app-b`. Ping from `ewb-app-a` drops to `100% packet loss` despite active peering connection and route tables.
* **Recovery Execution:** Inbound ICMP Echo Request rule re-added (`10.10.2.10/32` allowed). Connectivity is restored with `0% packet loss`.

#### Controlled Failure Evidence (ICMP Inbound Rule Dropped)
<img src="screenshots/Screenshot 2026-10-31 142558.png" alt="Test 05 - Controlled Failure" width="100%" />

*Figure 5.4: Terminal output from `ewb-app-a` showing 100% packet loss after removing ICMP inbound rule from `ewb-sg-app-b`.*

#### Controlled Recovery Evidence (ICMP Inbound Rule Restored)
<img src="screenshots/Screenshot 2026-10-31 143026.png" alt="Test 05 - Controlled Recovery" width="100%" />

*Figure 5.5: Terminal output from `ewb-app-a` showing immediate restoration to 0% packet loss after re-adding the ICMP inbound rule.*

---

## 🧹 Cleanup & Teardown

To ensure complete resource lifecycle management and eliminate cloud costs, all lab infrastructure was terminated in strict reverse dependency order:

<img src="screenshots/Screenshot 2026-10-31 143613.png" alt="EC2 Instances Terminated" width="100%" />

*Figure 6.1: Termination of all EC2 compute workloads (`ewb-bastion`, `ewb-app-a`, `ewb-app-b`).*

<img src="screenshots/Screenshot 2026-10-31 143759.png" alt="Peering Connection Deleted" width="100%" />

*Figure 6.2: Peering connection `pcx-0d42ebe35f2e2ad10` deleted along with its 3 active routes.*

<img src="screenshots/Screenshot 2026-10-31 144116.png" alt="VPC B Deleted" width="100%" />

*Figure 6.3: Deletion of `ewb-vpc-b` along with associated security group, subnet, and private route table.*

<img src="screenshots/Screenshot 2026-10-31 144420.png" alt="Subnets Cleared" width="100%" />

*Figure 6.4: VPC subnets completely de-provisioned.*

---

## Viva & Concept Questions
Q1: Why must CIDR blocks between peered VPCs be strictly non-overlapping?

Answer: IP routing relies on longest-prefix and unambiguous route lookups. If VPC A and VPC B shared identical or overlapping CIDR blocks (e.g., both using 10.10.0.0/16), a host cannot determine whether destination packets belong to the local network or across the peering link. AWS VPC Peering prevents creating peering connections between VPCs with overlapping CIDRs at the API level to maintain routing determinism.

Q2: What is the difference between Security Groups and Network ACLs in the context of VPC Peering?

Answer:
Security Groups operate at the virtual network interface (ENI) / instance level and are stateful. If inbound traffic (like ICMP Echo or SSH) is permitted, return traffic is automatically allowed regardless of outbound rules. Security groups can reference IPs across peering links.

Network ACLs (NACLs) operate at the subnet boundary level and are stateless. Both inbound and outbound rules must explicitly permit the traffic and ephemeral return ports (1024-65535).

Q3: Why is VPC Peering considered non-transitive? Give a real-world scenario.

Answer: Non-transitive routing means traffic cannot hop through an intermediate VPC to reach a third destination. If VPC A is peered with VPC B, and VPC B is peered with VPC C:

VPC A cannot communicate with VPC C through VPC B.

Similarly, private workloads in VPC B cannot route out to the public internet through an Internet Gateway attached to VPC A via peering.
For transitive hub-and-spoke multi-VPC designs, AWS Transit Gateway is required instead of VPC Peering.

Q4: How does SSH ProxyJump work, and why is it preferred over traditional SSH Agent Forwarding?

Answer: ProxyJump (-J or ProxyJump in ~/.ssh/config) establishes an encrypted SSH tunnel to the target host via the intermediate bastion host using standard port forwarding (stdin/stdout).
It is strongly preferred because:

Zero Key Exposure: The private key never leaves the client machine, nor is it stored or decrypted on the intermediate bastion server.

Attack Surface Reduction: Even if the bastion host is compromised, attackers cannot access forwarded SSH agent sockets or decrypt active key sessions.



## 💡 What I Learned

1. **Non-Overlapping CIDRs Are Mandatory:** AWS VPC Peering requires distinct IPv4 CIDRs (`10.10.0.0/16` vs `10.20.0.0/16`) to establish route table associations without IP collisions.
2. **Peering Connections Are Non-Transitive:** A peering link between VPC A and VPC B does not grant transitive access to external internet gateways or third-party VPCs.
3. **Dual Layer Requirements (Routing + Firewall):** A peering connection alone does not forward traffic. Both explicit destination CIDR entries in custom Route Tables and stateful Security Group rules must align.
4. **Bastion Host ProxyJump Pattern:** Secure cloud operations isolate backend databases and compute nodes entirely without public IPv4 addresses. OpenSSH `ProxyJump` allows secure zero-footprint jump tunneling without storing private keys on intermediate bastion servers.
