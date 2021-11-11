# Network Storage

### EFS (Elastic File System) Architecture
- Overview:
  - EFS is an implementation of NFSv4
  - EFS Filesystems can be mounted in Linux 
  - Shared between many EC2 instances 
    - exists separately from any EC2 instance 
  - Private service, via mount targets inside a VPC 
  - Can be accessed outside the VPC (from on-premises) via VPN or DX 
- Architecture:
  - IP address mount target to connect to the EFS 
- Key points:
  - Linux only 
  - Performance Modes:
    - General purpose: 
      - ideal for latency, web servers, content, home directories
      - Default for 99.9% of uses
    - Max I/O
      - con is increased latency
      - Mostly used for parallel 
  - Throughput modes: 
    - Bursting (by default) 
    - Provisioned 
  - Storage classes:
    - Standard (default) 
    - Infrequent Access (IA)
    - Similar to S3 storage classes
    - Lifecycle policies can be used to move objects between classes 