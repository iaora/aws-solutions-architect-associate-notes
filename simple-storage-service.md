# Simple Storage Service (S3)

### S3 Security (Resource Policies & ACLs)
- S3 is private by default. Only the account that created the S3 bucket will have perms by default. Otherwise need to 
  grant permissions to access
- Bucket policies:
  - A form of resource policy
  - Like identity policy, but attached to a bucket 
  - Resource perspective permissions 
  - ALLOW/DENY same or different accounts (by putting perms on the resource)
  - ALLOW/DENY Anonymous principals 
  - In the policy, if there exists and "principal" field in the statement, then it likely means the resource = principal
  - If another account attempts to access your bucket, then:
    - your bucket policies apply (e.g. bucket allows outside access)
    - and that account's individual bucket policies apply as well (e.g. they need to be able to access S3)
- Access Control Lists (ACLs) - DO NOT USE 
  - ACLs on objects and bucket 
  - A subresource 
  - **legacy**
  - inflexible & simple permissions  
- Block Public Access
  - Applies to an anonymous principal 
> **Exam powerup:**
>  - Identity: Controlling different resources 
>  - Identity: you have a preference for IAM
>  - Identity: same account
>  - Bucket: Just controlling S3
>  - Bucket: Anonymous or cross-account 
>  - ACLs: **NEVER** - unless you must 

### Static Website Hosting 
- Normal access is via AWS APIs
- This feature would allow access via HTTP (e.g. blogs)
- Index and Error documents (HTML) are set 
- website endpoint is created

### Object Versioning & MFA Delete
- Controlled at a bucket level 
- Default is disabled on a bucket. Can enable, but once enabled, **can not** disable
- Can suspend an enabled bucket, then re-enable it 
- Versioning:
  - Lets you store multiple versions of objects within a bucket. Operations which would modify objects
    generate a new version 
  - when versioning disabled, when adding object in bucket, then the id = null. But when versioning enabled, then an ID 
    is set. If an object is set to replace the old object, then the new object has a new ID 
  - If want to interact with an object, can give an ID or not. If have an ID, then get that version. If no ID, then get 
    most up to date. 
  - Also impacts deletion:
    - If we want to delete an object without an ID, then S3 will make a delete marker. It's not actually deleted, but 
      hidden. Hides previous version. Can delete the delete marker, which makes it seem like an object hasn't ever been 
      deleted. To fully delete an object, then need to specify the object ID
  - **cannot be switched off**, only suspended
    - Suspending = does not remove objects, so still charged for it. 
  - Space is consumed for ALL versions 
- MFA Delete:
  - Enabled in versioning configuration 
  - MFA is required to change bucket versioning state 
  - MFA required to delete versions 
  
### S3 Performance Optimization 
- Multipart upload: break a larger file into smaller sizes and upload those
  - Min data size: 100mb 
  - Max size: 10,000
- S3 Accelerated Transfer (Off)

### Key Management Service (KMS)
- KMS:
  - Regional and public service 
  - Create, store, manage keys 
  - Symmetric and Asymmetric keys 
  - Cryptographic operations (encrypt, decrypt, etc.)
  - Keys never leave KMS - provides **FIPS 140-2** (Level 2/L2)
- Customer Master Keys (CMK)
  - CMK is logical - ID, date, policy, description, state 
  - backed by physical key material 
  - generated or imported 
  - CMKs can be used for data up to **4KB of data**
- Data Encryption Keys (DEKs)
  - GenerateDataKey - works on >4KB data 
  - linked to a specific CMK
  - KMS does not store/use the data encryption key in any way. Creates it and passes it over, then discards it 
    - User/outside service uses the DEK to encrypt the data
  - Plaintext version - of the key 
  - Ciphertext/Encrypted version - of the key encrypted with the CMK
  - Encrypt data using plaintext key 
  - Discard 
  - Store encrypted key with data
- Key concepts:
  - CMKs are isolated to a region & never leave 
  - AWS Managed (automatically created for services, like S3) or Customer Managed CMKs
  - Customer managed keys are more configurable 
  - CMKs support key rotation 
    - always enabled for AWS Managed keys, every 3 years
    - Customer managed keys are not rotating by default, but can be enabled to rotate each year
  - Backing Key (and previous backing keys)
  - Aliases (per region) 
- Key policies and security:
  - Key policies (resource)
  - Every CMK has one key policy. Trust is not automatic 
    - If the key trusts the account, and the account trusts the IAM role, then there can be a chain of trust 
  - Key policies + IAM policies

### Object Encryption
- Buckets aren't encrypted, but instead objects are 
- Client side: Client encrypts the data before sending it over to S3
- Server side: The data is sent in plaintext to S3 via HTTPS (so already encrypted), then S3 encrypts the data upon storing
- Types of Encyrption:
  - SSE-C: Server side encryption with Customer-provided Keys
    - Customer responsible for the encryption keys 
    - S3 endpoint - manages encryption. Offloading CPU requirements for this process 
    - Provide data in plaintext and the key to S3
    - S3 encrypts/decrypts the data, then discards the keys given by the user
      - Need to trust S3 to discard the keys 
  - SSE-S3: server side encryption with amazon s3-managed keys
    - Users provide the plaintext data 
    - S3 endpoint: manages encryption and keys 
    - A unique key is generated for every single object. S3 also generates a master key to encrypt the object encryption key
    - Then, S3 encrypts the object and the key, which is then stored on S3. Discard the uncrypted key
    - Uses AES -256
    - Problems:
      - Can't control the keys and access to the keys
      - Can't rotate the keys
      - Can't separate roles (e.g. different people to manage keys, encrypt, decrypt). 
        Everyone would have access to all of these permissions 
  - SSE-KMS: server side encryption with CMKs stored in AWS KMs
    - Similar to SSE-S3, but the master key is within KMS as a CMK
  - ![g](object-encryption.png)
- Bucket Default Encryption

### S3 Object Storage Classes
- S3 Standard
  - Storing/replicating objects across from at least 3 AZs in the AWS region 
  - 99.999999999 (11 9s) durability for 10,000,000 objects. aka 1 object loss per 10,000 years 
  - Content-MD5 checksums and Cyclic Redundancy checks (CRCs) are used to detect and fix any data corruption 
  - When objects are stored, A HTTP/1.1 200 OK response is provided by the S3 API endpoint. Data is stored durably 
  - With S3 standard, you are billed a GB/m fee for data stored. A $ per GB charge for transfer OUT (IN is free) and 
    a price per 1,000 requests. No specific retrieval fee, no minimum duration, no minimum size
  - "milliseconds" first byte latency (objects available within milliseconds) and objects can be made publicly available
- S3 Standard-IA (infrequent access)
  - Shares most of architecture of S3 Standard. Difference:
    - Storage costs are much cheaper than standard. 
    - New cost component: 
      - per GB data retrieval fee, overall cost increases with frequent data access
      - minimum duration charge of 30 days- objects can be stored for less but the minimum billing always applies
      - Minimum capacity charge of 128KB per object
  - Use for: long-lived data, which is important, but where access is infrequent. No small objects either 
- S3 One Zone-IA
  - Cheaper than Standard and Standard-IA
    - minimum capacity: 128KB 
  - Main difference: Data is stored in only 1 AZ in the region. Does not provide the multi-AZ resilience model as other classes
  - Use for: long-lived data, non-critical & replaceable and where access in infrequent 
- S3 Glacier
  - 3 AZs, same durability. About 1/5 of storage cost of standard
  - Differences: 
    - cold, not ready for use 
    - **not immediately available and not public accessible**. 
      - Any access of data (beyond object metadata) requires a retrieval process (which incurs a cost)
      - Types of retrieval:
        - Expedited (1-5 minutes) - most expensive
        - Standard (3-5 hours)
        - Bulk (5-12 hours) - cheapest
        - first byte latency = minutes or hours 
      - 40KB min size; 90 day min duration
  - Use for: archival data where frequent or realtime access isn't needed. Minutes-hours retrieval 
- S3 Glacier Deep Archive
  - 1/4 of the price of Glacier 
  - Objects in a frozen state
  - 40KB min size
  - 180 day minimum duration 
  - objects not publically available and need a retrieval process
  - Types of retrieval:
    - Standard (12 hours)
    - Bulk (up to 48 hours) 
    - Much longer than glacier 
  - Use for: Archival data that rarely, if ever, needs to be accessed - hours or days for retrieval e.g. legal or 
    regulation data storage
- Intelligent-Tiering 
  - Contains 4 different tiers of storage 
  - Tiers:
    - Frequent Access
    - Infrequent Access
    - Archive
    - Deep Archive 
  - Don't have to worry about moving objects between tiers
    - System monitors the usage of the object and moves the object into respective tiers based on access frequency
  - Monitoring and automation cost per 1,000 objects. 
    - Frequent = Standard
    - Infrequent = Standard-IA
    - Archieve & Deep Archive = their glacier equialent 
  - Use for: long-lived data with changing or unknown patterns 

### S3 Lifecycle Configurtation
- Set of rules that are applied to a bucket 
- Rules consist of actions 
- Can be applied on a bucket or groups of objects 
- Transition actions -> transfer objects from standard to standard-IA
- Expiration actions -> delete objects or versions of objects after a certain period of time 


### S3 Replication:
- Cross-Region Replication (CRR)
- Same-Region Replication (SRR) 
- Same account: that IAM role has access to both source and destination buckets 
- Different account: add a bucket policy that allows the role in the source account can write/replicate accounts 
  into the destination bucket 
- Replication options:
  - Default: all objects from one bucket to another, optionally can define a subset of objects
  - Storage class: default is so maintain the same as the source (e.g. standard, IA, etc.)
  - Ownership: default is the source account 
    - if there's another account, then the destination bucket object will be owned by the source account. Can change it
  - Replication Time Control (RTC): 15 minutes guaranteed level of predictability
- S3 Replication Considerations 
  - Replication is **not retroactive** & both source and destination needs versioning needs to be on 
  - One way replication: from source to destination only
  - Capable of handling objects that are: Unencrypted, encrypted with SSE-S3 & SSE-KMS (with extra config)
  - Source bucket owner needs permissions to objects 
  - No system events (only user events), no replications from buckets that are Glacier or Glaicer Deep Archive
  - Deletes are not replicated 
- Why use replication:
  - SSR - Log aggregation 
  - SSR - PROD and TEST sync 
  - SSR - Resilience with strict  sovereignty 
  - CRR - Global resilience improvements 
  - CRR - Latency reduction 

### Presigned URLs
- Can use pre-signed URLs for GET or PUT operations
> - **Exam PowerUps:**
>  - Can create a URL for an object you have no access to 
>  - When using the URL, the permissions match the identity which generated it **right now**
>  - Access denied could mean that the generating ID never had access or doesn't now 
>   - Don't generate a pre-signed URL with a role. Only identities, aka IAM User. URLs stop working when temporary 
     credentials expire

### S3 Select and Glacier Select 
- S3 can store HUGE objects (up to 5TB)
- You often want to receive the entire object 
- Retrieving 5TB objects takes time and uses 5TB 
- Filtering at the client side doesn't reduce this
- S3/Glacier select let you use SQL-like statements 
  - to select part of the object that's pre-filtered by S3
- CSV, JSON, Parquet, etc. 
- Filtering at S3:
  - up to 400% faster and 80% cheaper (because S3 charges based on item retrieval)

### S3 Events
- Notification generated when events occur in a bucket 
  - these notifications can be delivered to SNS, SQS< and Lambda functions 
- Objects created (put, post, copy, completemultipartupload)
- Objects delete (*, delete, deletemarkercreated)
- Object restore  (post(initiated), completed)
- Replication (missedthreshold, replicatedafterthreshold, etc.) 
- S3 Events is a bit outdated. EventBridge is an alternative that offers more types of events and for more services 

### S3 Access Logs
- Enable logging on source bucket 
- Logging managed by S3 Log Delivery Group 
  - Best efforts process
