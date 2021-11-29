# IAM, Accounts, and AWS Orgs

### IAM Identity Policies
- IAM Priority Order:
    - 1.Explicit **DENY** statements
    - 2.Explicit **ALLOW** statements
    - 3.Default **DENY** (Implicit)
- Be default, Start off with no access to any AWS resources
- If a user has multiple policies:
    - then AWS will congregate all the policy documents and follow the same rule as above: deny, allow, deny
- Inline policy:
    - Apply the statements into each individual accounts (not best practice)
- Managed Policy:
    - Created as its own object
    - Attach this identity to any user
    - Pros: reusable and low management overhead
    - Should be used for normal default authorization
- Inline vs Managed:
    - Inline is only for special or exceptional allows or denies

### IAM Users and ARNs
- IAM Users are an identity used for anything requiring long-term AWS access (e.g. humans, Applications, 
  or service accounts)
  - Any "named" things will likely be an IAM account
- Principal - individual people, applications, services, etc. 
  - Authentication (Client to IAM)
    - Makes a request with a resource in AWS 
    - To do so, the Principal needs to authenticate with IAM. 
      - This is done via Username & Password (human) or access keys (service or human on CLI)
  - Authorize (IAM to Client)
    - IAM checks the credentials given to ensure that it's the correct person/thing making the request
- Amazon Resource Name (ARN)
  - Uniquely identify resources without any AWS accounts 
  - `arn:aws:s3:::catgifs`
    - References the "catgifs" bucket, and not the objects in the bucket 
  - `arn:aws:s3:::catgifs/*`
    - References the objects in the "catgifs" bucket, but not the bucket itself
  - ARN are fields, separated by colons 

> - **Exam power up:**
>  - 5,000 IAM Users per account
>  - IAM User can be a member of 10 groups
>  - This has system design impacts.. internet scale applications, large orgs, org merges 
>    - To fix this: use IAM roles & identity federation 

### IAM Groups
- **Cannot log into an IAM group**. Do not have credentials
- Used solely to organize IAM users
- Users can be part of multiple groups 
- Groups:
  - Allow effective admin style management of users 
  - Groups can have policies attached to them (inline and managed) 
    - Additionally, users can  have their own inline policy too 
  - No limit for number of users in a single IAM group
  - Do not have a group that contains all users in the AWS account
  - No nesting (no groups within groups) 
  - Cannot be logged into 
  - 300 groups per account (but can be increased with support ticket)
  - Groups are not a true identify. They can't be referenced as a **principal** in a policy
  - **TL;DR Groups are a container for IAM users**

### Cloudwatch
- Overview:
  - Public service 
  - Store, monitor, and access logging data 
  - AWS integrations via IAM roles or service roles 
  - Can generic metrics based on logics - metric filter 
  - Non-API calls
- Architecture:
  - Regional service

### Cloudtrail 
- Logs API calls/activities as a cloudtrail event
- 90 days stores by default in Event History
- enabled by default - no cost for 90 day history 
- to customize the service.. create 1 or more trails. Types:
  - **Management** Events (making,stopping, etc. a service), or 
  - **Data** Events (objects uploaded/access to S3)
- CloudTrail is a regional service 
  - Only logs events for that region 
  - Global services can log to one region by default (us-east-1), but need to turn on listening to global services 
  - If they're regional services, then those logs will be logged in the region that the service is in 
  - Can create an all-regions trail 
- How to store data past 90 days:
  - Logs can be stored (in JSON) in S3 past the 90 days
  - CloudWatch logs (this gives a lot more advanced insight into digesting the data)
- Organizational trail 
> - **Exam Powerup:**
>  - Enabled by default on an AWS account, but only 90 day default. No s3 storage
>  - Trails are how you configure S3 and CWLogs
>  - Only management events are logged by default. Data events need to logged specifically (and additional pricing)
>  - IAM, STS, CloudFront - are global service events, so their data gets logged into us-east-1 and need to enable 
>    logging for them
>  - Not realtime - there is a delay (logs within 15 minutes)
