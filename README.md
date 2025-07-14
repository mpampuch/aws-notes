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
  - A good practice is to create an IAM user with privileges to change other IAM user's privileges, and to avoid doing it from the Root user.
  - I like to change the policies from the AWS management console. How I do this is I go to `Identity and Access Management (IAM)` -> `Users` -> `Permissions policies` -> `Add Permissions` -> `Create Inline Policy` -> `Policy Editor: JSON` and then I add my policies using the JSON format there. An example of this is as follows:
 
```json
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":[
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:GetRole",
        "iam:PutRolePolicy",
        "iam:DeleteRolePolicy",
        "iam:AttachRolePolicy",
        "iam:DetachRolePolicy",
        "iam:PassRole",
        "iam:CreateInstanceProfile",
        "iam:DeleteInstanceProfile",
        "iam:GetInstanceProfile",
        "iam:AddRoleToInstanceProfile",
        "iam:RemoveRoleFromInstanceProfile",
        "iam:CreatePolicy",
        "iam:DeletePolicy",
        "iam:GetPolicy",
        "iam:CreatePolicyVersion",
        "iam:DeletePolicyVersion",
        "iam:GetPolicyVersion",
        "iam:ListPolicyVersions",
        "iam:ListAttachedRolePolicies",
        "iam:ListRolePolicies",
        "iam:ListRoles",
        "iam:ListInstanceProfiles",
        "iam:ListPolicies",
        "iam:TagRole",
        "iam:UntagRole",
        "iam:TagPolicy",
        "iam:UntagPolicy",
        "iam:TagInstanceProfile",
        "iam:UntagInstanceProfile",
        "iam:GetRolePolicy",
        "iam:ListRolePolicies",
        "iam:ListInstanceProfilesForRole"
      ],
      "Resource":[
        "arn:aws:iam::*:role/aws-bioinfo-sandbox-*",
        "arn:aws:iam::*:instance-profile/aws-bioinfo-sandbox-*",
        "arn:aws:iam::*:policy/aws-bioinfo-sandbox-*"
      ]
    },
    {
      "Effect":"Allow",
      "Action":[
        "logs:CreateLogGroup",
        "logs:DeleteLogGroup",
        "logs:DescribeLogGroups",
        "logs:PutRetentionPolicy",
        "logs:DeleteRetentionPolicy",
        "logs:CreateLogStream",
        "logs:DeleteLogStream",
        "logs:DescribeLogStreams",
        "logs:PutLogEvents",
        "logs:GetLogEvents",
        "logs:FilterLogEvents",
        "logs:TagResource",
        "logs:UntagResource",
        "logs:ListTagsForResource"
      ],
      "Resource":[
        "arn:aws:logs:*:*:log-group:/aws/ec2/aws-bioinfo-sandbox-*",
        "arn:aws:logs:*:*:log-group:/aws/lambda/aws-bioinfo-sandbox-*",
        "arn:aws:logs:*:*:log-group:*"
      ]
    },
    {
      "Effect":"Allow",
      "Action":[
        "logs:DescribeLogGroups"
      ],
      "Resource":"*"
    },
    {
      "Effect":"Allow",
      "Action":[
        "ec2:*"
      ],
      "Resource":"*"
    },
    {
      "Effect":"Allow",
      "Action":[
        "s3:*"
      ],
      "Resource":[
        "arn:aws:s3:::aws-bioinfo-sandbox-*",
        "arn:aws:s3:::aws-bioinfo-sandbox-*/*"
      ]
    },
    {
      "Effect":"Allow",
      "Action":[
        "lambda:*"
      ],
      "Resource":[
        "arn:aws:lambda:*:*:function:aws-bioinfo-sandbox-*"
      ]
    },
    {
      "Effect":"Allow",
      "Action":[
        "cloudwatch:*"
      ],
      "Resource":"*"
    }
  ]
}
``` 

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

### AWS Free Services

In addition to AWS Free Tier, there are free services on AWS that are free forever

![AWS Free Services](imgs/AWS-free-services.png)

### Other AWS resources

- **AWS Educate**: For students (requires .edu email)
- **AWS Activate**: For startups
- **AWS Partner Network**: For partners
- **Regional pricing**: Some regions have lower costs

## AWS Pricing Calculator & Cost Explorer

### AWS Pricing Calculator

- **What is it?**
  - A free, web-based tool (https://calculator.aws) to estimate the cost of AWS services before you deploy them.
  - No AWS account required to use.
- **Key Features:**
  - Supports 100+ AWS services for cost estimation
  - Configure resources, usage, and pricing models (on-demand, reserved, etc.)
  - View monthly, upfront, and 12-month cost projections
  - Export estimates to CSV for sharing or analysis
  - Useful for planning, budgeting, and TCO (Total Cost of Ownership) comparisons
- **Use Cases:**
  - Estimate costs for new workloads or migrations
  - Compare AWS costs to on-premises or other cloud providers
  - Share cost estimates with stakeholders

### AWS Cost Explorer

- **What is it?**
  - A tool in the AWS Management Console for analyzing your actual AWS spending and usage over time.
  - Requires an AWS account with billing permissions.
- **Key Features:**
  - Visualize costs and usage with interactive graphs and filtering
  - Break down costs by service, account, tag, region, or usage type
  - Forecast future costs based on historical usage
  - Identify cost trends, spikes, and savings opportunities
  - Set up cost and usage reports, and create custom views
- **Use Cases:**
  - Track and manage AWS spending
  - Identify cost optimization opportunities
  - Allocate costs to teams, projects, or business units
  - Monitor budgets and avoid unexpected charges

### Key Differences

| Feature  | AWS Pricing Calculator       | AWS Cost Explorer            |
| -------- | ---------------------------- | ---------------------------- |
| Purpose  | Estimate future costs        | Analyze actual costs         |
| Access   | Public web tool              | AWS Console (account needed) |
| Data     | Hypothetical (planned usage) | Real usage and billing data  |
| Export   | CSV, shareable links         | CSV, reports                 |
| Use Case | Planning, budgeting          | Monitoring, optimization     |

**Tip:** Use the Pricing Calculator before deploying resources, and Cost Explorer to monitor and optimize after deployment.

## AWS Networking: Security Groups vs NACLs

### Security Groups

- Act as virtual firewalls for EC2 instances to control inbound and outbound traffic.
- **Instance-level**: Applied directly to EC2 instances (and some other resources).
- **Stateful**: If you allow an incoming request, the response is automatically allowed.
- **Rules**: Only allow rules (no explicit deny); you specify allowed protocols, ports, and source/destination IPs or CIDR blocks.
- **Default behavior**: All inbound traffic is denied, all outbound traffic is allowed (can be modified).
- **Evaluation**: All rules are evaluated together; if any rule allows traffic, it is allowed.

### Network ACLs (NACLs)

- Act as firewalls for subnets within a VPC, controlling traffic in and out of one or more subnets.
- **Subnet-level**: Applied to all resources within a subnet.
- **Stateless**: Responses to allowed inbound traffic must be explicitly allowed by outbound rules (and vice versa).
- **Rules**: Support both allow and deny rules; rules are evaluated in order (lowest number first).
- **Default behavior**: All inbound and outbound traffic is allowed (can be modified).
- **Evaluation**: Rules are processed in order; the first matching rule is applied.

### Key Differences

| Feature            | Security Group             | NACL (Network ACL)       |
| ------------------ | -------------------------- | ------------------------ |
| Level              | Instance (ENI)             | Subnet                   |
| Stateful/Stateless | Stateful                   | Stateless                |
| Allow/Deny         | Allow only                 | Allow and Deny           |
| Evaluation         | All rules evaluated        | Rules evaluated in order |
| Default Inbound    | Deny all                   | Allow all                |
| Default Outbound   | Allow all                  | Allow all                |
| Applies to         | EC2, ENI, ELB, RDS, Lambda | All resources in subnet  |

### When to Use

- **Security Groups**: Use for most access control needs at the instance/resource level.
- **NACLs**: Use for additional subnet-level control, especially for blocking specific IPs or ranges, or for compliance.

## AWS Storage Services

![Types of Storage Services](imgs/types-of-storage-services.png)

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

### EC2 Tenancy Levels

![EC2 Tenancy Levels](imgs/EC2-levels-of-tenancy.png)

AWS EC2 offers different tenancy options that determine how your instances are run on physical hardware:

- **Shared Tenancy (default):**

  - Your instance runs on physical servers that may be shared with other AWS customers.
  - Most cost-effective and flexible option.
  - Default for most EC2 instance launches.

- **Dedicated Instance:**

  - Your instance runs on hardware that is dedicated to your AWS account, but may share the hardware with other instances from your account.
  - Provides physical isolation at the account level, but not at the instance level.
  - Higher cost than shared tenancy.

- **Dedicated Host:**
  - You get an entire physical server dedicated to your use.
  - Allows you to place and control your instances on a specific, physical server.
  - Useful for compliance, licensing, or regulatory requirements.
  - Most expensive option, but provides the highest level of isolation and control.

**When to use each:**

- Use **shared tenancy** for most workloads.
- Use **dedicated instances** for additional isolation without full host control.
- Use **dedicated hosts** for compliance, licensing, or when you need to use your own server-bound software licenses.

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

## Logging Services

### CloudTrail

- **AWS CloudTrail** is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account.
- **Purpose:** Monitors and records account activity across your AWS infrastructure, giving you visibility into user and resource actions.

### What Does CloudTrail Log?

- **All API calls** made via the AWS Management Console, AWS SDKs, CLI, and other AWS services.
- **Events include:**
  - Who made the request (user, role, or service)
  - When the request was made (timestamp)
  - What actions were performed (event name, e.g., CreateUser, DeleteBucket)
  - Which resources were affected (resource ARNs, IDs)
  - Source IP address and user agent
  - Request and response parameters

### Example CloudTrail Event (JSON)

```json
{
  "Records": [
    {
      "eventVersion": "1.0",
      "userIdentity": {
        "type": "IAMUser",
        "principalId": "EX_PRINCIPAL_ID",
        "arn": "arn:aws:iam::123456789012:user/Worf",
        "accountId": "123456789012",
        "accessKeyId": "EXAMPLE_KEY_ID",
        "userName": "Worf"
      },
      "eventTime": "2014-03-24T21:11:59Z",
      "eventSource": "iam.amazonaws.com",
      "eventName": "CreateUser",
      "awsRegion": "us-east-1",
      "sourceIPAddress": "127.0.0.1",
      "userAgent": "aws-cli/1.3.2 Python/2.7.5 Windows/10",
      "requestParameters": { "userName": "LaForge" },
      "responseElements": {
        "user": {
          "createDate": "Mar 24, 2014 9:11:59 PM",
          "userName": "LaForge",
          "arn": "arn:aws:iam::123456789012:user/LaForge",
          "path": "/",
          "userId": "EXAMPLEUSERID"
        }
      }
    }
  ]
}
```

### CloudTrail Event History vs. Trails

- **Event History:**
  - CloudTrail logs are enabled by default for all AWS accounts.
  - The Event History in the AWS Console shows the last **90 days** of management events (read/write API activity).
  - Useful for quick lookups and recent activity.
- **Trails:**
  - To retain logs for more than 90 days, you must create a **Trail**.
  - Trails deliver log files to an S3 bucket for long-term storage and analysis.
  - Trails can be created for a single region or all regions (recommended for compliance).
  - Trails can also send events to CloudWatch Logs for real-time monitoring and alerting.

### Analyzing CloudTrail Logs

- **No GUI for Trails:**
  - Trails output raw JSON logs to S3, not a GUI.
- **Analysis Tools:**
  - Use **Amazon Athena** to run SQL queries on CloudTrail logs stored in S3.
  - Integrate with CloudWatch Logs for real-time analysis and alerting.
  - Export logs to SIEM or third-party tools for advanced security analytics.

### Use Cases

- **Security & Compliance:**
  - Detect unauthorized or unusual activity (e.g., root account usage, policy changes).
  - Meet regulatory requirements for audit trails.
- **Operational Auditing:**
  - Troubleshoot changes and resource creation/deletion.
  - Answer questions like "Who deleted this resource?" or "Who changed this security group?"
- **Automation:**
  - Trigger automated responses to specific events (e.g., Lambda function on S3 bucket deletion).

### Best Practices

- Always enable CloudTrail in all regions.
- Create a multi-region trail for comprehensive coverage.
- Store logs in a secure, versioned S3 bucket.
- Enable log file validation for integrity.
- Integrate with CloudWatch for real-time alerts.
- Regularly review and analyze logs for suspicious activity.

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

## VMs vs Containers

![VMs vs Containers](imgs/vms-vs-containers.png)

### Key Differences

- **VMs (Virtual Machines):**
  - Do not make the best use of space; can result in wasted resources.
  - Apps are not isolated, which can cause configuration conflicts, security problems, or resource hogging.
  - Each VM includes its own OS, libraries, and binaries, running on a hypervisor.
- **Containers:**
  - Allow you to run multiple apps, each virtually isolated from the others.
  - Launch new containers and configure OS dependencies per container.
  - More efficient use of resources; available space can be used for more containers.
  - Containers share the host OS and run on a container engine (e.g., Docker Daemon), reducing overhead.

![VMs vs Containers](imgs/vms-vs-containers.png)

## What are Microservices?

![Microservices vs Monolithic](imgs/microservices-vs-monolithic.png)

### Monolithic Architecture

- **One app responsible for everything**; all functionality is tightly coupled.
- All services (load balancing, caching, database, frontend, backend, etc.) are part of a single codebase and deployment.
- Changes in one part can affect the whole system.
- Harder to scale, update, and maintain as the application grows.

### Microservices Architecture

- **Multiple apps, each responsible for one thing**; functionality is isolated and stateless.
- Each service (load balancing, caching, database, queueing, etc.) is its own independent component.
- Services communicate over APIs and can be deployed, updated, and scaled independently.
- Easier to maintain, scale, and develop by separate teams.

### Key Differences

| Monolithic Architecture             | Microservices Architecture                |
| ----------------------------------- | ----------------------------------------- |
| Single, tightly-coupled application | Multiple, loosely-coupled services        |
| All functionality in one codebase   | Each service has its own codebase         |
| Harder to scale and update          | Easier to scale and update independently  |
| One deployment pipeline             | Separate deployment pipelines per service |
| Shared resources and dependencies   | Isolated resources and dependencies       |

## Kubernetes

- **Kubernetes** is an open-source container orchestration platform for automating deployment, scaling, and management of containerized applications.
- Originally developed by Google, now maintained by the Cloud Native Computing Foundation (CNCF).
- **Key features:**
  - Automated container scheduling and scaling
  - Self-healing (auto-restart, reschedule, replace containers)
  - Service discovery and load balancing
  - Rolling updates and rollbacks
  - Declarative configuration (YAML/JSON)
- **AWS Integration:**
  - AWS offers managed Kubernetes via **Amazon EKS (Elastic Kubernetes Service)**
  - Can run Kubernetes clusters on EC2 or on-premises (hybrid)
- **Why use Kubernetes?**
  - Simplifies running microservices and containerized workloads at scale
  - Enables portability across cloud and on-prem environments
  - Industry standard for container orchestration

### Example: Simple Kubernetes Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.21
          ports:
            - containerPort: 80
```

This manifest deploys two NGINX containers managed by Kubernetes.

## Terraform

- **Terraform** is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp.
- Allows you to define, provision, and manage cloud infrastructure using declarative configuration files (HCL - HashiCorp Configuration Language).
- **Key features:**
  - Supports AWS, Azure, GCP, and many other providers
  - Enables version control and repeatability for infrastructure
  - Manages dependencies and resource relationships
  - Plan and preview changes before applying
  - State management for tracking deployed resources
- **AWS Integration:**
  - Use Terraform to automate creation and management of AWS resources (EC2, S3, VPC, IAM, etc.)
  - Works well for multi-cloud and hybrid-cloud deployments
- **Why use Terraform?**
  - Infrastructure is reproducible, auditable, and shareable
  - Reduces manual errors and increases automation
  - Essential for modern DevOps and cloud engineering workflows

### Example: Simple Terraform AWS EC2 Instance

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI (example)
  instance_type = "t2.micro"

  tags = {
    Name = "TerraformExample"
  }
}
```

This configuration will provision a t2.micro EC2 instance in AWS using Terraform.

### Terraform Workflow: `init`, `plan`, and `apply`

#### `terraform init`

- **Purpose:** Initializes a Terraform working directory.
- **What it does:**
  - Downloads and installs the provider plugins required by your configuration (e.g., AWS, Azure, GCP).
  - Sets up the backend for storing state (local or remote).
  - Prepares the directory for further Terraform commands.
- **When to use:** Run this command first, and any time you add or change providers or modules.
- **Example:**
  ```bash
  terraform init
  ```

#### `terraform plan`

- **Purpose:** Creates an execution plan, showing what actions Terraform will take to achieve the desired state.
- **What it does:**
  - Compares your configuration files to the current state (real infrastructure or state file).
  - Shows which resources will be created, changed, or destroyed.
  - Does not make any changes—safe to run as often as you like.
- **When to use:** Before every `terraform apply`, to review and confirm changes.
- **Example:**
  ```bash
  terraform plan
  ```
- **Debugging Tip:**
  - You can set the environment variable `TF_LOG=DEBUG` to get detailed debug output for troubleshooting.
  - Example:
    ```bash
    TF_LOG=DEBUG terraform plan
    ```

#### `terraform apply`

- **Purpose:** Applies the changes required to reach the desired state of the configuration.
- **What it does:**
  - Executes the actions proposed in the most recent `terraform plan`.
  - Prompts for approval before making changes (unless run with `-auto-approve`).
- **When to use:** After reviewing the plan and confirming you want to make the changes.
- **Example:**
  ```bash
  terraform apply
  ```

---

| Command           | Purpose                                    | Typical Usage                            |
| ----------------- | ------------------------------------------ | ---------------------------------------- |
| `terraform init`  | Initialize working directory and providers | First run, after provider/module changes |
| `terraform plan`  | Preview changes before applying            | Before every `apply`                     |
| `terraform apply` | Make actual changes to infrastructure      | After reviewing plan                     |

**Debugging:**

- Use `TF_LOG=DEBUG` with any Terraform command to get verbose logs for troubleshooting.
  - Make sure you don't separate the commands with `;` or `&&` (example: `TF_LOG=DEBUG terraform plan`)

## OpenTofu

- **OpenTofu** is an open-source, community-driven fork of Terraform, created in response to HashiCorp's change of Terraform's license from open source (MPL) to the Business Source License (BSL) in August 2023.
- **Goal:** To provide a truly open, vendor-neutral, and community-governed alternative to Terraform, ensuring the ecosystem remains open and collaborative.

### Key Points

- **Origin:** Forked from Terraform 1.5.x before the license change.
- **License:** Uses the Mozilla Public License (MPL) 2.0, ensuring it remains open source.
- **Compatibility:** Aims for drop-in compatibility with existing Terraform configurations, providers, and modules.
- **Community:** Governed by the Linux Foundation and supported by a broad coalition of cloud vendors, infrastructure companies, and the open source community.
- **CLI:** The command-line interface is nearly identical to Terraform (`opentofu init`, `opentofu plan`, `opentofu apply`, etc.).
- **Ecosystem:** Supports the same HCL language, state files, and provider ecosystem as Terraform.
- **Migration:** Most users can switch by replacing `terraform` with `opentofu` in their workflows.

### Why Use OpenTofu?

- **Open Governance:** No single vendor control; decisions are made by the community.
- **Future-Proof:** Ensures continued open source development and innovation.
- **Compatibility:** Minimal friction for teams already using Terraform.
- **Trust:** Transparent development and licensing.

### Example Usage

```bash
opentofu init
opentofu plan
opentofu apply
```

## AWS CloudWatch

Amazon CloudWatch is AWS's native monitoring and observability service. It collects, visualizes, and acts on metrics, logs, and events from AWS resources, applications, and on-premises servers, enabling you to monitor performance, troubleshoot issues, and automate responses.

### Core CloudWatch Components

- **Metrics**: Numeric data points representing resource or application performance over time.
- **Alarms**: Automated rules that watch metrics and trigger actions when thresholds are breached.
- **Logs**: Aggregated log data from AWS services, EC2, Lambda, and custom sources.
- **Events/EventBridge**: Event-driven rules and automation for AWS and SaaS events.
- **Dashboards**: Customizable visualizations of metrics and logs.
- **Insights**: Advanced analytics for logs (CloudWatch Logs Insights).

---

### CloudWatch Metrics

- **Definition**: A time-ordered set of data points (values) for a resource or application variable.
- **Predefined Metrics**: Provided automatically for AWS services (EC2, S3, Lambda, RDS, etc.).
- **Custom Metrics**: You can publish your own metrics (e.g., app latency, business KPIs).
- **Namespaces**: Logical containers for metrics (e.g., `AWS/EC2`, `AWS/Lambda`, `Custom/MyApp`).
- **Dimensions**: Name/value pairs for filtering (e.g., InstanceId, FunctionName).
- **Granularity**: Default is 1-minute; some services support 1-second (high-resolution metrics).
- **Retention**: 15 months (with decreasing granularity over time).
- **Metric Math**: Perform calculations across multiple metrics (e.g., sum, average, rate, anomaly detection).

**Common EC2 Metrics:**

- CPUUtilization, DiskReadOps, DiskWriteOps, NetworkIn, NetworkOut, StatusCheckFailed, etc.

**Custom Metrics Example (CLI):**

```bash
aws cloudwatch put-metric-data --namespace "Custom/App" --metric-name PageLoadTime --value 1.23
```

---

### CloudWatch Alarms

![CloudWatch Alarms Anatomy](imgs/cloudwatch-anatomy-of-an-alarm.png)

- **Purpose**: Monitor a metric and perform actions when it breaches a defined threshold.
- **States**: `OK`, `ALARM`, `INSUFFICIENT_DATA`
  - `OK`: The metric or expression is **within** the defined threshold
  - `ALARM`: The metric or expression is **outside** of the defined threshold
  - `INSUFFICIENT_DATA`: Either the alarm has **just started**, the metric is **not available**, or **not enough data** is available
- **Types**:
  - **Metric Alarm**: Single metric or metric math expression
  - **Composite Alarm**: Combines multiple alarms (AND/OR logic)
  - **Anomaly Detection**: Uses ML to detect outliers
- **Actions**: Send SNS notification, trigger Lambda, Auto Scaling, EC2 actions, etc.
- **Evaluation Periods**: Number of periods to evaluate before changing state
- **Datapoints to Alarm**: How many periods must breach threshold to trigger alarm
- **Best Practices**:
  - Use alarms for billing, system health, and custom app metrics
  - Combine with automation (e.g., auto-recover EC2, scale out/in)

**Example: Billing Alarm (CLI):**

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name "BillingAlarm" \
  --metric-name EstimatedCharges \
  --namespace AWS/Billing \
  --statistic Maximum \
  --period 86400 \
  --threshold 10 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 \
  --alarm-actions arn:aws:sns:us-east-1:123456789012:NotifyMe
```

---

### CloudWatch Logs

- **Log Groups**: Containers for log streams (e.g., `/aws/lambda/my-function`)
- **Log Streams**: Sequences of log events from a single source (e.g., EC2 instance, Lambda invocation)
- **Log Events**: Individual log entries (timestamped)
- **Ingestion Sources**: EC2 (via agent), Lambda, VPC Flow Logs, API Gateway, ECS/EKS, custom apps
- **Retention**: Configurable (default is never expire)
- **Subscription Filters**: Stream logs to Lambda, Kinesis, or Elasticsearch for real-time processing
- **Export**: Send logs to S3 for long-term storage/analytics
- **Security**: Encryption at rest (KMS), fine-grained IAM controls

#### CloudWatch Logs Insights

- **Purpose**: Interactive log analytics and querying
- **Query Language**: Powerful SQL-like syntax for filtering, aggregating, and visualizing logs
- **Use Cases**: Troubleshooting, security analysis, operational monitoring
- **Limits**: Query up to 20 log groups, 7-day result retention, 15-min query timeout

**Example Query:**

```sql
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

---

### CloudWatch Events / EventBridge

- **CloudWatch Events**: Delivers a near real-time stream of system events describing changes in AWS resources.
- **EventBridge**: Next-gen event bus, supports AWS, SaaS, and custom events.
- **Rules**: Match events and route to targets (Lambda, SNS, SQS, Step Functions, etc.)
- **Scheduled Events**: Cron-like scheduling for automation
- **Use Cases**: Automation, decoupling, cross-service orchestration, SaaS integration

**Example: Scheduled Lambda Trigger**

```json
{
  "source": ["aws.events"],
  "detail-type": ["Scheduled Event"]
}
```

---

### CloudWatch Dashboards

- **Purpose**: Visualize metrics and logs in customizable, shareable dashboards
- **Widgets**: Graphs, numbers, text, alarms, logs, custom queries
- **Cross-Region**: Can display metrics from multiple regions
- **Sharing**: Share dashboards with teams or externally (read-only)
- **Use Cases**: NOC, executive reporting, troubleshooting

---

### CloudWatch Agent & Integrations

- **CloudWatch Agent**: Collects system-level metrics and logs from EC2, on-prem servers, and VMs
- **Unified Agent**: Replaces older SSM and CloudWatch Logs agents
- **Custom Metrics/Logs**: Send application-specific data
- **Integration**: ECS, EKS, Lambda, hybrid/on-prem, third-party

**Agent Install Example (Linux):**

```bash
sudo yum install amazon-cloudwatch-agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
sudo systemctl start amazon-cloudwatch-agent
```

---

### Pricing & Best Practices

- **Pricing**: Based on metrics (standard/custom), logs ingested/retained, dashboards, alarms, and API requests
- **Cost Optimization**:
  - Use metric filters and log retention policies
  - Aggregate custom metrics where possible
  - Export infrequently accessed logs to S3
- **Security**:
  - Use IAM roles and policies for least-privilege access
  - Enable encryption for logs and metrics
- **Monitoring Strategy**:
  - Centralize monitoring for multi-account/multi-region
  - Use composite alarms for complex scenarios
  - Integrate with CloudTrail for audit and security

---

### Common Scenarios & Exam Tips

- **Auto Scaling**: Use alarms to trigger scaling policies
- **EC2 Recovery**: Alarm on instance status check failure to auto-recover
- **Billing Alerts**: Alarm on EstimatedCharges for cost control
- **Log Analysis**: Use Logs Insights for troubleshooting and security
- **Event-Driven Automation**: Use EventBridge for serverless workflows
- **Dashboards**: Build NOC or executive dashboards for real-time visibility

---

**References:**

- [CloudWatch Documentation](https://docs.aws.amazon.com/cloudwatch/)
- [CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)
- [CloudWatch Logs Insights Query Syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)
