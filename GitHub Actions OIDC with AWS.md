# GitHub Actions OIDC Authentication with AWS

This project demonstrates how to securely authenticate GitHub Actions with AWS using OpenID Connect (OIDC) without storing long-term AWS credentials in GitHub. Instead of using AWS Access Keys, GitHub requests a temporary identity token, which AWS verifies and then provides temporary security credentials using AWS STS.

## What is OIDC?

OpenID Connect (OIDC) is an authentication protocol built on top of OAuth 2.0 that allows applications to verify user identity using a trusted identity provider. In CI/CD pipelines, OIDC enables GitHub Actions to securely authenticate with cloud providers like AWS without storing static secrets.

**Flow:**
GitHub → Requests an identity token → AWS validates the token → Temporary credentials are issued


## Why Use OIDC with GitHub Actions?

Traditional CI/CD pipelines store AWS credentials in GitHub Secrets:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`

This creates security risks:
- Credential leakage
- Long-lived credentials
- Manual credential rotation
- Increased attack surface

OIDC eliminates these risks by using short-lived credentials issued dynamically during workflow execution.

## Key Benefits

- **No Long-Term Credentials**  
  No need to store AWS Access Keys in GitHub Secrets.

- **Temporary Credentials**  
  AWS STS generates credentials that expire automatically after a short time.

- **Improved Security**  
  Only specific repositories, branches, or workflows can assume the IAM role.  
  *Example restriction:* `repo:organization/repository:ref:refs/heads/main`

- **Automatic Credential Rotation**  
  Credentials expire automatically, reducing the need for manual management.

- **Fine-Grained Access Control**  
  IAM roles can restrict access to:
  - Specific repositories
  - Specific branches
  - Specific workflows
  - Specific environments

## OIDC Authentication Workflow



The authentication process works as follows:

1. **Developer pushes code** to GitHub
2. **GitHub Actions workflow** starts
3. **Workflow requests** an OIDC token from GitHub
4. **GitHub generates** a signed JWT identity token
5. **Workflow sends** the token to AWS Security Token Service (STS)
6. **AWS validates** the token against the configured OIDC provider
7. **AWS allows** the workflow to assume an IAM role
8. **AWS issues** temporary credentials
9. **Workflow uses** temporary credentials to access AWS services

