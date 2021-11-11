# Course Fundamentals and AWS Accounts

### AWS Accounts - the basics:
- An AWS account can be seen as a container for identities (a user) and resources.
- To make an account, you'll need a name, unique email, and payment option to create a new account. 
- This email is used to make the AWS account root user of this AWS account. 
- Many users can share one AWS account by becoming a user under an AWS account. This is done via IAM (eye-am). All
    accounts can be given minimal, partial, or full permissions depending on the level of permission set by the account.
- All users added to an AWS account are, by default, given no permissions. Any updates must be set up via IAM.
- AWS is very good at containing damage within an individual AWS account. For example, if something goes haywire in one
    account, then another account will not be impacted. Best practice is to create separate accounts
    for different environments (e.g. dev, prod, testing, etc.) and/or projects. 
- AWS accounts are also very good at keeping people out of accounts because AWS accounts and resources are, by default, 
    denied except for account root users.

### Multi-Factor Auth
- Traditionally there's only username and passwords to login to an account.
- To prevent these two from getting leaked, there's additional factors added:
  - Knowledge: something you know e.g. usernames, passwords, PINs
  - Possession: something you have e.g. bank card, MFA device/app
  - Inherent: Something you are e.g. fingerprint, face, voice, iris
  - Location: A location (physical) or network (corporate or wifi)
- **Goal**: achieve balance between convenience and security 

### Setting up a new AWS account
1. Create a new account (Use the Gmail '+' trick)
2. Set up an MFA (My Security Credentials > MFA)
3. Add a Billing Alert 
4. Create a new Billing Alarm in Cloudwatch 
   1. Navigate to Cloudwatch 
   2. On the left, click All Alarms and Create Alarm
   3. Click Select Metric
   4. At the bottom, select "Billing" and "Total Estimated Charge"
   5. Check the box next to "USD" and then press "Select Metric" in orange in the bottom right
   6. On the first screen, keep everything the same, then enter an alert amount (I put $5), then press Next
   7. On the next screen under Notifications, select "In Alarm" and "
      Create new topic". Name is "BillingAlert" and enter in a target email. Press Create Topic.
   8. Enter in a name for the alarm (I also put "BillingAlert")
   9. Then on the last screen, press Create Alarm on the bottom right. 
   10. Afterwards, check the email you'd entered in step #7 for a confirmation link to receive alarms. 
5. Enable IAM User & Role Access to Billing (My Account > Scroll down)

### Identity and Access Management (IAM) Basics
- Least privileged access: only give permissions to do a job or perform a task 
- Cannot revoke the permissions of a Root User
- Every AWS account comes with its own IAM (its own db/instance of IAM). Global resilient service, so any data is 
    always secure across all AWS regions. 
- Inside IAM, you can create many identities. 
- IAM lets us create 3 different type of identity objects:
  - IAM Users: 
    - Identities which represent **humans** or **applications** that need access to your account
    - Use this when can identity the individual thing that will log into that user (e.g. individual user or app)
  - IAM Groups
    - Collection of related users (e.g. dev team, finance, or HR)
  - IAM Roles
    - Can be used by AWS services or external access to your account
    - Generally used to grant access to services to an **uncertain** amount of entities 
- IAM Policy:
  - Objects or documents to allow or deny access to AWS services **only** when they are attached to users, groups, 
    or roles
- IAM's main jobs:
  - Manage Identities: an ID Provider (IDP)
    - Create, modify, and delete identities such as users and roles
    - Prove you are who you claim to be 
  - Authenticates identities
    - Anytime want to make request to AWS, they are identity principle. Users need to prove their identity
  - Authorize
    - After providing identity, IAM then authorizes and allows or denies access to resources 
- Things to note:
  - IAM is provided for free. No cost with creating users, groups, roles
  - Global service/Global resilience
    - 1 global db for your account 
    - can cope with failure of large failures of AWS infra
  - Allow or deny its identities on its AWS account
  - No direct control on external accounts or users
  - Identity federation and MFA 

### IAM Access Keys
- They are a type of long term credentials within AWS. Do not change regularly or rotate
  - The user must change them manually 
- As a user, you can use the following to access AWS:
  - user and password 
  - access key via command line interface
- Username/password vs Access Keys
  - ISM user has 1 username and 1 password (password is optional). Cannot have more than 1 user or 1 password at a time
    - username = public; password = private 
  - IAM user can have two access keys 
    - They can be created, delete, made inactive, or made active. Default = active 

Access Keys:
- AWS gives 
  - Access Key ID
  - Secret Access Key 
- Once you see the secret access key once, then you won't be able to access it again
- Note down the secret access key and store it safely & securely 
- Command line associate uses both of these to sign the request being sent to AWS. 
  - AWS keeps the public part, thus they'll compare again the private one so AWS can verify the request
- If need to recreate the access keys for any reason, then need to make new Access Key ID and Secret Access Key
- Rotating access keys = create a second set, update all applications + command line with new set, then delete the 
   old set 
  - **Note:** Each identity can have up to 2 pairs of access keys at a time (regardless of active or inactive) 
- IAM users are the only ones that user IAM access keys. IAM roles do not use access keys

### Creating Access Keys and setting up AWS CLI
```
$ aws configure
```
- This command sets up the default profile for the AWS CLI. Otherwise, you can specify a named profile 
```
$ aws configure --profile <name_of_profile>
```
- The above creates a profile under a specified named profile 