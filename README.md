# GitHub-S3-Sync
Step-by-step sync of a static website or files between Github and AWS S3 bucket using Github Actions.

# Step 1: Create S3 bucket

* To create a new bucket, you can navigate to the AWS S3 console, and click on the ‘Create Bucket’ button.
* Click on the properties tab of the newly created bucket and enable the static website option.
* Click on the permission tab and make it public by unchecking "Block all public access"
* On the Bucket policy section paste this policy and save.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::anesthesia-forms/*"
        }
    ]
}

```


# Step 2: Create an IAM user and generate an Access Key

* From AWS IAM create a new user
* In the Set Permission step select "Attach policies directly"
* From the list of Permission policies search and find "AmazonS3FullAccess" and select it and hit the Create user button.
* Click Create access keys for the user and download the access key CSV file or copy and save it somewhere. 


# Step 3: Configure GitHub repo Create with action workflow file to sync on Push.
 * On your GitHub Repo Click on settings.
 * Select Secrets from the left sidebar
 * Create two Secret Keys 
 * On your GitHub repo click on Actions and a workflow file by clicking on the New Workflow Button.
 * Paste the bellow code in the action workflow file 

```

name: uploadToS3
on:
  push:
    branches: [ "main" ]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: jakejarvis/s3-sync-action@master
      with:
        args: --acl public-read --follow-symlinks --delete  --exclude '.git/*'
      env:
        AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        AWS_REGION: 'us-east-2'   # optional: defaults to us-east-1

```

We are done. GitHub will upload the files to your S3 Bucket every time you push your update to GitHub.  

