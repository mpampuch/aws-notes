# aws-notes

Some tips / things of notes for myself while I'm learning AWS

# AWS Infrastructure

AWS infrastructure is organized into a global network of **regions** and **availability zones** that provide high availability, fault tolerance, and scalability.

### AWS Regions

**Regions** are geographic areas where AWS has data centers. Each region is completely independent and isolated from other regions.

#### Key Characteristics

- **Geographic separation**: Regions are located in different geographic areas
- **Independent infrastructure**: Each region has its own power, cooling, and networking
- **Compliance**: Different regions may have different compliance certifications
- **Latency**: Choose regions close to your users for lower latency
- **Pricing**: Costs can vary significantly between regions

#### US-EAST-1 (North Virginia) - The First Region

**US-EAST-1** holds special significance in AWS infrastructure:

- **First AWS region**: Launched in 2006, it was the original AWS region
- **Largest region**: Contains the most services and features
- **Billing headquarters**: All AWS billing information appears in US-EAST-1
- **Service launchpad**: New AWS services are typically launched in US-EAST-1 first
- **Global services**: Some AWS services are only available in US-EAST-1

#### Regional Service Availability

**Not all AWS services are available in every region:**

- **Global services**: Available in all regions (IAM, CloudFront, Route 53)
- **Regional services**: Available in most regions (EC2, S3, RDS)
- **Limited availability**: Some services are only in specific regions
- **New services**: Often launch in US-EAST-1 first, then expand to other regions

**Example of regional differences:**

- **EC2**: Available in all regions
- **S3**: Available in all regions
- **Lambda**: Available in most regions
- **SageMaker**: Limited to specific regions
- **Ground Station**: Only available in certain regions

### Availability Zones (AZs)

**Availability Zones** are isolated locations within a region, each consisting of one or more discrete data centers.

All Availability Zones in an AWS Region are interconnected with high-bandwidth, low-latency networking, over fully redundant, dedicated metro fiber providing high-throughput, low-latency networking between them.

#### Key Characteristics

- **Physical separation**: AZs are physically separated from each other
- **Independent infrastructure**: Each AZ has independent power, cooling, and networking
- **Low latency**: AZs within a region are connected via high-speed, low-latency links
- **Fault isolation**: Failures in one AZ don't affect other AZs
- **Standard configuration**: Most regions contain 3 availability zones
- **Encryption**: All traffic between AZs is encrypted

#### AZ Structure

Availability Zones are designated by taking the region name and suffixing it with a letter. For example:

The region `ca-central-1` has 3 availability zones: `ca-central-1a`, `ca-central-1b`, and `ca-central-1c`

This can be visualized more in-depth as so:

```
Region (e.g., US-EAST-1)
├── Availability Zone A (us-east-1a)
│   ├── Data Center 1
│   └── Data Center 2
├── Availability Zone B (us-east-1b)
│   ├── Data Center 1
│   └── Data Center 2
└── Availability Zone C (us-east-1c)
    ├── Data Center 1
    └── Data Center 2
```

#### AZ Naming Convention

- **Format**: `region-identifier` (e.g., `us-east-1a`, `eu-west-1b`)
- **Not consistent**: The same letter doesn't always map to the same physical AZ across accounts
- **Account-specific**: AWS maps AZ names differently for each account for load balancing

### AWS Management Console Regional Scoping

**The AWS Management Console is scoped to a selected region:**

- **Region selector**: Located in the top-right corner of the console
- **Service-specific**: Some services show resources from all regions
- **Resource visibility**: Most resources are only visible in their home region
- **Cross-region operations**: Some services allow cross-region operations

#### Console Behavior

- **EC2**: Shows instances only in the selected region
- **S3**: Shows buckets from all regions (global service)
- **IAM**: Shows users and policies (global service)
- **RDS**: Shows databases only in the selected region

### Best Practices for Region Selection

#### Choose Regions Based On:

1. **Latency**: Select regions close to your users
2. **Compliance**: Choose regions that meet your regulatory requirements
3. **Service availability**: Ensure required services are available
4. **Cost**: Compare pricing across regions
5. **Disaster recovery**: Use multiple regions for high availability

#### Multi-Region Strategies

- **Primary region**: Main deployment region
- **Secondary region**: Disaster recovery or backup
- **Edge locations**: Use CloudFront for global content delivery
- **Data residency**: Store data in specific regions for compliance

### Regional Service Launch Pattern

**AWS typically follows this pattern when launching new services:**

1. **US-EAST-1**: First region to get new services
2. **US-WEST-2**: Second region (Oregon)
3. **EU-WEST-1**: European region
4. **AP-SOUTHEAST-1**: Asia-Pacific region
5. **Other regions**: Gradually expand to all regions

### AWS China - Special Considerations

**AWS China operates as a separate entity from global AWS**.

AWS China is the AWS cloud offerings in Mainland China. AWS China is completely isolate intentionally from AWS global to meet regulatory compliance in Mainland China. That means **if you make a workload on AWS Global, you cannot operate in China**

AWS China is on it's own domain at `amazonaws.cn`

In order to operate in AWS China Region, you need a Chinese Business License (ICP license).

Not all services (e.g. Route 53) are available in China.

Running in Mainland China (instead of Singapore) means you would not need to traverse the Great Firewall

- **Separate accounts**: AWS China requires separate AWS accounts from global AWS
- **Different credentials**: Cannot use global AWS credentials in China regions
- **Local partnership**: Operated by local Chinese companies (Sinnet in Beijing, NWCD in Ningxia)
- **Compliance requirements**: Must comply with Chinese data sovereignty laws
- **Limited services**: Not all AWS services are available in China regions
- **Network isolation**: China regions are not connected to global AWS regions

#### China Regions

- **cn-north-1**: Beijing (operated by Sinnet)
- **cn-northwest-1**: Ningxia (operated by NWCD)

#### Key Differences

- **Account separation**: Cannot access China regions with global AWS accounts
- **Service availability**: Limited service portfolio compared to global regions
- **Data residency**: Data must remain within China for compliance
- **Pricing**: Different pricing structure from global AWS
- **Documentation**: Separate documentation and support channels

#### When to Use AWS China

- **Chinese market entry**: Applications targeting Chinese users
- **Compliance requirements**: When data must stay within China
- **Local partnerships**: Working with Chinese companies
- **Performance**: Lower latency for users in China

### Infrastructure Summary

```
Global AWS Infrastructure
├── Regions (Geographic areas)
│   ├── US-EAST-1 (North Virginia) - First region
│   ├── US-WEST-2 (Oregon)
│   ├── EU-WEST-1 (Ireland)
│   └── ... (20+ regions worldwide)
│
└── Availability Zones (Per region)
    ├── AZ A (us-east-1a)
    ├── AZ B (us-east-1b)
    └── AZ C (us-east-1c)
```

### Important Considerations

- **Data transfer costs**: Moving data between regions incurs charges
- **Service dependencies**: Some services may require resources in the same region
- **Backup strategies**: Consider cross-region backups for critical data
- **Compliance requirements**: Some regulations require data to stay in specific regions
- **Disaster recovery**: Multi-region deployments provide better fault tolerance

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

## AWS Account ID

- **Every AWS account has a unique 12-digit Account ID.**
- You can find your Account ID by clicking on "My Account" in the AWS Management Console's global navigation bar.
- **Format:** 12 digits (e.g., 123456789012, 121212121212, 498241098510).

### Uses of the AWS Account ID

- Required when logging in as an IAM (non-root) user.
- Used for cross-account roles and permissions.
- Needed for AWS support cases.

### Security Note

- It is generally good practice to keep your Account ID private.
- The Account ID is one of several components that could be used by a malicious actor to target your account.

## Amazon Resource Names (ARNs)

- **ARNs (Amazon Resource Names)** uniquely identify AWS resources.
- ARNs are required to specify a resource unambiguously across all of AWS.

### ARN Format Variations

- The general format is:
  - `arn:partition:service:region:account-id:resource-id`
  - `arn:partition:service:region:account-id:resource-type/resource-id`
  - `arn:partition:service:region:account-id:resource-type:resource-id`
- **Partition**:
  - `aws` (standard regions)
  - `aws-cn` (China regions)
  - `aws-us-gov` (GovCloud regions)
- **Service**: Identifies the AWS service (e.g., `ec2`, `s3`, `iam`)
- **Region**: The AWS region (e.g., `us-east-1`, `ca-central-1`)
- **Account ID**: The 12-digit AWS account number
- **Resource ID**: The specific resource (could be a name, number, or path, e.g., `user/Bob`, `instance/i-1234567890abcdef0`)

### Example ARN

- S3 bucket: `arn:aws:s3:::my-bucket`

### Paths in ARNs

- Resource ARNs can include a path.
- Paths can include a wildcard character (`*`).
- **Examples:**
  - IAM Policy ARN Path:  
    `arn:aws:iam::123456789012:user/Development/product_1234/*`
  - S3 ARN Path:  
    `arn:aws:s3:::my_corporate_bucket/Development/*`

### Console Tip

- In the AWS Management Console, you can often copy the ARN for a resource directly to your clipboard.

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

## AWS Application Programming Interface (API)

### What is an API?

- An **API (Application Programming Interface)** is software that allows two applications or services to communicate with each other.
- The most common type of API is via HTTP/S requests.

### AWS API Basics

- AWS provides HTTP APIs for its services.
- You can interact with AWS APIs by sending HTTPS requests, often using tools like **Postman** or programmatically via SDKs/CLI.

### How Users Interact with AWS APIs

- **Rarely do users directly send HTTP requests to the AWS API.**
- It's much easier to interact with the API via a variety of developer tools:
  - **AWS Management Console**: A WYSIWYG web interface for interacting with AWS services.
  - **AWS CLI**: Command Line Interface for interacting with AWS via terminal/shell.
  - **AWS SDK**: Software Development Kits for interacting with AWS using your favorite programming language.
  - **Direct HTTP Request**: You can interact directly with the AWS API, but this is uncommon for most users.

### Service Endpoints

- **Each AWS service has its own Service Endpoint** (e.g., `monitoring.us-east-1.amazonaws.com` for CloudWatch in US-EAST-1).
- You send your API requests to these endpoints.

### Example API Request

```
GET / HTTP/1.1
host: monitoring.us-east-1.amazonaws.com
x-amz-target: GraniteServiceVersion20100801.GetMetricData
x-amz-date: 20180112T092034Z
Authorization: AWS4-HMAC-SHA256 Credential=...
Content-Type: application/json
Accept: application/json
Content-Encoding: amz-1.0
Content-Length: 45
Connection: keep-alive
```

### Authorization & Signed Requests

- To authorize your API requests, you must generate a **signed request** (using your AWS credentials, typically with AWS Signature Version 4).
- Sometimes, you make a separate request with your credentials to get a token.

### Actions and Payloads

- You must specify an **ACTION** (e.g., `GetMetricData`) and provide any required parameters in the payload.

### Key Points

- **Service Endpoint**: Each AWS service/region has a unique endpoint.
- **Signed Requests**: Required for authorization; use your AWS credentials.
- **Action**: Specify the operation you want to perform.
- **Tools**: You can use Postman, AWS Console, CLI, or SDKs to interact with AWS APIs for testing and learning.

However, **rarely do users directly send HTTP requests directly to the AWS API. Its much easier to interact with the API via a variety of Developer Tools**.

## AWS Developer Tools (Click Ops, CLI, and SDKs)

AWS provides multiple ways to interact with its services, each suited for different use cases and skill levels.

### AWS Management Console (Click Ops)

The **AWS Management Console** is a web-based graphical user interface for managing AWS resources.

It can be found here: [https://console.aws.amazon.com](https://console.aws.amazon.com)

> [!NOTE]
> For some reason, sometimes I have issues accessing this website on Google Chrome. On Safari I have no issues so I use Safari as of right now.

#### Pros

- **User-friendly**: No coding required
- **Visual**: Easy to see resource relationships
- **Interactive**: Real-time feedback and validation
- **Learning tool**: Great for understanding AWS concepts
- **Quick tasks**: Perfect for one-off operations

#### Cons

- **Manual**: Repetitive tasks are time-consuming
- **Error-prone**: Human mistakes in configuration
- **Not scalable**: Difficult to manage many resources
- **No version control**: Changes aren't tracked
- **Limited automation**: Can't be easily scripted

#### Best Use Cases

- Learning and exploration
- One-time setup tasks
- Troubleshooting and debugging
- Small-scale operations
- Quick configuration changes

### AWS Command Line Interface (CLI)

The **AWS CLI** is a command-line tool for managing AWS services from your terminal.

#### Installation and Setup

```bash
# Install AWS CLI
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# Configure AWS CLI
aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: json
```

#### Key Features

- **Cross-platform**: Works on Windows, macOS, Linux
- **JSON output**: Structured data for parsing
- **Profiles**: Multiple account configurations
- **Auto-prompt**: Interactive command building
- **Pagination**: Handle large result sets

#### Common Commands

```bash
# List S3 buckets
aws s3 ls

# Create EC2 instance
aws ec2 run-instances \
  --image-id ami-12345678 \
  --instance-type t2.micro \
  --key-name my-key-pair

# Copy files to S3
aws s3 cp local-file.txt s3://my-bucket/

# List EC2 instances
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress]' --output table
```

#### Pros

- **Automation**: Can be scripted and automated
- **Version control**: Commands can be saved in scripts
- **Consistent**: Same interface across all services
- **Powerful**: Advanced querying and filtering
- **CI/CD integration**: Works in pipelines

#### Cons

- **Learning curve**: Requires command-line knowledge
- **Verbose**: Long commands for complex operations
- **No visual feedback**: Harder to see relationships
- **Error handling**: Less user-friendly error messages

### AWS Software Development Kits (SDKs)

AWS provides **SDKs** for various programming languages to integrate AWS services into applications.

#### Supported Languages

- **Python**: boto3 (most popular)
- **JavaScript/Node.js**: AWS SDK for JavaScript
- **Java**: AWS SDK for Java
- **C#/.NET**: AWS SDK for .NET
- **Go**: AWS SDK for Go
- **Ruby**: AWS SDK for Ruby
- **PHP**: AWS SDK for PHP

#### Python Example (boto3)

```python
import boto3

# Create S3 client
s3 = boto3.client('s3')

# List buckets
response = s3.list_buckets()
for bucket in response['Buckets']:
    print(bucket['Name'])

# Upload file
s3.upload_file('local-file.txt', 'my-bucket', 'remote-file.txt')

# Create EC2 instance
ec2 = boto3.resource('ec2')
instance = ec2.create_instances(
    ImageId='ami-12345678',
    MinCount=1,
    MaxCount=1,
    InstanceType='t2.micro',
    KeyName='my-key-pair'
)
```

#### JavaScript Example

```javascript
const AWS = require("aws-sdk");

// Configure AWS
AWS.config.update({
  region: "us-west-2",
  accessKeyId: "YOUR_ACCESS_KEY",
  secretAccessKey: "YOUR_SECRET_KEY",
});

// Create S3 client
const s3 = new AWS.S3();

// List buckets
s3.listBuckets((err, data) => {
  if (err) console.log(err);
  else console.log(data.Buckets);
});
```

#### Pros

- **Programmatic**: Full control over AWS operations
- **Integration**: Seamless app integration
- **Error handling**: Robust error management
- **Type safety**: Compile-time checking (typed languages)
- **Testing**: Easy to unit test

#### Cons

- **Complexity**: Requires programming knowledge
- **Setup**: More initial configuration needed
- **Debugging**: Harder to troubleshoot
- **Dependencies**: Additional libraries to manage

### When to Use Each Tool

#### Use AWS Console When:

- Learning AWS services
- One-time configuration
- Troubleshooting issues
- Small-scale operations
- Quick exploration

#### Use AWS CLI When:

- Automating repetitive tasks
- CI/CD pipelines
- Server administration
- Bulk operations
- Scripting workflows

#### Use AWS SDKs When:

- Building applications
- Integrating AWS into code
- Complex business logic
- Production systems
- Custom automation

### Best Practices

1. **Start with Console**: Learn services visually first
2. **Graduate to CLI**: Automate common tasks
3. **Use SDKs for apps**: Integrate AWS into your code
4. **Combine tools**: Use the right tool for each job
5. **Version control**: Save CLI commands and SDK code
6. **Security**: Use IAM roles and temporary credentials
7. **Monitoring**: Log and monitor all operations

### Tools Comparison

| Feature        | Console | CLI     | SDK       |
| -------------- | ------- | ------- | --------- |
| Learning curve | Low     | Medium  | High      |
| Automation     | No      | Yes     | Yes       |
| Integration    | No      | Limited | Full      |
| Speed          | Fast    | Fast    | Fast      |
| Scalability    | Low     | High    | High      |
| Error handling | Good    | Basic   | Excellent |

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

### EBS Persistence During Instance Lifecycle

**Important Distinction: Stop vs Terminate**

**EC2 Instance Lifecycle:**

- **Stop/Start**: Instance is paused, EBS volumes persist ✅
- **Terminate/Recreate**: Instance is destroyed, EBS volumes are lost ❌

#### EBS Persistence During Stop/Start

**EBS volumes persist when EC2 instances are stopped and started:**

- **EBS volumes remain attached** to the instance when it's stopped
- **Data is preserved** on the EBS volume during stop/start cycles
- **Volume stays in the same AZ** where it was created
- **Only the public IP address changes** (unless using an Elastic IP)

**What Happens During Stop/Start:**

1. **Instance stops**: OS shuts down, but EBS volume remains attached
2. **Instance starts**: Same EBS volume is reattached to the restarted instance
3. **Data persists**: All files, applications, and data remain intact
4. **IP changes**: Public IP address may change (unless using Elastic IP)

#### EBS Persistence During Terminate/Recreate

**EBS volumes do NOT persist when EC2 instances are terminated and recreated:**

- **EBS volumes are deleted** when the instance is terminated (by default)
- **New instances get fresh EBS volumes** with no previous data
- **All data is lost** unless you manually detach volumes before termination
- **Snapshots** can be used to preserve data across termination cycles

**What Happens During Terminate/Recreate:**

1. **Instance terminates**: EBS volume is deleted along with the instance
2. **New instance created**: Fresh EBS volume is attached
3. **Data lost**: All previous data is gone
4. **Fresh start**: New instance starts with empty storage

#### Key Points

- **EBS-backed instances** can be stopped and started (data persists)
- **EBS-backed instances** lose data when terminated and recreated
- **Instance Store-backed instances** cannot be stopped (only terminated), and data is lost
- **EBS snapshots** provide additional backup protection across termination cycles
- **Elastic IPs** can be used to maintain the same public IP address
- **Volume detachment** can preserve EBS volumes during termination (manual process)

#### Example Scenarios

**Stop/Start Cycle (Data Persists):**

```bash
# Your instance has EBS storage with important data
# Stop the instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Start the instance (gets new public IP)
aws ec2 start-instances --instance-ids i-1234567890abcdef0

# Data on EBS volume remains intact
# Only the public IP address changed
```

**Terminate/Recreate Cycle (Data Lost):**

```bash
# Your instance has EBS storage with important data
# Terminate the instance (EBS volume is deleted)
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Create new instance (gets fresh EBS volume)
aws ec2 run-instances --image-id ami-12345678 --instance-type t2.micro

# Data is lost - new instance starts with empty storage
# Previous EBS volume and all data are gone
```

**Preserving Data During Termination (Manual Process):**

```bash
# Detach EBS volume before termination
aws ec2 detach-volume --volume-id vol-1234567890abcdef0

# Terminate instance (volume survives)
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Create new instance
aws ec2 run-instances --image-id ami-12345678 --instance-type t2.micro

# Attach preserved volume to new instance
aws ec2 attach-volume --volume-id vol-1234567890abcdef0 --instance-id i-newinstance --device /dev/sdf
```

### Best Practices

- Use snapshots for backup and disaster recovery
- Monitor volume performance with CloudWatch
- Choose appropriate volume type based on workload
- Consider using EBS-optimized instances for high I/O workloads

## AWS EFS

**Amazon Elastic File System (EFS)** is a fully managed, scalable, cloud-native NFS (Network File System) file storage service for use with AWS Cloud services and on-premises resources.

### Key Features

- **Fully Managed**: No servers to provision or manage.
- **Elastic**: Automatically scales storage up or down as files are added or removed.
- **Shared Access**: Multiple EC2 instances (across multiple Availability Zones) can access the same file system concurrently.
- **POSIX-Compliant**: Supports standard file system semantics (permissions, links, etc.).
- **High Availability & Durability**: Data is redundantly stored across multiple AZs.
- **Performance Modes**:
  - **General Purpose**: Default, suitable for most workloads.
  - **Max I/O**: For highly parallelized workloads (e.g., big data, media processing).
- **Throughput Modes**:
  - **Bursting**: Scales with storage size.
  - **Provisioned**: Set throughput independent of storage size.
- **Encryption**: Supports encryption at rest and in transit.
- **Lifecycle Management**: Automatically moves infrequently accessed files to a lower-cost storage class (EFS Infrequent Access).

### Common Use Cases

- Web serving and content management
- Home directories
- Application data storage
- Big data analytics
- Container storage (EKS, ECS)
- Lift-and-shift enterprise applications

### How EFS Works

- You create an EFS file system in a VPC.
- Mount targets are created in each AZ for high availability.
- EC2 instances (Linux) mount the file system using the NFSv4 protocol.
- Data is accessible from all instances with appropriate network and IAM permissions.

### Pricing

- Pay for storage used (GB/month).
- Separate pricing for Standard and Infrequent Access storage classes.
- Additional charges for provisioned throughput (if used).
- Data transfer within the same region is free.

### Security

- **VPC Security Groups**: Control network access to mount targets.
- **IAM Policies**: Control who can manage EFS resources.
- **Encryption**: At rest (using KMS) and in transit (TLS).

### Mounting EFS

```bash
# Install NFS client (Amazon Linux)
sudo yum install -y nfs-utils

# Create mount point
sudo mkdir /mnt/efs

# Mount EFS (replace fs-xxxx with your file system ID)
sudo mount -t nfs4 -o nfsvers=4.1 fs-xxxx.efs.<region>.amazonaws.com:/ /mnt/efs
```

- For persistent mounts, add an entry to `/etc/fstab`.

### Best Practices

- Use security groups to restrict access.
- Enable encryption for sensitive data.
- Use lifecycle management to save costs.
- Monitor usage with CloudWatch metrics.
- Use EFS Access Points for application-specific access permissions.

### Limitations

- Only supports Linux-based clients (no native Windows support).
- Not suitable for high IOPS, low-latency workloads (use EBS for those).
- Maximum file size: 47.9 TiB.

### References

- [AWS EFS Documentation](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html)
- [EFS Pricing](https://aws.amazon.com/efs/pricing/)

## EFS vs EBS Comparison

AWS provides two different storage solutions: **Elastic File System (EFS)** and **Elastic Block Store (EBS)**. Understanding their differences helps you choose the right storage for your workload.

### Key Differences

| Feature         | EFS                                          | EBS                      |
| --------------- | -------------------------------------------- | ------------------------ |
| **Type**        | File system (NFS)                            | Block storage            |
| **Access**      | Network-based (NFS)                          | Direct attached          |
| **Sharing**     | Multiple instances can access simultaneously | Single instance only     |
| **Protocol**    | NFSv4                                        | Block-level              |
| **OS Support**  | Linux only                                   | Linux and Windows        |
| **Scalability** | Automatic scaling                            | Manual resizing required |
| **Performance** | Network latency                              | Direct connection        |
| **Use Case**    | Shared file storage                          | Instance storage         |

### When to Use EFS

**Use EFS when you need:**

- **Shared access** across multiple EC2 instances
- **File system semantics** (permissions, links, etc.)
- **Automatic scaling** without manual intervention
- **Cross-AZ availability** for high availability
- **Container storage** for EKS/ECS workloads
- **Web serving** with multiple web servers
- **Content management systems** with shared storage
- **Big data analytics** with shared datasets

**Example scenarios:**

- Multiple web servers serving the same content
- Container applications needing shared storage
- Development teams sharing code repositories
- Analytics workloads processing shared datasets

### When to Use EBS

**Use EBS when you need:**

- **High performance** with low latency
- **Database storage** (RDS, self-managed databases)
- **Boot volumes** for EC2 instances
- **Single-instance storage** requirements
- **Windows workloads** (EFS doesn't support Windows)
- **High IOPS** workloads (databases, applications)
- **Point-in-time snapshots** for backup
- **Instance-specific data** that doesn't need sharing

**Example scenarios:**

- Database storage for MySQL, PostgreSQL, etc.
- Application servers with local storage needs
- Boot volumes for EC2 instances
- High-performance applications requiring low latency

### Performance Comparison

| Metric          | EFS                                | EBS                         |
| --------------- | ---------------------------------- | --------------------------- |
| **Latency**     | Higher (network-based)             | Lower (direct connection)   |
| **IOPS**        | Bursting or provisioned            | Configurable (gp3, io1/io2) |
| **Throughput**  | Scales with storage or provisioned | Based on volume type        |
| **Consistency** | Strong consistency                 | Strong consistency          |

### Cost Comparison

**EFS Pricing:**

- Pay per GB stored
- Separate pricing for Standard vs Infrequent Access
- Additional cost for provisioned throughput
- No charges for data transfer within region

**EBS Pricing:**

- Pay per GB provisioned (not used)
- Different pricing for volume types (gp3, io1, etc.)
- Additional cost for provisioned IOPS
- Snapshots stored in S3 (additional cost)

### Migration Considerations

**From EBS to EFS:**

- Use AWS DataSync for large migrations
- Consider application compatibility (NFS vs block)
- Test performance impact of network latency
- Update application mount points

**From EFS to EBS:**

- Use rsync or similar tools for data migration
- Consider data sharing requirements
- Plan for manual volume management
- Test application performance improvements

### Best Practices

**EFS Best Practices:**

- Use EFS Access Points for application-specific permissions
- Enable lifecycle management for cost optimization
- Use appropriate performance mode (General Purpose vs Max I/O)
- Monitor CloudWatch metrics for performance

**EBS Best Practices:**

- Choose appropriate volume type for workload
- Use snapshots for backup and disaster recovery
- Monitor volume performance and resize as needed
- Use EBS-optimized instances for high I/O workloads

### Hybrid Approach

You can use both EFS and EBS in the same architecture:

- **EBS** for high-performance, instance-specific storage (databases, boot volumes)
- **EFS** for shared file storage (web content, user uploads, logs)
- **S3** for long-term archival and backup

This approach gives you the benefits of each storage type where they're most appropriate.

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
