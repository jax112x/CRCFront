
# Cloud Resume Challenge



## Overview

A webpage hosted on AWS that tracks and displays the number of visitors.

## Architecture

![alt text](/public/images/architecture.jpg "Architecture")

## Technology used
HTML, CSS, JavaScript, Python, GitHub, GitHub Actions, Terraform, CloudFront, S3, API Gateway, Lambda, DynamoDB, IAM 


## Implementation Details

### Static Website
The HTML, CSS, and JavaScript files are hosted on an S3 bucket. Static website hosting is not enabled on this bucket.
### DynamoDB
DynamoDB is used to store the visitor count.
### Lambda
A Lambda function is used to handle the updating and retrieving of the visitor count from DynamoDB. 
### API Gateway
An API Gateway is configured with proxy integration to the Lambda function. To protect the downstream resources, a usage plan with an API key is created and associated with a deployment stage.
### CloudFront
* A CloudFront distribution is created and is used as a reverse proxy.
* The distribution is created with two origins: one for the S3 bucket and one for the API Gateway.
* The S3 origin is configured with origin access control to securely interact with the bucket. Caching is enabled for this origin.
* The API Gateway origin is set to send a custom header with an API key. Caching is disabled on this behavior since it returns dynamic data.

### Infrastructure as Code
The backend infrastructure, including the CloudFront distribution, is deployed using Terraform. The state file is stored in an s3 bucket.

### CI/CD(GitHub)
* __Frontend__ - A GitHub repository with a workflow for the static content is set up. It updates the S3 bucket and invalidates the CloudFront distribution whenever there is a push to the master branch.
* __Backend__ - A GitHub repository with a workflow is used to deploy the backend infrastructure and the Lambda function whenever there is a push to the master branch.
* __Authentication__ - OIDC is utilized for GitHub Actions to authenticate with AWS.
* __Secrets__ - Any information that is deemed to be sensitive is stored as a repository secret.

### IAM Roles and Permissions

* __Github Frontend__ - The role assumed by this identity only has access to S3 and Cloudfront.
* __Github Backend__ - The role assumed by this identity only has access to CloudFront, API gateway, lambda, Dynamodb, IAM, and the S3 bucket that stores the terraform state.
* __Lambda__ - The lambda function has permission to create CloudWatch logs and interact with the DynamoDB database. The lambda function also has a resource policy that lets the API gateway invoke it.
* __Static Website S3 bucket__ - This bucket does not have public access and only allows access from the CloudFront distribution with origin access control.
