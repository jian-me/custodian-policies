# Cloud Governance
Cloud governance is the practice of managing the cost, compliance, performance, and security of cloud services. Often, cloud governance is associated with the use of public cloud providers, such as AWS and Azure, but it may also extend to private, hybrid, and on-prem infrastructure. 
 
# What are the Risks?
Improper governance of cloud services may lead to unnecessary spend, loss of service availability, or unauthorized data or system access. Given the volume of data that companies process and store in their cloud infrastructure and the criticality of most cloud services to business operations, the impact of such risks may be material.

# What are Potential Mitigations?
From a security perspective, there are many cloud governance solutions often grouped under the term "Cloud Security Posture Management" or CSPM. I have created sample policies to demonstrate the capabilities of an open-source cloud governance tool called Cloud Custodian (https://cloudcustodian.io/). These policies and scripts demonstrate the capabilities of Cloud Custodian to configure common public cloud security controls. The policies are meant to be used in conjunction with other controls.

# Getting Started
Start by downloading and installing the Cloud Custodian packages from the website. I have documented the steps I followed below.
https://cloudcustodian.io/docs/quickstart/index.html

Notes: 
1. For this demo, I have installed Cloud Custodian on a MacBook running OSX Sonoma 14.5 running an M1 processor with 16 GB of RAM.
2. You will need the latest version of `python3` installed.
3. The demo policy configures the new S3 buckets to log events to a separate bucket called `{account_id}-{region}-s3-logs`. You will need to create this bucket manually for via IaC before you run Custodian.

**Installation:**
1. Install Cloud Custodian via CLI. You can install using Docker as well.

```
python3 -m venv custodian
source custodian/bin/activate
pip install c7n
```

2. Create a folder to store all the Cloud Custodian files.

```
mkdir custodian
cd custodian
```

3. In your AWS account, create an IAM Role called "Cloud_Custodian_Role" with the appropriate permissions. Since Cloud Custodian creates Lambda functions to execute actions, you will need to define a trust policy.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowLambdatoAssumeRole",
            "Effect": "Allow",
            "Principal": {
                "Service": "lambda.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

4. Create an IAM User and assign the "Cloud_Custodian_Role" to the user. Create an Access Key for the user and save the Access Key and Secret Access Key. You will need to save these values as variables in the CLI to run Cloud Custodian. This step can be done manually (for this demo) or using IaC such as Terraform or OpenTofu.

5. In Terminal, set `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

```
export AWS_ACCESS_KEY_ID=<Access Key you copied for the IAM User>
export AWS_SECRET_ACCESS_KEY = <Secret Access Key you copied for the IAM User>
```

6. Copy the `custodian.yml` file from this repo to the "custodian" folder.

7. Run Cloud Custodian. For this demo, we output the logs to the folder. You can also output results to an S3 bucket or other remote location.

```
custodian run --output-dir=. custodian.yml
```

The output should look something like this.

<img width="986" alt="image" src="https://github.com/user-attachments/assets/b7c7cd71-373d-466b-a07e-def77ebb59b5" />

# Future / Planned Work:
- Add custodian policies for IAM and other common AWS services
- Store Custodian output to a separate S3 bucket
- Configure custodian policies for Microsoft Azure
