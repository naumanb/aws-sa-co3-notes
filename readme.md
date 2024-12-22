# 1. SAA-C03 Notes (Dec 2024)

## 1.1 AWS Fundamentals

### 1.1.1 Public vs Private Services
- **Public Internet**: Zone for internet-based services (e.g., email, online games, streaming).
- **Private Internet**: Isolated VPCs accessible only via VPN or Direct Connect. Public internet access requires an Internet Gateway.
  - Example: On-premises systems accessing AWS resources via Direct Connect can still use Internet Gateway for public AWS services like S3.
- **AWS Public Zone**: Hosts public services like S3 but requires explicit permissions for access.

### 1.1.2 AWS Global Infrastructure
- **Regions**:
  - Fully deployed AWS infrastructure in areas like Ohio, Singapore, and London.
  - Key benefits: fault tolerance, compliance, performance optimization.
- **Edge Locations**:
  - Local distribution points for low-latency services (e.g., Netflix streaming from Brisbane via Sydney Edge).
- **Management and Resilience**:
  - Regions connect via high-speed networks.
  - **Global Services** (e.g., IAM) replicate across regions.
  - **Regional Services** (e.g., EC2) replicate across AZs but fail if the entire region is down.
  - **AZ-Specific Services** operate in a single AZ; fail if the AZ is down.

### 1.1.3 AWS Default VPC
- Default VPC per region with CIDR `172.31.0.0/16`. Default subnets provide public IPv4 addresses.
- Use custom VPCs for production due to greater flexibility.
  - Example: Default VPC's `/16` range can host multiple smaller `/20` subnets.

### 1.1.4 EC2 Basics
- **IaaS**: Provides virtual machines (instances).
  - Example: Web servers with specific CPU and memory.
- **Billing**: On-demand, based on CPU, memory, storage, and networking.
- **Instance States**:
  - **Running**: Uses CPU, memory, and networking.
  - **Stopped**: Retains data, no active resources.
  - **Terminated**: Data deleted, no charges.
- **AMI**: Snapshot for creating instances.
  - Region-specific; includes permissions, boot volume, and device mappings.

### 1.1.5 S3 Basics
- **Global Object Storage**: Resilient, accessible globally, region-specific data.
- **Objects**:
  - Key (name), Value (data), Metadata, Access Control.
  - File size: 0 bytes to 5 TB.
- **Buckets**:
  - Names must be globally unique.
  - Data resides in a single region unless explicitly moved.
  - Soft limit: 100 buckets/account; hard limit: 1000.

### 1.1.6 CloudFormation Basics
- Automates infrastructure using templates (YAML/JSON).
- Templates create/update stacks (living representations of resources).
  - Example: Deploying multi-tier web apps with a single template.

### 1.1.7 CloudWatch Basics
- **Monitoring**: Tracks metrics (e.g., CPU usage, disk I/O), logs, and events.
  - **Alarms**: Trigger actions like sending SNS notifications.
- **Dimensions**: Name-value pairs to separate data sources within a metric.

### 1.1.8 Shared Responsibility Model
- **AWS**: Responsible for cloud security (e.g., hardware, global infrastructure).
- **Customer**: Responsible for in-cloud security (e.g., data encryption, firewall configuration).

### 1.1.9 High Availability, Fault Tolerance, and Disaster Recovery
- **High Availability (HA)**: Ensures uptime (e.g., 99.9% = ~8.8 hours/year downtime).
- **Fault Tolerance (FT)**: Operates despite failures with redundancy.
- **Disaster Recovery (DR)**: Prepares for major failures with recovery plans.

### 1.1.10 Route53 Fundamentals
- **DNS as a Service**: Manages domain registration and zones (public/private).
  - Example: Hosting DNS records for a website.
- Globally resilient with fault tolerance across regions.

### 1.1.11 DNS Records
- **A/AAAA**: Maps host to IPv4/IPv6 addresses.
- **CNAME**: DNS alias for other records.
- **MX**: Mail server records with priority settings.
- **TXT**: Arbitrary text, often for domain verification.
- **TTL**: Specifies cache duration for DNS records.

### 1.1.12 DNS Root
- Managed by IANA; root servers guide DNS queries.
- **Root Hints**: Point to root servers for DNS resolution.

### 1.1.13 DNS Hierarchy
- Root zone delegates authority to TLDs (e.g., `.com`, `.org`).
- **Registry** manages zones for TLDs.
- **Registrar** facilitates domain registration under TLDs.

## 1.2 IAM, Accounts, and AWS Organizations

### 1.2.1 IAM Identity Policies
- **Policy Components**:
  - **Effect**: Allow/Deny.
  - **Actions**: Operations like `s3:GetObject`.
  - **Resources**: Targeted via ARNs.
  - **Statement ID (SID)**: Optional description.
- Policy priority: Explicit Deny > Explicit Allow > Implicit Deny.

### 1.2.2 IAM Users and ARNs
- **IAM Users**: Provide long-term AWS access for individuals or apps.
  - Authentication via username/password or access keys.
- **ARNs**: Uniquely identify AWS resources.
  - Example: `arn:aws:s3:::mybucket/*` targets objects in a bucket.

### 1.2.3 IAM Groups
- Manage multiple users with shared permissions.
  - Example: DevOps team group with read/write S3 permissions.
- Benefits: Simplifies updates, centralized management.
- Limitations: Cannot reference groups in resource policies.

### 1.2.4 IAM Roles
- Provide temporary credentials for specific tasks.
  - Example: Granting developers temporary S3 access via an assumed role.
- **Trust Policy**: Who can assume the role.
- **Permission Policy**: Defines role actions.
- Temporary credentials expire and require reassumption.

### 1.2.5 When to Use IAM Roles
- **Lambda Execution Role**:
  - Use for functions with unknown number of principals.
  - Trust Policy: Trust Lambda Service.
  - Permission Policy: Grant AWS service access.
  - Uses `sts:AssumeRole` for temporary keys (e.g., CloudWatch, S3).
  - Prefer roles over direct policy attachment.

- **Emergency Situations (Break Glass)**:
  - Role for exceptional access needs.
  - Requires justification to "break the glass."
  - Grants expanded access when necessary.

- **Integrating AWS with Corporate Environment**:
  - Use IAM roles for Single Sign-On (SSO) with identity providers (e.g., Active Directory).
  - ID Federation allows external services to assume AWS roles.

- **Web Identity Federation**:
  - IAM Roles integrate with web identities (e.g., Google, Facebook, Twitter).
  - No AWS credentials stored in the app.
  - Scales for millions of users.
  - Grants trusted web identities access to AWS resources (e.g., DynamoDB).

- **Cross-Account Access**:
  - IAM Roles allow partner accounts to access or upload to AWS resources.

### 1.2.6 Service-linked Roles & Passrole
- **Service-linked Roles**:
  - IAM role that is linked to a specific AWS service which can be created/deleted by it (policy)
  - Provides permissions for service to interact with other services

- **Passrole**:
  - Allows users to pass an existing role to an AWS service

### 1.2.7. AWS Organizations
#### Overview
AWS Organizations centralize account management for multi-account environments. Without it, each account requires individual IAM users and payment methods. Useful for organizations managing 5+ accounts.

#### Key Features:
- **Management/Master Account**: Primary account that creates and manages the organization.
- **Member Accounts**: Accounts that join the organization and delegate management to the master account.
- **Organization Root**: Container holding master and member accounts or organizational units (OUs).
- **Consolidated Billing**: Combines billing from member accounts into a single bill for the master account. Allows cost optimization via reservations and volume discounts.
- **Role Switching**: Enables switching between accounts using CLI or console.

#### Creating Accounts in an Organization:
- Only an email address is required.
- IAM users in individual accounts can be replaced with IAM roles for centralized access.

---

### 1.2.8. Service Control Policies (SCPs)

#### Overview
SCPs restrict actions that member accounts can perform. They act as a "guardrail," not granting permissions but limiting what IAM policies can allow. Master accounts cannot be restricted by SCPs (security risk).

- **Only Overlaps Allowed**: Only allow rules in both the SCP **and** the IAM policy are granted. If a rule is missing in one or the other, the action is denied.

#### Allow List vs Deny List:
1. **Deny List** (Default):
   - AWS applies `FullAWSAccess` SCP initially, allowing all actions.
   - Example: To deny all S3 access:
     ```json
     {
       "Effect": "Deny",
       "Action": "s3:*",
       "Resource": "*"
     }
     ```
   - Minimal administrative overhead.
2. **Allow List**:
   - Explicitly define allowed services. Example:
     ```json
     {
       "Effect": "Allow",
       "Action": ["s3:*", "ec2:*"],
       "Resource": "*"
     }
     ```

---

### 1.2.9. CloudWatch Logs

#### Overview
- **Purpose**: Store, monitor, and analyze logging data.
- **Architecture**:
  - **Log Events**: Data points with a timestamp.
  - **Log Streams**: Sequences of log events from the same source.
  - **Log Groups**: Containers for related log streams with shared settings (e.g., retention, permissions).
  - **Metric Filters**: Generate metrics based on log data.
- Regional service integrated with CloudWatch.

---

### 1.2.10. CloudTrail Essentials

#### Overview
Tracks API activity and user actions for auditing and compliance.

#### Key Features:
1. **Event History**: Retains 90 days of management events for free.
2. **Trails**:
   - **Single-region** or **all-region** configurations.
   - Logs events to S3 or CloudWatch Logs.
3. **Management Events**: Logs resource management activities (e.g., creating EC2 instances).
4. **Data Events**: Logs object-level activities (e.g., S3 uploads). Must be enabled explicitly.
5. **Global Services** (e.g., IAM, CloudFront): Always log to `us-east-1`.

#### Notes:
- Not real-time; ~15-minute delay.
- Pricing applies for additional trails, storage, and event data.

---

### 1.2.11. AWS Control Tower

#### Overview
- Quick and easy setup of multi-account environments
- Orchestrates other AWS services to provide this functionality
- Organizations, IAM identity center, CloudFormation, Config and more
- **Landing Zone**: Centralized management for multi-account environments
  - SSO/ID Federation, Centralized logging and auditing
- **Guard Rails**: Detect/Mandate rules/standards across all accounts
  - Mandatory, Strongly Recommended, or Elective
- **Account Factory**: Automates and standardizes account creation

---

## 1.3. Simple Storage Service (S3)

### 1.3.1. S3 Security

- **Private by Default**: Only the account root user has access initially.
- **Bucket Policy**: Manages access at the bucket level for cross-account or anonymous access.
  - Type of **Resource Policy**: like identity policy, but attached to bucket.
- **Access Control Lists (ACLs)**: Legacy feature; not recommended due to limitations.
- **Block Public Access**: Default setting that prevents access from public (fail-safe).

---

### 1.3.2. S3 Static Hosting

- Enables serving static websites directly from S3 via HTTP.
- Requires:
  - **Index Document**: Entry point (e.g., `index.html`).
  - **Error Document**: For error handling (e.g., `404.html`).
- Website Endpoint: Specific address that serves that page/content.
- Pricing: Costs based on storage (per GB/month), data transfer (per GB), and request rates (per 1k requests). Much cheaper than EC2 and other compute services.

---

### 1.3.3. Object Versioning and MFA Delete

- **Versioning**:
  - Retains previous versions of objects.
  - Once enabled, cannot be disabled (only suspended).
  - Delete marker hides versions without deleting them.
  - **ID**: Each object is given an ID if versioning is enabled.
- **MFA Delete**:
  - Adds MFA requirement for versioning changes or object deletions.

---

### 1.3.4. S3 Performance Optimization

1. **Multipart Uploads**:
   - For objects > 5GB, breaks data into parts for faster, fault-tolerant uploads.
   - Each part can be retried independently, reducing upload failures.

2. **S3 Accelerated Transfer**:
   - Uses AWS edge locations to improve transfer speed.
   - Ideal for large files and distant clients.

---

### 1.3.5. Key Management Service (KMS)

- **Purpose**: Manage encryption keys.
- Adheres to **FIPS 140-2 (L2)** compliance for AWS-managed keys.
- **KMS Keys**: Customer-owned keys used for cryptographic operations.
  - Logical - ID, date, policy, description & state.
  - Backed by physical key material.
  - Isolated to a single region & never leave.
  - AWS Owned or Customer Owned.
  - AWS Managed or Customer Managed.
- **Data Encryption Keys (DEKs)**:
  - Generated by KMS and used for encrypting large data.
  - KMS only stores encrypted DEKs, ensuring data security.

---

### 1.3.6. Encryption

#### Types of Encryption:
1. **At Rest**: Protects stored data (e.g., disk encryption).
2. **In Transit**: Secures data during transfer (e.g., HTTPS).

#### Methods:
- **Client-Side Encryption**: Performed by the user before data reaches S3.
- **Server-Side Encryption (SSE)**:
  1. **SSE-C**: Customer-provided keys.
  2. **SSE-S3**: AWS-managed keys.
  3. **SSE-KMS**: Keys managed by AWS KMS, allowing role-based access.

---

### 1.3.7. S3 Storage Classes

1. **Standard**: High durability and availability; low latency.
  - Used for **frequently accessed data** that is important.
  - Replicated over 3 AZs.
  - **HTTP/1.1 200 OK** response is provided by S3 API Endpoint when objects are stored.
  - Billed GB/month fee for data stored, $ per GB for transfer out (in is free), and $ per 1000 requests.
2. **Standard-IA**: Lower cost for infrequent access.
  - Used for **long-lived data** which is important but not frequently accessed.
  - Same as Standard but with retrieval fee (per GB).
  - Minimum duration charge of 30 days.
3. **One Zone-IA**: Cost-effective for less critical data stored in one AZ.
  - Used for **long-lived data** that is not critical and not frequently accessed.
  - Stored only in 1 AZ.
4. **Glacier Instant**: Cheaper storage, more expensive retrieval, longer minimum.
  - Used for long-lived data accessed **once per quarter** with millisecond access.
5. **Glacier Flexible** Archival storage with retrieval times of minutes to hours.
  - Requires retrieval process: Expedited (1-5 minutes), Standard (3-5 hours), Bulk (5-12 hours).
6. **Glacier Deep Archive**: Cheapest option for long-term archival with retrieval in hours to days.
7. **Intelligent-Tiering**: Automatically moves objects between storage classes based on usage.

---

### 1.3.8. Object Lifecycle Management

- **Lifecycle configuration**: Set of rules consisting of actions on a bucket or group of objects.
  - Transition Actions and Expiration Actions
- Automates transitions between storage classes or object expiration.
  - Minimum of **30 days** before transition.
- Example:
  - Move to Standard-IA after 90 days.
  - Archive to Glacier after 1 year.

---

### 1.3.9. S3 Replication

1. **Cross-Region Replication (CRR)**:
   - Replicates objects between buckets in different regions.
2. **Same-Region Replication (SRR)**:
   - Replicates objects within the same region.
3. Within same account or between different accounts.
  - Need a bucket policy at destination account to allow Source Role to transfer.

#### Key Notes:
- Requires versioning enabled on both buckets.
- Cannot replicate retroactively.
- Handles SSE-S3, SSE-KMS, and SSE-C.
- Source bucket owner needs permissions to objects.
- No system events, Glacier, or Glacier Deep Archive.

---

### 1.3.10. S3 Presigned URLs

- **Purpose**: Provide temporary access to S3 objects without sharing credentials.
- **Parameters**:
  - Security credentials.
  - Bucket and object key.
  - Expiry time.
- The permissions of the URL match the identity which generated it.

---

### 1.3.11. S3 Select and Glacier Select

- Retrieve specific parts of an object using SQL-like queries.
- Reduces data transfer and retrieval costs.

---

### 1.3.12. S3 Event Notifications

- Notification generated when events occur in a bucket
- Can be delivered to SQS, SNS, or Lambda.
- **Event Types**:
  - Object Created
  - Object Deleted
  - Object Restore
  - Object Replication

---

### 1.3.13. S3 Object Locks

- Store objects using a write-once-read-many (WORM) model.
- Prevent objects from being deleted or overwritten for a fixed amount of time or indefinitely.
- Requires versioning
- Retention period: days & years
- Architectures: Legal Hold, Governance, & Compliance
  - Compliance: Can't be adjusted, deleted, or overwritten.

---

### 1.3.14. S3 Access Points

- Simplify managing access to S3 buckets/objects.
- Create multiple access points for different use cases, policies, and network access controls.
- Created via Console or **aws s3control create-access-point** command.

---

## 1.4 Virtual Private Cloud (VPC)

### 1.4.1 Networking Refresher

#### IPv4 - RFC 791 (1981)
- Uses **dotted decimal notation**: 4 numbers (0-255) separated by periods.
- Total: **~4.3 billion addresses**.
- Limitation: Inflexible sizing; some addresses often go unused.

#### Classful Addressing
- **Class A**: `0.0.0.0 - 127.255.255.255` (Large companies; 128 networks).
- **Class B**: `128.0.0.0 - 191.255.255.255` (Medium organizations).
- **Class C**: `192.0.0.0 - 223.255.255.255` (Small networks).

#### Internet/Private IPs - RFC1918
- **Private IP ranges** (not internet-routable):
  - **Class A**: `10.0.0.0 - 10.255.255.255`
  - **Class B**: `172.16.0.0 - 172.31.255.255`
  - **Class C**: `192.168.0.0 - 192.168.255.255`

#### Classless Inter-Domain Routing (CIDR)
- CIDR notation: `<network address>/<prefix>`.
  - **Example**: `10.0.0.0/16` (65,536 addresses).
  - Larger prefix = smaller network, e.g., `/20` splits `/16` into 16 subnets.

#### Key IP Address Notations
- `0.0.0.0/0`: All IPs.
- `10.0.0.0/8`: All IPs starting with `10` (Class A).
- `10.0.0.0/16`: All IPs starting with `10.0` (Class B).
- `10.0.0.0/32`: Single IP address.

#### Packets
- Contains:
  - **Source IP**
  - **Destination IP**
  - **Data**
- Uses protocols:
  - **TCP** (connection-oriented).
  - **UDP** (connectionless).
- Ports allow multiple concurrent communications.

#### IPv6 - RFC 8200 (2017)
- Format: `2001:0db8:28ac::82ae:3910:7334`.
- Total: **128-bit** addresses (vastly larger than IPv4).
- CIDR notation applies, e.g., `2001:db8::/48` spans addresses `2001:db8:0000::` to `2001:db8:ffff::`.
- `::/0` covers all IPv6 addresses.

---

### 1.4.2 VPC Sizing and Structure

#### VPC Considerations
- **Size**: Plan VPC size to avoid overlap with other VPCs or cloud networks.
- **Range**: Use uncommon ranges, e.g., `10.16.x.x`.
  - Min: `/28` (16 IPs).
  - Max: `/16` (65,536 IPs).
- Reserve ranges per region/account for scalability.

#### Subnet Sizing
- Subnets are **AZ-specific**.
- Best practice: Use at least **4 AZs** with separate tiers (web, app, DB, spare).
- Example: Splitting `/16` into `/20` provides **16 subnets**.

---

### 1.4.3 Custom VPC

#### Features
- **Regionally isolated and resilient**.
- No inbound/outbound traffic without explicit configuration.
- **Hybrid Networking**: Connects on-prem/cloud environments.
- Tenancy options:
  - **Default**: Shared hardware.
  - **Dedicated**: Isolated hardware (higher cost).

#### Key Facts
- **IPv4**: Mandatory CIDR block (min `/28`, max `/16`).
  - Can add up to **5 secondary CIDR blocks**.
- **IPv6**: Assigned `/56` CIDR (public by default).
- **DNS Support**: Provided by Route 53.
  - IP: `<VPC Base> + 2` (e.g., `10.0.0.2`).
  - Options:
    - **DNS Hostnames**: Assign public DNS to instances.
    - **DNS Resolution**: Enable VPC DNS access.

---

### 1.4.4 VPC Subnets

#### Subnet Features
- **AZ-resilient**: Subnets fail if the AZ fails.
- IPv4 CIDRs: Cannot overlap within the VPC.
- IPv6: Optional allocation.
- **Communication**: Subnets in the same VPC communicate by default.

#### Reserved IPs
- **5 reserved IPs** per subnet:
  1. Network address (e.g., `10.16.16.0`).
  2. VPC router (`10.16.16.1`).
  3. DNS (`10.16.16.2`).
  4. Future AWS use (`10.16.16.3`).
  5. Broadcast (`10.16.31.255`).

#### DHCP Options Set
- Automates IP assignment.
- Cannot edit; create new set if changes are needed.

#### IP Allocation
- **Public IPv4**: Enabled to make subnets public.
- **IPv6**: Requires subnet and VPC IPv6 allocations.

---

### 1.4.5 VPC Routing and Internet Gateway (IGW)

#### VPC Router
- Moves traffic between subnets.
- Route tables define outbound traffic behavior.
  - Each subnet uses the **main route table** unless explicitly associated with a custom table.

#### Route Tables
- Matches destination IPs against routes.
- **Local routes** (within VPC) cannot be modified and take precedence.

#### Internet Gateway (IGW)
- **Enables external connectivity** for VPCs.
- **Regional service**: Covers all AZs in a region.
- Attach to VPC:
  - No IGW = private VPC.
  - One IGW = public VPC.
- Steps:
  1. Create IGW.
  2. Attach IGW to VPC.
  3. Create custom route table (RT).
  4. Associate RT.
  5. Default Routes => IGW.
  6. Subnet allocate IPv4.

#### IGW Functionality
- **Private-to-Public Translation**: Maps private IPs to public IPs for external access.
- **IPv6**: Public by default; no translation needed.

#### Bastion Host / Jumpbox

- An instance in public subnet where incoming management connections arrive.
- Used as an inbound management point, or as an entry point for private-only VPCs.

---

### 1.4.6 Stateful vs Stateless Firewalls
- Every connection has a request and a response.
- Both can be in either direction inbound/outbound (based on perspective).
- **Stateless**: Doesn't understand state of connections.
  - Need 2 rules (1 IN, 1 OUT) per connection.
- **Stateful**: Can identify the request and response components of a connection as being related.
  - Allowing request means response is automatically allowed.

---

### 1.4.6 Network Access Control Lists (NACLs)

#### Features
- **Stateless**: Separate inbound and outbound rules.
- **Subnet-level**: Filters traffic crossing subnet boundaries.
- Rules processed in order, lowest number first.
- Fields:
  - **Type**: Protocol (TCP/UDP/ICMP).
  - **Port Range**.
  - **Source/Destination**.
- Default implicit deny (`*`).

#### Example
- Allow HTTPS (TCP/443) inbound.
- Add outbound ephemeral ports (`1024-65535`) for response traffic.

#### Exam PowerUp
- Use NACLs for:
  - Explicit deny (e.g., blocking bad IPs).
  - Non-SG-compatible resources (e.g., NAT Gateways).

---

### 1.4.7 Security Groups (SGs)

#### Features
- **Stateful**: Tracks inbound/outbound traffic automatically.
- **Resource-level**: Attached to ENIs (Elastic Network Interfaces), not instances or subnets.
- Default SG:
  - Allows all traffic within the group.
  - Implicit deny for everything else.
- **Logical referencing**: When you reference an SG from another SG, you're implicitly referencing any resources associated with that SG.

#### Exam PowerUp
- Use SGs as the default for most configurations.
- Combine with NACLs for explicit deny scenarios.

---

### 1.4.8 Network Address Translation (NAT) Gateway

#### Features
- Gives Private CIDR range **outgoing** internet access.
- **IP Masquerading**: Hides CIDR blocks behind one IP.
- **Elastic IPs**: Static public IPs.
- AZ-resilient but not region-resilient (deploy in multiple AZs for HA).

#### Comparison
- **NAT Gateway**:
  - Managed service; scales up to **45 Gbps**.
  - No port forwarding or bastion host capabilities.
- **NAT Instance**:
  - On a EC2 Instance.
  - Customizable but single-point-of-failure.

#### IPv6 Note
- NAT not required for IPv6; all IPv6 addresses are public.


