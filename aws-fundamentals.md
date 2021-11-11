# AWS Fundamentals

### AWS Global Infrastructure 
- AWS Regions:
  - Geographic separation - isolated fault domain
  - Geopolitical separation - different governance 
    - The data stored in each region is subject to the laws of that specific region 
    - The data also will be stored at the specific location without moving to another region (unless explicitly specified)
  - Location control: performance 
- Service Resilience:
  - Globally Resilient: Multiple regions can go down, and the service would still be active
  - Region Resilient: Replicate to multiple Availability Zones in the region 
  - AZ Resilient: Services run from a single AZ zone. If a single service is hosted on an AZ and that fails, 
    then that service will be down.

### AWS Default Virtual Private Cloud (VPC) 
- VPC = virtual network inside AWS 
  - VPC is within 1 account and 1 region 
  - Regional services and regionally resilient 
  - By default, VPCs are private and isolated from other VPCs 
  - **1 default VPC per region, but can have many custom VPCs per region**
- Default VPC Facts
  - One per region - can be removed & recreated
  - Default VPC CIDR is always 172.31.0.0/16
  - /20 subnet in each AZ in the region (higher slash number, the smaller the network is)
  - Internet Gateway (IGW), Security group (SG) and NACL
  - Subnets assign public IPv4 addresses

### Elastic Compute Cloud (EC2) Basics
- IAAS - Infrastructure as a service 
  - Provides virtual machines -> instances
- Private AWS service (by default, runs in private AWS zone). Uses VPC networking
- AZ Resilient - Instance fails if AZ fails 
- Instance:
  - Can choose size and capabilities of this instance 
  - On-demand billing - per second
  - Local on-host storage 
- Instance lifecycle:
  - Running
    - Taking CPU, memory, storage, and networking while running 
    - AWS charges for this 
  - Stopped
    - No CPU, memory, networking resources 
    - Storage is still allocated to the instance, regardless of operating state
  - Terminated 
    - Instance gets fully deleted. Cannot be brought back upon termination 
    - No CPU, memory, storage, networking resources 
    - Deletes the disks upon termination 
- Amazon Machine Imagine (AMI)
  - Image of an EC2 instance 
  - AMI can create an EC2 instance, or an EC2 instance can create an AMI
  - similar to server image/USB device that contains the OS
  - Details:
    - Comes with attached permissions 
      - Public - everyone allowed to launch instances from the AMI
      - Owner - implicit allowed to create EC2 instances from that AMI
      - Explict - owner grants access to AMI for specific AWS accounts 
    - Root volume 
    - Block Device Mapping 
  - Windows port 3389 - Windows Remote Desktop Protocol (RDP)
  - Linux port 22 - for SSH

### Simple Storage Service (S3) Basics
- 101:
  - Global storage platform - regionally based/resilient 
    - Buckets are regional 
  - Public service, unlimited data & multi-user 
  - Storing large data (movies, audio, photos, etc.)
  - Economical & accessed via GUI/CLI/API/HTTP
  - Default storage of AWS 
  - Objects & buckets 
    - Objects: the data S3 stores 
    - Buckets: containers for objects
- Objects:
  - Objects are like files 
  - Components:
    - Key: (file name) - if you know the object key and bucket, then can access the file
    - Value: content being stored (picture, etc)
      - Value size can range from 0 bytes to 5TB 
    - Version ID, Metadata, Access Control, Subresources 
- Buckets: 
  - Created in a specific AWS region, thus the bucket has a primary home region. Won't leave unless configured to leave
  - Blast radius is contained only within the region 
  - Bucket name needs to be **globally unique** (across all regions and all AWS accounts)
  - Buckets can hold an unlimited number of objects, thus a bucket can hold data from 0 bytes to unlimited 
  - S3 bucket has no complex structure. It's flat. Everything is stored in the bucket at the root level 
    - However, there's an ability to create prefixes to model a file directory (but everything still is in the root folder) 
  - No concept of object type based on file name (.jpg, etc.)
>Exam power up:
>  - Bucket names are globally unique 
>  - Bucket names 3-63 characters, all lowercase, no underscore
>  - Start with lowercase letter or number
>  - Can't be formatted like an IP
>  - Buckets - 100 soft limit; 1000 hard limit (done via soft requests) per account 
>    - Exam may will ask how to structure a system with thousands of users 
>  - Unlimited objects in a bucket; objects can be 0 bytes to 5TB in size 
>  - Key = Name, Value = Data 
- S3 Patterns and Anti-Patterns
  - S3 is an object store - not file or block 
    - Because it's not a block, can't mount an S3 bucket 
  - Great for large scale data storage, distribution, or upload
  - Great for 'offload' (e.g. photos, videos, etc. for a blog)
  - Input and/or output to many AWS products 

### CloudFormation (CFN) Basics
- IaC (Infrastructure as Code) product in AWS 
- Uses templates in YAML or JSON 
- Resources:
  - Tells CloudFormation what to do 
  - Only mandatory part of the CloudFormation template 
- Description:
  - Author can add description to the template 
  - Only restriction: if have AWSTemplateFormatVersion, then need to have description needs to follow immediately after 
- Metadata: 
  - Control how the different functions are presented in the AWS console (UI)
- Parameters:
  - Prompt the user to input information 
- Mappings (Optional):
  - Create lookup tables 
- Conditions:
  - Certain things in the template will happen only if a conditional happens 
  - Create conditions, then use Conditions later 
- Outputs:
  - Once template is finished, it'll present outputs based on what's created, updated, or deleted
- CloudFormation starts with a template 
  - For any logical resource in the stack, CloudFormation makes a physical resource in your AWS account 
  - It's CloudFormation's job to keep the logical and physical resources in sync 
  - If delete a stack, then CloudFormation will delete the physical resources as well
  - Allows users to automate resources

### Cloudwatch (CW) Basics
- Collects and manages operational data 
- Metrics - AWS products, apps, on-premises

### High-Availability vs Fault-Tolerance vs Disaster Recovery
- High-Availability: Aims to ensure that an agreed level of operational performance, usually uptime, for a higher
  than normal period
  - This is not about the user experience 
  - It's about maximizing a system's online time 
  - 9.99% (three 9s) = 8.77 hours per/year downtime
  - 99.999% (Five 9s) = 5.26 minutes per/year downtime
  - Likely needs redundant servers to help reduce downtime 
- Fault-Tolerance: Property that enabled a system to continue operating properly in the event
  of the failure of some (one or more faults within) of its components. 
  - The service has to continue operating even through disruption/failure 
  - Need to minimize outages, but also design system to tolerate the failure (e.g. automation to re-route failures)
  - Harder to design, implement, and costs more than HA 
- Disaster Recovery (DR)
  - A set of policies, tools, and procedures to enable the recovery or the continuation of vital technology infrastructure 
    and systems following a natural or human-induced disaster

### Domain Name System (DNS) Fundamentals 
- System of delegation 
- Root Hints -> config points at the root servers IPs and addresses 
- Root server -> hosts the DNS root zone 
- Root zone -> points at TLD authoritative servers 
- gTLD -> generic top level domain (.com, .org)
- ccTLD -> country top level domain (.uk, .eu, etc.)

### Route53 (R53) Fundamentals
- Basics:
  - Register Domains
  - Host Zone files / managed nameservers
  - Global service, single database
  - Globally resilience 
- Registering domains:
  - Has relationships with domain registries 
- Hosted Zones
  - Zone files in AWS
  - Hosted on four managed name servers 
  - can be **public**, or **private** when linked to VPCs

### DNS Record Types
- Nameservers
  - Nameservers within the .com zone points to a record in the www.amazon.com zone
- A and AAAA Records
  - For a given website, 
    - A -> points to IPv4 ip address
    - AAAA -> points to IPv6 ip address 
- CNAME Records
  - Host to Host record 
  - A server points to a given IPv4
  - CNAME ftp, mail, www points to the server, which points to a specific IPv4 address 
  - CNAME can only point to a name (no IP addresses)
- MX Record
  - Priority and value 
  - value: 
  - Email servers looks at the "To:" address, and focuses on the domain name
  - Lower values is higher priority 
    - e.g. 10 has higher priority than 20 
  - TL;DR: How a server can find a mail server for a domain 
- TXT Record:
  - Add arbitrary text to a domain 
  - Used for proving domain ownership 
- TTL - Time To Live
  - Numeric value in seconds 
  - Non-authoritative is usually instant to query a domain IP address. Obtained from cached IP address in Resolver
  - Authoritative is follows the tree to get the final IP address 
  - TTL is important for when IP addresses change
    - Lower the TTL value if there's any changes to the DNS records to reduce caching related issues 
    