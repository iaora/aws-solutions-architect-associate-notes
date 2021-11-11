# Virtual Private Cloud (VPC) Basics

### Networking Refresher 
- IPv4:
  - Class A:
    - 0.0.0.0 - 127.255.255.255; 2 billion address; 128 total networks; 16 million IPs per network
  - Class B:
    - 128.0.0.0 - 191.255.255.255; 1 billion address; 16,384 total networks; 65,000 IPs per network
  - Class C:
    - 192.0.0.0 - 223.255.255.255; 536,000,000 addresses; 2,000,000 total networks; 256 IPs per network
  - Class D & E
  - Internet/Private IPs
    - Defined by standard RFC1918 
    - 10.0.0.0 - 10.255.255.255 (A Single Class A)
    - 172.16.0.0 - 172.31.255.255 (16 Class B)
    - 192.168.0.0 - 192.168.255.255 (256 Class C)
  - Classless inter-domain routing (CIDR)
    - 10.0.0.0/16 -> "/16" is the prefix 
      - bigger the prefix, the smaller the network & vice versa 
    - 10.0.0.0/16 = 10.0.0.0/17 + 10.0.128.0/17
    - 10.0.0.0/16 = 
      - 10.0.0.0 - 10.0.63.255 (10.0.0.0/18) + 
      - 10.0.64.0 - 10.0.127.255 (10.0.64.0/18) + 
      - ...
    - 0.0.0.0/0 means all IP addresses
    - 1.3.3.7/32 the "32" means 1 IP address 
    - 10.0.0.0/8 = 10.ANYTHING - Class A - 16 million IPs
    - 10.0.0.0/16 means 10.0.ANYTHING - Class B - 65,536 IPs
    - 10.0.0.0/24 means 10.0.0.ANYTHING - Class C - 256 IPs
- IPv6:
  - 2001:db8:1234::/48
    - 2001:db8:1234:: = 2001:db8:1234:0000:0000:0000:0000:0000
  - ::/0 -> all IPv6 addresses 

### VPC Sizing and Structure 
- Need to figure out the VPC range before starting. Things to consider:
  - What **size** the VPC should be
  - Are there any networks **we can't use** 
  - VPC's, Cloud, on-premises, partners & vendors
  - Try to predict the future 
  - VPC Structure - Tiers & Resiliency (Availability) Zones
  - What IP ranges need to avoid 
- More considerations:
  - in AWS, VPC minimum is /28 and maximum is /16 
  - Avoid common ranges - avoid future issues 
  - Determine how many ranges == how many AWS regions the business will operate in + a few more for buffer
    - Reserve 2+ networks per region being used per account 
    - Example:
      - For each account: 3 US regions, 1 Europe, 1 Australia (5 total)
        - For each of these regions, need 2 IP addresses, for a total of 10 IP addresses per account (2 per region)
      - If we have 4 accounts in total, then need 40 ranges (ideally)
- VPC Structure:
  - \# of AZ: 3 default + a spare = 4 total AZ 
  - Tiers: Web, App, DB, Spare 


### Custom VPCs 
- Regional service - All AZs in the region
- Create isolated networks 
- Nothing IN or OUT without explicit configuration 
  - If there's a problem in a VPC, then the impact is limited to this VPC or anything connected to it
- Flexible configuration - simple or multi-tear
- Hybrid network - on cloud & on-premises networking
- Default or dedicated tenancy
  - Controls whether resources created in VPC is made in dedicated or shared hardware 
  - Default -> can be shared and then moved to dedicated. 
  - Dedicated -> all resources are on dedicated hardware. 
  - If unsure/don't know if things need to be dedicated, then always go for default 
- IPv4 private CIDR blocks & public IPs
- 1 mandatory private primary IPv4 CIDR block
  - restrictions: min /28 (16 IPs) and max of /16 (65,536 IPs)
- Can create secondary IPv4 blocks (max of 5, but can increase with support)
- Optional single assigned IPv6 /56 CIDR block 
- DNS in VPC:
  - Provided by Route 53 
  - Two boolean variables:
    - enableDnsHostnames - gives instances DNS names
    - enableDnsSupport - enables DNS resolution in VPC

### VPC Subnet 
- AZ Resilient 
- A subnetwork of a VPC - within a particular AZ 
- 1 subnet is only in 1 AZ. One AZ can have many subnets 
- IPv4 CIDR is a subset of the VPC CIDR
- Cannot overlap with other subnets 
- Optional IPv6 CIDR (/64 subset of the /56 VPC - space for 256)
- Subnet IP Addresses:
  - Reserved IP Addresses (**5 in total**) 
  - 10.16.16.0/20 = 10.16.16.0 - 10.16.31.255. Unusable IPs:
    - Networking address (10.16.16.0) - starting address of the network
    - Networking +1 (10.16.16.1) - AWS VPC Router 
    - Network +2 (10.16.16.2) - AWS Reserved for DNS*
    - Network +3 (10.16.16.3) - AWS Reserved for Future Use 
    - Broadcast Address (10.16.31.255 - last IP in subnet)

### VPC Routing and Internet Gateway 
- Every VPC has a VPC Router - highly available 
- In every subnet. Sits on 'network+1' address
- Routes traffic between subnets
- Controlled by 'route tables' each subnet has one 
- A VPC has a Main route table - subnet default 
- Higher CIDR number = more specific = higher priority in the route table 
- Local routes always take priority 
- Things to know:
  - Route tables are attached to zero or more subnets 
  - A subnet has to have a route table (either main of the VPC or custom)
  - A route table controls what happens to data as it leaves the subnet/subnets that the route table is associated with
  - Local routes are always there when editable and match VPC IP version 4 or 6 side range
- Internet Gateway (IGW)
  - Region resilient gateway attached to a VPC (need only one per region) 
  - 1 VPC = 0 or 1 IGW; 1 IGW = 0 or 1 VPC (1 to 1 relationship). 
  - Runs from within the AWS public zone
  - Gateways traffic between the VPC and the Internet or AWS Public Zone (S3, SQS, SNS)
  - Managed - AWS handles performance 
- Using an IGW:
  1. Create IGW
  2. Attach IGW to VPC
  3. Create custom RT (route table)
  4. Associate RT
  5. Default Routes -> IGW 
  6. Subnet allocate IPv4
  7. -> this allows for the VPC to become a public network 
- IPv4 Addresses with a IGW
  - IPv4 instance only knows the private IPv4 IP address
  - The public internet can access this IPv4 instance, but it's done via a mapping of the public IPv4 address to the 
    the private IPv4 address via the IGW
  - When a packet is coming from the private IPv4 instance, IGW translates the packet's source addresse from the 
    private address to the public address based on the route table.
  - **The OS on the IPv4 instance does not know the public IP address**
- IPv6 Addresses with IGW:
  - All AWS IPv6 addresses are natively, publicly route-able
  - IGW just passes data from source to destination. No translation is done
- Bastion Host/Jumpbox 
  - Bastion Host == Jumpbox 
  - An instance in a public subnet 
  - Incoming management connections arrive there
  - Then access internal VPC resources 
  - Often the only INTO a highly secure VPC
    - (Inbound management point)


### Network Access Control Lists (NACLs)
- Similar to firewalls for VPC subnets 
- Have inbound and outbound set of rules for the subnet 
  - Processed in order
  - Low rule number first 
  - Stops if matched
  - NACLs can have explicit allow and deny 
  - Default: implicit deny if no rules are matched, but be default it allows all connections
- Outbound:
  - The response from the web application gets sent back on an Ephemeral Port (any port from 1024-65,535)
- Communication between Web and App 
  - The web and app needs its own web NACL, even when communicating to each other (because they're different subnets)
> - Exam PowerUp
>  - **Stateless** - **initiation** and **response** are seen as different 
>  - Only impacts data **crossing subnet border** 
>  - Can EXPLICITLY ALLOW and DENY 
>    - Key: Can explicitly deny (which not a lot of services can)
>  - IPs/Networks, Ports & Protocols - no logical resources 
>  - NACLs cannot be assigned TO AWS resources. Only subnets 
>  - Use WITH Security Groups to explicit DENY (Bad IPs/Nets)
>  - One subnet = One NACL at a time 
>    - NACL can be part of multiple subnets, but a subnet can only have 1 NACL 
>  - Lower rule numbers processes first

### Security Groups 
- Attached to AWS resources 
- Security Groups:
  - Have inbound and outbound traffic table 
  - Stateful - any response viewed as same communication for the inbound communication 
  - Cannot explicitly deny; hidden implicit deny 
> - Exam Powerups:
>  - Stateful - TRAFFIC and RESPONSE = same rule 
>  - SGs can filter based on AWS logical resources
>    - resources, other SGs and even themselves
>  - Implicit deny and explicit allow
>  - But, **no explicit deny** 
>  - SGs vs NACL
>    - NACLs on subnet for any product which don't work with SGs (e.g. NAT Gateway)
>    - NACLs when adding explicit DENY (bad IPs, bad actors)
>    - NACLs are applicable on only subnets and filter traffic that crosses the subnet 
>    - SG as the default almost everywhere else 

### Network Address Translation (NAT) & NAT Gateway
- It is a set of processes - remapping SRC and DST IPs
- IP masquerading - hiding CIDR Blocks behind one IP 
  - Important because Private IPv4 addresses are running out 
- Gives private CIDR range **outgoing** internet* access
- NAT Architecture
  - Place a NAT Gateway in a public subnet 
  - Thus, when a private App can set up an individual route table, so by default, it'll send packets to tbe public web
    server if it needs to get packets to the public internet 
  - NAT's goal is to masquerade all the private connections from the public internet- by being the middle man 
- Things to know about NAT Gateways:
  - If want to give an instance its own public IPv4 address, then only internet gateway is required
  - If want to give private instances outgoing access to the internet and is in AWS public zone, then need both NAT
    gateway to do this many to one translation and internet gateway to translate NAT IP to real public IPv4 address
  - A subnet is deemed to be a Public Subnet if it has a Route Table that directs traffic to the Internet Gateway.
  - Uses Elastic IPs (static IPv4 Public)
  - AZ resilient service (High Availability in that AZ)
  - For region resilience - NATGW in each AZ
    - RT in for each AZ with that NATGW as target 
  - Managed, scales to 45Gbps, $ duration and data volume 
  - Do not support security groups. Only can use NACLs with NAT Gateways 
- NAT Instance:
  - Always prefer to use NAT Gateway over an instance (hosted in EC2)
  - Can use NAT Instance if:
    - deploying test pipeline 
    - Use them as an EC2 instance to manage it (can use bastion/port forwarding)
    - Have a lot of data 
    - If want to have security groups and NACLs
- What about IPv6?
  - NAT isn't required for IPv6 
  - All IPv6 addresses are publicly routable in AWS 
  - Internet Gateway works directly with all IPv6 IPs 
  - **NAT Gateways don't work with IPv6** 
  - ::/0 route + IGW for bi-directional connectivity 
  - ::/0 Route + Egress-only internet gateway - outbound only 
