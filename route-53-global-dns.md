# Route 53 - Global DNS

### R53 Public Hosted Zones
- A R53 hosted zone is a DNS DB for domain e.g. animals4life.org
- Globally resilient (multiple DNS servers) 
- Created with domain registration via R53 - can be created separately 
- Host DNS records (e.g. A, AAAA, MX, NS, TXT)
- Hosted zones are what the DNs system references - authoritative for that domain
- DNS Database (zone file) hosted by R53 (public name servers) 
- Accessible from the public internet & VPCs
- When create a public hosted zone, R53 allocates "4" R53 name servers (NS) specific for the zone 
  - Then use the "NS records" to point at these NS (connect to global DNS)
- Inside the public hosted zone, create Resource Records (RR), which is what DNS actually uses
- Externally registered domains can point at R53 Public Zone

### R53 Private Hosted Zones
- Overview: 
  - A public hosted zone, which isn't public
  - Associated with VPCs, and only accessible in those VPCs 
  - Using different accounts is supported via CLI/API
  - Split view (overlapping public and private) for PUBLIC and INTERNAL use with the same zone name
- Architecture:
  - Inaccessible from the public internet
  - But can access from a VPC if they are associated with the private hosted zone
- Split View Hosted Zones:
  - Similar to public hosted zones, but the public hosted zone = a subset of information of the private hosted zone 
  - Only those from the VPC and are associated with the private zone can see the private zone information

### CNAME vs R53 Alias
- Issues with only having CName:
  - "A" maps to a NAME to an IP address
    - e.g. catagram.io -> 1.3.3.7
  - CNAME maps NAME to another NAME
    - e.g. www.catagram.io -> catagram.io
  - CName is invalid for naked/apex (e.g. catagram.io)
  - Problem: because AWS services use the DNS Name (ELBs)
  - With just CName - catagram.io -> ELB would be invalid 
- ALIAS:
  - ALIAS records map a NAME to an AWS resource
  - Can be used for both naked/apex and normal records
  - For non-apex/naked - functions like CNAME
  - There is no charge for ALIAS requests pointing at AWS resources 
  - For AWS Services - default to picking ALIAS 
    - Should be the same "Type" as what the record is pointing at 
  - Should use for: API Gateway, CloudFront, Elastic Beanstalk, ELB, Global Accelerator & S3

### Simple Routing
- Overview:
  - 1 record per name (www) 
  - Each record can have multiple values 
  - All values are returned in a random order 
  - Client then chooses 1 value 
- Use for: when you want to route requsts towards one service such as a web server
- Does not support health checks - all values are returned for a record when queried

### R53 Health Checks
- Overview:
  - Health checks are separate from, but are used by records 
  - Health checkers located globally 
  - Can check any IP on the public internet
  - Health checkers check every 30s (every 10s costs extra)
  - Types of areas to check for: 
    - TCP, HTTP/HTTPS, HTTP/HTTPS with String Matching 
  - An endpoint is either health or unhealthy 
  - Types of checks:
    - Endpoint
    - CloudWatch Alarm 
    - Checks of Checks (calculated)
  - If 18%+ of the health checkers report as healthy, then the health check is healthy 
  - In most cases, an unhealthy record is not returned in queries 

### Failover Routing
- In a hosted zone, there's a primary www record, and a secondary www record 
  - A common architecture is to use failover for a "out of band" failure/maintenance page for a service 
- If the target of the health check is `healthy`, then the primary record is used. 
- If the target of the health check is `unhealthy`, then any queries return the secondary record of the same name 
  (e.g. an S3 bucket)
- When you want to configure active passive failover
  - Route traffic to a resource when that resource is healthy, or 
  - to a different resource when the original resource has failed its health check

### Multi Value Routing
- Merging simple and failover together 
- Overview:
  - Multi-value routing supports multiple records with the same name
    - Each are mapped to its own IP address
    - Each record is independent and can have associated health check with it 
    - Any records which fail the health check won't be returned when queried in the next step
  - Up to '8' health records are returned. If more than 8 exist, then 8 are randomly selected. 
  - Client chooses one of them and uses it to connect. 
- Multi-value improves availability. It is **NOT** a replacement for load balancing

### Weighted Routing
- Use for: simple load balancing or testing new software versions 
  - Only one record is chosen and returned 
- Overview:
  - Multiple records (e.g. 3 A records )
    - Each record has its own weight 
    - The weight means the probability that it'll be returned in a query
    - Each record is returned based on its record weight vs the total weight of all records
      - Total weight = ∑ of all record weights
    - e.g. Record 1 of weight 40, Record 2 of weight 40, and Record 3 of weight 20. 
    - Record 1 and 2 will be returned 40% of the time, then Record 3 will be returned 20% of the time 
    - A '0' weight means that a record is never returned unless all others are weighted '0', then all are evenly considered
    - IF a chosen record is unhealthy, then the process of selection is repeated until a healthy record is chosen 

### Latency Routing
- Overview: 
  - Supports routing one record with the same name in each AWS region 
  - AWS maintains a database of latency between users general location and regions tagged in records
  - The record returned is the one with which offers the lowest estimated latency and is healthy
- Use for: latency-based routing when optimising for performance and user experience

### Geolocation Routing
- Overview:
  - Similar to latency, but it's the location that influences the server choice
  - With geolocation, records are tagged with location. This can be either a "US state", "country", "continent" or "default"
  - An IP check verifies the location of the user (normally the resolver)
  - Geolocation doesn't return "closest" records, but instead only returns relevant (location) records based on location:
    - R53 checks for records in 1) the state 2) the country 3) the continent and 4) (optionally) default
      - It returns the most specific record or "NO ANSWER"
- Use for: regional restrictions, language specific content, or load balancing across regional endpoints

### Geoproximity
- Get records as close to the customer as possible 
- Similar to latency, but routing primarily calculated with distance 
- Overview: 
  - Records can be tagged with an AWS region or lat & long coordinates 
  - "+" or "-" bias can be added to rules. "+" increases a region size and decreases a neighboring regions 
  - Routing is distance based (including bias)

### R53 Interoperability
- Overview: 
  - R53 has 2 jobs - Domain registrar and domain hosting
    - R53 can do both or either 
  - R53 accepts your money (domain registration fee) 
  - R53 allocates 4 name servers (NS) (domain hosting) 
  - R53 creates a zone file (domain hosting) on the above NS 
  - R53 communicates with the registry of the TLD (domain registrar) 
    - sets the NS records for the domain to point at the 4 NS above 
  - 
