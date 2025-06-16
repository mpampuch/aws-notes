# aws-notes
Some tips / things of notes for myself while I'm learning AWS

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

## AWS S3

## AWS EC2

## AS EBS

## AWS AMI

## AWS Root Device Type
