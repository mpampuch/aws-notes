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

## Configuration Files

## AWS CLI Configure Commands

## AWS CLI Environment Variables

## AWS CLI Autoprompt

## AWS S3

## AWS EC2

## AS EBS

## AWS AMI

## AWS Root Device Type
