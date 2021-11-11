# Containers and ECS

### Introduction to Containers 
- Dockerfiles are used to build images 
- Portable - self-contained, always run as expected 
- Lightweight - Parent OS used, fs layers are shared 
  - have an additional read/write layer to the OS 
- Containers only runs the applicaiton and environment it needs 
- Provides much of the isolation that VMs do
- Ports are 'exposed' to the host and beyond 
- Application stacks can be multi-container

### Elastic Container Service (ECS) - Concepts
- Cluster - where your container runs from 
- ECR - Elastic Containter Registry 
- Components:
  - Container definition: 
    - provides enough information about the single container that the user wants to define
    - Similar to a pointer to the task definition 
  - Task Definition: 
    - Store the resources used by the task (e.g. store networking mode, compatibility with EC2 or Fargate)
    - Also stores the **task role** 
      - IAM role the task can use
      - Temporary credentials 
      - Best practice way of giving containers within ECS permissions to AWS products and services
  - Tasks and containers are different things. 
  - Service definition: 
    - Defines a service (e.g. how want a task to scale, how many copies to run, etc.)
    - Adds capability and add resilience because can have multiple independent copies of our task running, and can add 
      load balancer 
    - Gives your tasks scalability and high availability 
- Key points summary:
  - Container definition - defines which image to use and which ports are exposed 
  - Task Definition: security (Task role), Container(s), Resources that the task will consume 
  - Task Role: IAM role which the TASK assumes 
  - Service - how many copies, high availability, restarts 
  - Supports Docker as the container standard 

### ECS - Cluster Mode
- EC2 Mode:
  - ECS Cluster runs in a VPC, so can span multiple AZs 
  - Host multiple EC2 instances 
- Fargate: 
  - Only pay for the container capacity that you use 
- EC2 vs ECS (EC2) vs Fargate
  - If you use containers: ECS 
  - Large workload and price conscious: EC2 Mode 
  - Large workload and overhead conscious: Fargate 
  - Small/Burst workloads: Fargate 
  - Batch/Periodic workloads: Fargate 