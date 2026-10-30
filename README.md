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
| `ewb-vpc-a` | VPC A | `10.10.0.0/16` | `vpc-` (TBD) |
| `ewb-a-public` | VPC A / Public | `10.10.1.0/24` | `subnet-` (TBD) |
| `ewb-a-private`| VPC A / Private| `10.10.2.0/24` | `subnet-` (TBD) |
| `ewb-vpc-b` | VPC B | `10.20.0.0/16` | `vpc-` (TBD) |
| `ewb-b-private`| VPC B / Private| `10.20.1.0/24` | `subnet-` (TBD) |
| `ewb-bastion` | `ewb-a-public` | `10.10.1.10` | `i-` (TBD) |
| `ewb-app-a` | `ewb-a-private`| `10.10.2.10` | `i-` (TBD) |
| `ewb-app-b` | `ewb-b-private`| `10.20.1.10` | `i-` (TBD) |
| `ewb-a-b-peer` | Peering | N/A | `pcx-` (TBD) |

*Note: All resources are tagged with `Project = EWB-VPC-Peering` and `Student = Ashish Kumar`.*

---

## 📸 Checkpoints & Verification

### Checkpoint 01: Subnet Associations & Public Default Route
**(Placeholder: Insert Screenshot 01 - showing subnets and IGW route)**

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
