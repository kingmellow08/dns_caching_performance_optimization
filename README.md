| **Type**          | **Query Direction** | **Purpose**                                    |
| ----------------- | ------------------- | ---------------------------------------------- |
| Inbound Endpoint  | On-prem → AWS       | Resolve **AWS private DNS names** from on-prem |
| Outbound Endpoint | AWS → On-prem       | Resolve **on-prem DNS names** from AWS         |

🧠 Simple Definitions
Inbound Endpoint

Lets your on-prem network query AWS private DNS (like EC2 hostnames or Route 53 private zones).

Outbound Endpoint

Lets your AWS VPC query on-prem DNS (like Active Directory, corp.local, etc.).

🔍 Technical Breakdown
🔸 Inbound Resolver Endpoint
Accepts DNS queries from on-premises → AWS VPC.

Used to resolve:

Private Hosted Zones (e.g., db.internal.mycorp)

EC2 internal DNS (e.g., ip-10-0-1-1.ec2.internal)

Deployed in 1+ VPC subnets with Elastic Network Interfaces (ENIs).

Requires security group to allow UDP/TCP port 53 from on-prem DNS.

🔸 Outbound Resolver Endpoint
Sends DNS queries from AWS VPC → on-prem DNS.

Used to resolve:

Corporate domains (e.g., corp.local, ad.company.com)

Non-public internal DNS names

Needs Route 53 Resolver Rules to forward DNS based on domain.

Deployed in 2 subnets across AZs for high availability.

🔁 Key Differences
Feature	Inbound Endpoint	Outbound Endpoint
Query Source	On-prem DNS → AWS	AWS VPC → External/On-prem DNS
Query Target	AWS private DNS (Route 53, EC2)	External DNS servers (corp, AD)
Uses Resolver Rules?	❌ No	✅ Yes
Security Group Direction	Inbound port 53	Outbound port 53
Common Use Case	On-prem to resolve AWS resources	AWS to resolve on-prem services

✅ When to Use Each
Use Inbound Endpoint if:
You want on-prem systems to resolve:

AWS private hosted zone records

EC2 hostnames like *.ec2.internal

Use Outbound Endpoint if:
You want AWS workloads to resolve:

On-prem internal domains (e.g., corp.local)

Active Directory or legacy apps

🔐 Networking & Security Tips
Security Groups

Inbound Endpoint: Allow UDP/TCP 53 from on-prem.

Outbound Endpoint: Allow outbound 53 to on-prem DNS.

Routing

Use Transit Gateway, Direct Connect, or VPN.

Ensure subnets used by endpoints can reach DNS clients or servers.

Multi-Account VPCs

Can’t share endpoints via VPC Peering — use shared services VPC or Resolver Rules for centralization.

🗺️ DNS Query Flow Diagrams
🔸 Inbound Endpoint
scss
Copy
Edit
[On-Prem DNS Server]
        ↓
Route 53 Inbound Resolver (VPC)
        ↓
AWS DNS (e.g., Route 53 Private Zone)
🔸 Outbound Endpoint
pgsql
Copy
Edit
[AWS VPC EC2 or service]
        ↓
Route 53 Resolver Rule
        ↓
Outbound Resolver → On-Prem DNS
        ↓
Returns Answer to AWS
🛠 Example Use Cases
Use Case	Endpoint Type
On-prem app needs to access RDS by DNS	Inbound
EC2 in AWS needs to resolve corp.local	Outbound
AWS Lambda resolving LDAP or AD DNS	Outbound
Monitoring tool in datacenter querying EC2	Inbound

