# aws-notes

Some tips / things of notes for myself while I'm learning AWS

## AWS User Accounts and Best Practices

### AWS Root User

- The **root user** is created when you first set up an AWS account. It is tied to the email address used to create the account.
- The root user has **full, unrestricted access** to all resources in the AWS account.
- The root user credentials (email and password) are separate from IAM users and are used to log in to the AWS Management Console.

#### What is the root user for?

- The root user is intended for **account setup and emergency tasks only**.
- Examples of root-only tasks:
  - Changing account settings (e.g., billing info, closing account)
  - Restoring IAM user access
  - Managing some security credentials (e.g., root access keys)

#### Should the root user spin up servers or manage resources?

- **No.** The root user should **not** be used for day-to-day operations, including spinning up servers, managing S3 buckets, or configuring services.
- Instead, create **IAM users** with the necessary permissions for regular tasks.

### Setting Up Users Correctly

1. **Enable Multi-Factor Authentication (MFA) on the root account**

   - This is the most important security step. Use a hardware or virtual MFA device.

2. **Create individual IAM users for each person or application**

   - Never share user accounts or credentials.
   - Assign each user only the permissions they need (principle of least privilege).

3. **Use Groups and Roles**

   - Assign permissions to groups, then add users to groups for easier management.
   - Use IAM roles for applications, services, or cross-account access.

4. **Disable or delete root access keys**

   - If you must create root access keys, delete them as soon as possible.
   - Use IAM user access keys for programmatic access.

5. **Monitor and audit account activity**

   - Enable AWS CloudTrail to log all account activity.
   - Regularly review IAM policies and user activity.

6. **Rotate credentials regularly**

   - Change passwords and access keys on a regular schedule.

7. **Use Service Control Policies (SCPs) with AWS Organizations**
   - For multi-account setups, use SCPs to enforce permission boundaries.

### Best Practices Summary

- **Root user = emergency only**
- **MFA on root and all users**
- **No root access keys**
- **Use IAM users, groups, and roles**
- **Principle of least privilege**
- **Monitor and audit regularly**

For more details, see the official AWS documentation on [Best Practices for Managing AWS Access Keys](https://docs.aws.amazon.com/general/latest/gr/aws-access-keys-best-practices.html) and [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html).

## IAM Users

**Identity and Access Management (IAM) users** are entities that represent a person or application that interacts with AWS resources. Unlike the root user, IAM users are created within your AWS account and have specific permissions assigned to them.

### What are IAM Users?

- **Individual identities** for people or applications that need access to AWS
- **Separate from the root user** - they have their own username and password
- **Granular permissions** - you control exactly what they can and cannot do
- **Programmatic access** - can have access keys for API/CLI usage
- **Console access** - can log into the AWS Management Console

### How to Set Up IAM Users

#### 1. Create a New IAM User

**Via AWS Console:**

1. Go to IAM service in AWS Console
2. Click "Users" → "Add user"
3. Enter username and select access type:
   - **Console access**: For web-based access
   - **Programmatic access**: For API/CLI access
   - **Both**: For full access

**Via AWS CLI:**

```bash
# Create user
aws iam create-user --user-name myuser

# Create access key (for programmatic access)
aws iam create-access-key --user-name myuser

# Create login profile (for console access)
aws iam create-login-profile --user-name myuser --password MyPassword123!
```

#### 2. Assign Permissions

**Option A: Attach Managed Policies**

- Use AWS pre-built policies (e.g., `AmazonS3FullAccess`, `AmazonEC2ReadOnlyAccess`)
- Quick and easy, but may grant more permissions than needed

**Option B: Create Custom Policies**

- Write JSON policies with exact permissions needed
- More secure, follows principle of least privilege

**Option C: Add to Groups**

- Create groups with specific permissions
- Add users to groups for easier management

#### 3. Set Up Security

**Enable MFA:**

- Require multi-factor authentication for console access
- Use virtual MFA apps (Google Authenticator, Authy) or hardware devices

**Password Policy:**

- Set minimum length, complexity requirements
- Require password changes periodically

**Access Key Rotation:**

- Regularly rotate access keys (every 90 days recommended)
- Delete unused access keys

### IAM User Best Practices

1. **Use descriptive usernames** (e.g., `john.doe`, `webapp-service`)
2. **One user per person/application** - never share accounts
3. **Start with minimal permissions** - add more as needed
4. **Use groups for permission management** - easier to maintain
5. **Enable MFA for all users** with console access
6. **Regular access reviews** - remove unused users and permissions
7. **Use IAM Access Analyzer** to identify unused permissions
8. **Monitor user activity** with CloudTrail

### Example: Creating a Developer User

```bash
# Create user
aws iam create-user --user-name developer

# Add to developers group
aws iam add-user-to-group --user-name developer --group-name developers

# Create access key
aws iam create-access-key --user-name developer

# Create login profile
aws iam create-login-profile --user-name developer --password TempPass123!
```

### Common IAM Policies

- **AdministratorAccess**: Full access (use sparingly)
- **PowerUserAccess**: Full access except IAM management
- **ReadOnlyAccess**: View-only access to all services
- **AmazonS3FullAccess**: Full S3 access
- **AmazonEC2FullAccess**: Full EC2 access
- **AmazonRDSFullAccess**: Full RDS access

### Security Checklist

- [ ] MFA enabled for console access
- [ ] Strong password policy configured
- [ ] Access keys rotated regularly
- [ ] Unused users and permissions removed
- [ ] CloudTrail enabled for audit logging
- [ ] IAM Access Analyzer used to find unused permissions
- [ ] Users added to appropriate groups
- [ ] Custom policies used instead of broad managed policies when possible

## Access Keys

- Access Keys are a **key and secret** required to have programmatic access to AWS resources when interacting with the AWS API **outside of the AWS Management Console**

- An Access Key is commonly referred to as **AWS Credentials** (this term includes both keys and secrets)

  - You get both Key and Secret when you generate credentials

- A user must be **granted access** to use Access Keys

  - When you create a new user within your AWS account, you get to choose "do they have programmatic access?"
    - If they do then you can generate AWS Acces Keys for them

- You can have two active Access Keys

  - If you need more you need to deactivate or delete one

- **Access Key have whatever access a user has to AWS resources**

  - If you are an admin user, your access keys will have admin user privileges.
  - Cannot set seperate permissions for individual access keys
    - If you need this, look into setting up a machine user

- **Never share your AWS credentials**
  - Never accidentally commit them to a codebase (e.g. GitHub, GitLab, etc.)

### How to Save Access Keys Locally?

There are 2 ways to set access keys locally.

1. You can put them in a credentials file
2. You can create an environment variable

#### Creating a Credentials File

Access keys are stored in `~/.aws/credentials` and follow a INI file format (similar to a TOML file format)

```ini
[default]
aws_access_key_id = AKIAEXAMPLEDEFAULT
aws_secret_access_key = abc123exampleSecretKeyDefault

[user123]
aws_access_key_id = AKIAEXAMPLEUSER123
aws_secret_access_key = abc123exampleSecretKeyUser123
regin = ca-central-1
```

`default` will be the access key used when no profile is specified.

You can store multiple access keys by giving the profile names (e.g. `user123`)

You can use the `aws configure` CLI command to populate the credentials file. This will automatically create the INI file.

```bash
$ aws configure
AWS Access Key ID [None]: AKIAEXAMPLE123456
AWS Secret Access Key [None]: abc123exampleSecretKey
Default region name [None]: us-east-1
Default output format [None]: json
```

#### Creating an Environment Variable

You can also set your credentials using environmental variables

```bash
export AWS_ACCESS_KEY_ID=AKIAEXAMPLE123456
export AWS_SECRET_ACCESS_KEY=abc123exampleSecretKey
export AWS_DEFAULT_REGION=us-east-1
```

The AWS SDK and CLI will automatically read from these environmental variables. **Environmental variables are a good way to set credentials especially on cloud developer environments where you can't set a credentials file.**

## AWS CLI Autoprompt

AWS CLI **Auto-prompt** is an interactive feature that helps you build commands step-by-step.

### How It Works

- Type `aws` followed by a service name and press Tab
- CLI will prompt you for required parameters
- Provides suggestions and validates input
- Helps prevent syntax errors

### Enabling Auto-prompt

```bash
# Enable for current session
export AWS_CLI_AUTO_PROMPT=on-partial

# Enable for specific command
aws s3 ls --cli-auto-prompt on-partial
```

### Auto-prompt Modes

- **off**: Disabled (default)
- **on**: Full auto-prompt
- **on-partial**: Auto-prompt only when you press Tab

### Benefits

- Faster command building
- Learn AWS CLI syntax interactively
- Reduce typos and errors
- Discover available options and parameters

## AWS Free Tier

The **AWS Free Tier** provides free access to many AWS services for 12 months after account creation, plus some services that are always free.

### What's Included in Free Tier

#### Always Free Services (No Time Limit)

- **AWS Lambda**: 1M requests per month, 400,000 GB-seconds
- **AWS DynamoDB**: 25GB storage, 25 WCU/RCU
- **AWS CloudWatch**: 10 custom metrics, 1M API requests
- **AWS SNS**: 1M publishes, 1M HTTP/HTTPS deliveries
- **AWS SQS**: 1M requests per month
- **AWS CodeBuild**: 100 build minutes per month
- **AWS CodeCommit**: 5 active users, 50GB storage
- **AWS CloudFormation**: 1,000 handler operations per month

#### 12-Month Free Tier Services

- **EC2**: 750 hours/month of t2.micro or t3.micro instances
- **S3**: 5GB storage, 20,000 GET requests, 2,000 PUT requests
- **RDS**: 750 hours/month of db.t2.micro instances
- **ElastiCache**: 750 hours/month of cache.t2.micro nodes
- **CloudFront**: 1TB data transfer out
- **API Gateway**: 1M API calls per month
- **Elastic Load Balancer**: 750 hours/month
- **EBS**: 30GB storage, 2M I/O operations
- **SES**: 62,000 outbound messages per month

### Important Free Tier Considerations

#### Usage Limits

- **Time-based**: Most services have monthly usage limits
- **Regional**: Free tier applies per region
- **Account-based**: One free tier per AWS account
- **Automatic billing**: You'll be charged if you exceed limits

#### Common Gotchas

- **Data Transfer**: Outbound data transfer beyond free tier is charged
- **Storage**: S3 storage beyond 5GB is charged
- **Snapshots**: EBS snapshots beyond free tier are charged
- **Load Balancers**: Running 24/7 can exceed free tier quickly
- **RDS**: Multi-AZ deployments are not free tier eligible

#### Best Practices for Free Tier

1. **Set up billing alerts**

   ```bash
   # Create billing alarm via CLI
   aws cloudwatch put-metric-alarm \
     --alarm-name "FreeTierAlert" \
     --alarm-description "Alert when approaching free tier limits" \
     --metric-name EstimatedCharges \
     --namespace AWS/Billing \
     --statistic Maximum \
     --period 86400 \
     --threshold 5 \
     --comparison-operator GreaterThanThreshold
   ```

2. **Monitor usage regularly**

   - Check AWS Cost Explorer
   - Review monthly billing statements
   - Use AWS Budgets for spending limits

3. **Clean up unused resources**

   - Terminate EC2 instances when not needed
   - Delete unused EBS volumes
   - Remove old S3 objects
   - Stop RDS instances during development

4. **Use appropriate instance types**
   - Stick to t2.micro for EC2
   - Use db.t2.micro for RDS
   - Choose free tier eligible services

### Free Tier Calculator

Use the [AWS Pricing Calculator](https://calculator.aws/) to estimate costs beyond free tier limits.

### Free Tier Expiration

- **12 months** from account creation date
- **No automatic notification** when free tier expires
- **Immediate billing** starts when free tier ends
- **Always free services** continue indefinitely

### Tips for Staying Within Free Tier

1. **Start small**: Begin with minimal resources
2. **Use spot instances**: For non-critical workloads
3. **Optimize storage**: Use appropriate storage classes
4. **Monitor closely**: Set up CloudWatch alarms
5. **Document usage**: Track what you're using
6. **Plan ahead**: Know when free tier expires

### Other AWS resources

- **AWS Educate**: For students (requires .edu email)
- **AWS Activate**: For startups
- **AWS Partner Network**: For partners
- **Regional pricing**: Some regions have lower costs

## AWS S3

**Simple Storage Service (S3)** is AWS's object storage service.

### Key Concepts

- **Buckets**: Containers for storing objects (files)

  - Must have globally unique names across all AWS accounts
  - Can be organized by region
  - Support versioning for data protection

- **Objects**: The actual files/data stored in S3
  - Can be up to 5TB in size
  - Include metadata and data
  - Have unique keys (file paths) within a bucket

### Storage Classes

- **S3 Standard**: High availability, immediate access
- **S3 Standard-IA**: Infrequent access, lower cost
- **S3 One Zone-IA**: Single AZ, lowest cost for infrequent access
- **S3 Glacier**: Long-term archival, 3-5 hour retrieval
- **S3 Glacier Deep Archive**: Lowest cost, 12-48 hour retrieval

### Common Use Cases

- Static website hosting
- Data backup and archival
- Content distribution (with CloudFront)
- Data lakes and analytics
- Application assets and media files

### Security Features

- **Bucket Policies**: JSON policies for bucket-level permissions
- **Object ACLs**: Fine-grained object permissions
- **Encryption**: Server-side encryption (SSE-S3, SSE-KMS, SSE-C)
- **Access Points**: Custom endpoints with specific permissions

## AWS EC2

**Elastic Compute Cloud (EC2)** provides resizable compute capacity in the cloud.

### Instance Types

- **General Purpose**: Balanced compute, memory, and networking (M, T series)
- **Compute Optimized**: High-performance processors (C series)
- **Memory Optimized**: Large memory workloads (R, X series)
- **Storage Optimized**: High I/O and storage (I, D series)
- **Accelerated Computing**: GPU/FPGA workloads (P, G, F series)

### Instance Lifecycle

1. **Launch**: Create and start an instance
2. **Start**: Resume a stopped instance
3. **Stop**: Shutdown OS and stop billing (data preserved)
4. **Terminate**: Delete instance and data (cannot be recovered)

### Key Features

- **Auto Scaling**: Automatically adjust capacity based on demand
- **Load Balancing**: Distribute traffic across multiple instances
- **Elastic IPs**: Static public IP addresses
- **Security Groups**: Virtual firewalls for instances
- **Placement Groups**: Control instance placement for performance

### Pricing Models

- **On-Demand**: Pay per second/hour, no commitment
- **Reserved Instances**: 1-3 year commitment, significant discount
- **Spot Instances**: Use unused capacity, up to 90% discount
- **Savings Plans**: Flexible commitment for consistent usage

## AWS EBS

**Elastic Block Store (EBS)** provides persistent block storage volumes for EC2 instances.

### Volume Types

- **General Purpose SSD (gp2/gp3)**: Balanced price/performance
- **Provisioned IOPS SSD (io1/io2)**: High-performance workloads
- **Throughput Optimized HDD (st1)**: Big data, data warehouses
- **Cold HDD (sc1)**: Lowest cost, infrequent access

### Key Features

- **Snapshots**: Point-in-time backups stored in S3
- **Encryption**: Automatic encryption using AWS KMS
- **Multi-Attach**: Attach single volume to multiple instances (io1/io2 only)
- **Fast Snapshot Restore**: Instant restore from snapshots

### Best Practices

- Use snapshots for backup and disaster recovery
- Monitor volume performance with CloudWatch
- Choose appropriate volume type based on workload
- Consider using EBS-optimized instances for high I/O workloads

## AWS AMI

**Amazon Machine Image (AMI)** is a template containing software configuration for an instance.

### AMI Types

- **Public AMIs**: Available to all AWS users
- **Private AMIs**: Only available to your account
- **Marketplace AMIs**: Third-party software with licensing
- **Community AMIs**: Shared by AWS community

### AMI Components

- **Root Volume**: Operating system and applications
- **Launch Permissions**: Who can use the AMI
- **Block Device Mapping**: How volumes are attached
- **Kernel/Ramdisk**: Boot configuration (if needed)

### Creating AMIs

1. **From Running Instance**: Create AMI from existing instance
2. **From Snapshot**: Create AMI from EBS snapshot
3. **From S3**: Import VM from S3 (for on-premises migration)

### Best Practices

- Keep AMIs updated with security patches
- Use descriptive names and tags
- Test AMIs before sharing or using in production
- Consider using AWS Systems Manager for automated AMI management

## AWS Root Device Type

The **Root Device Type** determines how the root volume is stored and managed.

### Types

- **EBS-backed**: Root volume is an EBS volume

  - Can be stopped and started (data persists)
  - Supports EBS snapshots
  - Can be resized
  - Better for production workloads

- **Instance Store-backed**: Root volume is ephemeral storage
  - Cannot be stopped (only terminated)
  - Data is lost when instance stops/terminates
  - Higher I/O performance
  - Lower cost
  - Good for temporary workloads

### Key Differences

| Feature          | EBS-backed | Instance Store-backed |
| ---------------- | ---------- | --------------------- |
| Data Persistence | Yes        | No                    |
| Stop/Start       | Yes        | No                    |
| Snapshot Support | Yes        | No                    |
| Volume Resize    | Yes        | No                    |
| Performance      | Good       | Better                |
| Cost             | Higher     | Lower                 |

### Choosing the Right Type

- **Use EBS-backed for**:

  - Production workloads
  - Data that needs to persist
  - Instances that need to be stopped/started
  - When you need snapshots

- **Use Instance Store-backed for**:
  - Temporary workloads
  - High-performance requirements
  - Cost-sensitive applications
  - When data persistence isn't required
