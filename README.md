## URL: http://hey-beast-landing-page.s3-website.ap-south-1.amazonaws.com

**main.yml**
```yaml
name: Landing Page Deployment

on:
  push:
    branches:
    - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v1

    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1

    - name: Deploy static site to S3 bucket
      run: aws s3 sync . s3://hey-beast-landing-page --delete
```

This project uses GitHub Actions to automatically deploy portfolio to an AWS S3 bucket whenever changes are pushed to the `main` branch.

## Workflow Overview

The deployment process is handled by a GitHub Actions workflow that performs the following steps:

1. **Checkout the repository** - Uses the `actions/checkout@v1` action to checkout the repository so the build can access the latest code.
2. **Configure AWS Credentials** - Configures AWS credentials using the GitHub Secrets to securely authenticate with AWS.
3. **Deploy to S3** - Syncs the local files to the S3 bucket specified, making sure to delete any files in the bucket that no longer exist in the repository.

## Workflow Configuration

The workflow is defined in a YAML file (`.github/workflows/deploy.yml`). Here is a breakdown of the workflow:

### `on`
This section defines when the workflow is triggered:
- **push** - The workflow is triggered whenever there is a push to the `main` branch.

### `jobs`
This section defines the steps to be run as part of the deployment job:

- **runs-on** - The job will run on the latest Ubuntu environment provided by GitHub Actions.
  
#### Steps:

1. **Checkout** - The repository code is checked out to the runner using the `actions/checkout@v1` action.
    ```yaml
    - name: Checkout
      uses: actions/checkout@v1
    ```

2. **Configure AWS Credentials** - AWS credentials are configured using secrets stored in the GitHub repository. These secrets (`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`) must be set up beforehand in your GitHub repository.
    ```yaml
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1
    ```

3. **Deploy static site to S3 bucket** - This step syncs the local directory with the S3 bucket using the AWS CLI command `aws s3 sync`. The `--delete` flag ensures that files in the bucket that no longer exist in the local directory are deleted.
    ```yaml
    - name: Deploy static site to S3 bucket
      run: aws s3 sync . s3://hey-beast-landing-page --delete
    ```

## Prerequisites

- An AWS account and an S3 bucket (e.g., `hey-beast-landing-page`) created where the static site will be hosted.
- AWS credentials (Access Key ID and Secret Access Key) should be stored as secrets in your GitHub repository:
  - `AWS_ACCESS_KEY_ID`
  - `AWS_SECRET_ACCESS_KEY`

## AWS S3 Bucket Configuration

Ensure that S3 bucket is configured to host a static website. This can be done in the AWS S3 console:
1. Go to S3 bucket.
2. Click on the **Properties** tab.
3. Scroll down to the **Static website hosting** section.
4. Enable static website hosting and provide the appropriate index and error document names (e.g., `index.html`).

# Public Access Policy for S3 Bucket

This S3 bucket (`hey-beast-landing-page`) is publicly accessible for reading the objects it contains. The following policy has been applied to allow public access.

## Policy Overview

This bucket policy allows any user, including anonymous users, to retrieve (download) objects from the `hey-beast-landing-page` bucket.

### Policy Details

- **Policy Version:** 2012-10-17
- **Effect:** Allow public read access to objects in the bucket.
- **Action Allowed:** `s3:GetObject` (Retrieve objects from the bucket).
- **Principal:** `*` (The policy applies to all users, including anonymous users).
- **Resource:** Applies to all objects within the bucket `hey-beast-landing-page`.

### JSON Policy

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::hey-beast-landing-page/*"
        }
    ]
}
