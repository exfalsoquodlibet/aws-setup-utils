# Set up AWS CLI to assume roles with MFA and interact with GDS AWS

Here we go through how to set up `aws cli` to assume an AWS IAM Role that has permissions your AWS user account does not have (e.g., accessing `S3`). It assumes that MFA is also required.

Assuming a role means that the AWS token service will give you **temporary credentials** to access the account with an assumed role. 

## GDS AWS Requirements

1. Ensure you have a GDS AWS Account. This will give you access to GDS AWS. Follow these instructions if you have not already done so as part of your onboarding: https://docs.publishing.service.gov.uk/manual/get-started.html#8-get-aws-access. At the end, you will have created your AWS user account, and also received an `access key ID` and `secret access key`.

2. Get STS Permission to AssumeRole with MFA for the role you want to assume. For instance, if you are a Data Scientist in CPTO, you may want to assume [`govuk-datascienceusers` AWS IAM Role][ds-role]. You can ask on the `#data-engineering` Slack channel.

## Set up AWS ALI

3. Ensure you have `aws cli` installed. It should have gotten installed as part of step 1. To verify, in your terminal run:

```shell
which aws
aws --version
```
If it is not available, please [follow the official installation instructions][awscli-install].

4. Configure `aws cli`. In your terminal, run:

```shell
aws configure
```
and follow the prompts. NOTE: you will be asked to provide your `access key ID` and `secret access key` so have them ready.

Your credentials should have now been added to the `~/.aws/credentials` file, under a `[default]` profile.

5. Create a profile for the role you want to assume in your `~/.aws/config`. If the file does not exist, you'll need to create it. In your `~/.aws/config` file, add:

```
[profile <PROFILE-ALIAS>]
source_profile = default
role_arn = arn:aws:iam::<ROLE ACCOUNT NUM>:role/<ROLE NAME>
mfa_serial = arn:aws:iam::<YOUR USER ACCOUNT NUMBER>:mfa/<YOUR NAME>.<YOUR SURNAME>@digital.cabinet-office.gov.uk
```
choosing a suitable `<PROFILE-ALIAS>` and substituting the correct values for `<ROLE ACCOUNT NUM>`, `<ROLE NAME>`, `<YOUR USER ACCOUNT NUMBER>`, `<YOUR NAME>` and `<YOUR SURNAME>`.


For instance, to create a profile for the `govuk-datascienceusers` so that we can assume that role with MFA, to the `~/.aws/config` file, add:

```
[profile govuk-datascience]
source_profile = default
role_arn = arn:aws:iam::<ROLE ACCOUNT NUM>:role/govuk-datascienceusers
mfa_serial = arn:aws:iam::<YOUR USER ACCOUNT NUMBER>:mfa/<YOUR NAME>.<YOUR SURNAME>@digital.cabinet-office.gov.uk
```

You can now assume the `govuk-datascienceusers` role and its permissions to interact with AWS S3 
by adding `--profile govuk-datascience` at the end of your `aws cli` commands.

For instance:
```shell
aws s3 ls --profile govuk-datascience
```