# aws-setup-utils

Quick guides on how to assume a cross-account GDS AWS Role that requires Multi-Factor-Authentication (MFA):
- using `aws-cli`: [setup-aws-cli.md](setup-aws-cli.md)
- using AWS Python SDK `boto3`: [setup-boto3.md](setup-boto3.md)

so that you can do things your GDS AWS Account is not authorised to do, e.g., interacting with `S3`.


### References and useful resources

- https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html
- https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-role.html#cli-configure-role-mfa
- https://aws.amazon.com/premiumsupport/knowledge-center/iam-assume-role-cli/
- https://medium.com/trainingpeaks-product-development/aws-cli-using-role-assumption-and-mfa-948882e9f6d9
- https://aws.amazon.com/premiumsupport/knowledge-center/authenticate-mfa-cli/
- https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html
- https://stackoverflow.com/questions/64961558/boto3-assume-cross-account-role-with-mfa 
- https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sts.html 
