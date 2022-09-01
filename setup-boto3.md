# Set up AWS Python SDK (boto3) to assume roles with MFA and interact with GDS AWS

Here we go through how to use `boto3` to interact with GDS AWS by assuming an IAM Role that has permissions your AWS user account does not have (e.g., accessing `S3`). It assumes that MFA is also required.

Assuming a role means that the AWS token service will give you **temporary credentials** to access the account with an assumed role. 

## GDS AWS Requirements

1. Ensure you have a GDS AWS Account. This will give you access to GDS AWS. Follow these instructions if you have not already done so as part of your onboarding: https://docs.publishing.service.gov.uk/manual/get-started.html#8-get-aws-access. At the end, you will have created your AWS user account, and also received an `access key ID` and `secret access key`.

2. Get STS Permission to AssumeRole with MFA for the role you want to assume. For instance, if you are a Data Scientist in CPTO, you may want to assume [`govuk-datascienceusers` AWS IAM Role][ds-role]. You can ask on the `#data-engineering` Slack channel.

## `boto3` Requirements

Ensure you have a python environment activated.

Install the python package [`boto3`][https://pypi.org/project/boto3/].

Configure `aws` credentials:

```shell
aws configure
```
and follow the prompts. NOTE: you will be asked to provide your `access key ID` and `secret access key` so have them ready. More info here: https://docs.aws.amazon.com/sdkref/latest/guide/creds-config-files.html.

Alternatively, you can set up your configurations and credentials as (secret) environment variables. More here: https://docs.aws.amazon.com/sdkref/latest/guide/environment-variables.html.


## Set up `boto3`

These are the basic steps to generate temporary credentials via AssumeRole with MFA and use them to make a connection to Amazon `S3`.

1. Create an STS client object, representing a live connection to the STS service
    
```shell 
sts_client = boto3.client("sts")
```

2. Define the ARN of the role we want to assume (e.g., `govuk-datascienceusers`), substituting appropriate values for `ROLE_ACCOUNT_ID` and `ROLE_NAME`:

```shell
role_arn = f"arn:aws:iam::{ROLE_ACCOUNT_ID}:role/{ROLE_NAME}"
```

3. Ask for the MFA token:

```shell 
MFA_OPT = input("Enter the MFA code: ")
```

4. Call the `assume_role` method of the STSConnection object and pass the role ARN and a chosen role session name:

```shell 
assumedRoleObject = sts_client.assume_role(
    RoleArn=role_arn,
    RoleSessionName=f"{SESSION_X}",
    SerialNumber=f"arn:aws:iam::{USER_ACCOUNT_ID}:mfa/{USERNAME}@digital.cabinet-office.gov.uk",
    DurationSeconds=3600,
    TokenCode=MFA_OPT,
    )
```

providing appropriate values for `SESSION_X` (your choice here, could be for instance `Session_Alessia`), `USER_ACCOUNT_ID` and `USERNAME`.


5. From the response that contains the assumed role, get the temporary credentials:

```shell
temp_credentials = assumedRoleObject["Credentials"]
```

6. You can now use the temporary credentials to, for instance, connect to `S3`:

```shell
s3_resource = boto3.resource(
    "s3",
    aws_access_key_id=temp_credentials["AccessKeyId"],
    aws_secret_access_key=temp_credentials["SecretAccessKey"],
    aws_session_token=temp_credentials["SessionToken"],
    )
```

And for instance list all S3 buckets:

```shell
buckets = [bucket.name for bucket in s3_resource.buckets.all()]
print(buckets)
```

## Helper functions for `boto3`

You can find some helpers functions (wrappers for the above) in [helpers_aws_boto3.py]. 
