# Cloud Computing Fundamentals 

### Cloud Computing - What is it ... really
As defined by [NIST](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf), cloud is:
1. On-Demand Self-Service
   2. TL;DR: "can provision capabilities as needed **without requiring human interaction**"
2. Broad Network Access
   1. "Capacilities are available over the **network** and access through **standard mechanisms**"
   2. e.g. Access your information via HTTP, HTTPS, VPN, etc. 
3. Resource Pooling 
   1. There is a sense of **location independence**. No **control** or **knowledge** over the exact **location** 
      of the resources.
      1. Moved away from thinking hardware is the primary value for customers, and instead hosting applications for 
         others as primary source of value.
   2. Resources are **pooled** to serve multiple consumers using a **multi-tentant model**
      1. Pooling allows for easier scaling whenever the customer needs it 
      2. Users allocate and return resources to a centralized pool 
      3. Cloud has isolation between customers so each customer can only see their own data 
4. Rapid Elasticity 
   1. Capabilities can be **elastically provisioned** and **released** to scale **rapidly** outward and inward with demand
   2. To the customer, the capabilities available for provisioning ofter **appear** to be **unlimited**
   3. The most important part of the cloud**
5. Measure Service
   1. Resource usage can be **monitors, controlled, reported, and billed**

#### Exam power-up:
1. On-Demand self-service - provision and terminate using UI/CI without human interaction
2. Broad Network Access - access services over any network, on any device, using standard protocols and methods
3. Resource pooling - economies of scale, cheaper service 
4. Rapid elasticity - Scale up (out) or down (in) automatically iin response to system load 
5. Measured Service - usage is measured. Pay for what you consume. 

### Public vs Private vs Multiple vs Hybrid Cloud
- Definitions:
  - Public Cloud: 
    - They are public clouds because they meet the 5 essential characteristics of cloud computing available to
      general public
    - Using one single public cloud (e.g. AWS, Azure, Google Cloud)
  - Private Cloud: 
    - Using on-premises **real** cloud (aka a cloud that supports all 5 components above)
      - Note: There is a massive difference between having on-premise hardware vs a private cloud 
    - Private cloud still needs to meet the 5 components of cloud, but in most cases, they do not
  - Multiple Cloud: 
    - Uses multiple cloud public clouds 
    - stay away from any products or products that stay away from a single management window (because it abstracts too much)
    - highly available infrastructure (if one goes down, then the other will still be on)
  - Hybrid Cloud: 
    - Only counts if using public and private cloud together as a single environment 
    - Does not count if using public cloud and on-premise equipment 
    - Hybrid **environment/networking**: public cloud & on-premise equipment


### Cloud Service Models 
- Terms and concepts: 
  - Infrastructure Stack (OSI model?)
    - Facility (building) 
    - Infrastructure (networking)
    - Servers (physically required to run an app)
    - Virtualization (allows servers to be carved up into virtual machines)
    - O/S (Servers run O/S)
    - Container (e.g. docker)
    - Runtime (Languages need to run in this environment)
    - Data ()
    - Application ()
  - For the stack, there are parts managed by **you** and some managed by the **vendor** 
  - Unit of consumption 
    - Parts of the stack that a user is responsible for per each task 
- Different Models:
  - On-Premises vc DC Hosted 
    - On premises:
      - All managed by the user
      - Must buy all the components part of the infra stack, including any staff affiliated with facilities 
    - DC Hosted:
      - The facilities are hosted by the vendor, but the user is in charge of everything 
  - Infrastructure as a Service (IaaS)
    - The vendor controls facilities, infra, servers, and virtualization 
    - The User still controls the O/S and anything above that layer in the stack 
    - Pay based on time interval and only when the hardware is used 
    - One of the most popular service models 
    - Unit of consumption: O/S
  - Platform as a Service (PaaS)
    - Vendor controls everything from Container and down
    - User is in charge of Runetime, Data, and Application 
    - Mainly used for developers
    - Unit of consumption: Runtime
  - Software as a Service (SaaS)
    - Vendor is in charge of everything from Data and down
    - User is in charge of only Application 
      - e.g. Netflix
    - Unit of consumption: Application