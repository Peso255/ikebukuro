# Ikebukuro
1 S3 bucket, 1 CloudFront distribution, 100 sites.

[![Launch Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=ikebukuro&templateURL=https://mamintada.s3.amazonaws.com/ikebukuro/template.json)

---

Hosting many static landing pages/websites? Tired of doing the same rigmarole on NGINX/S3/CloudFront when setting a new one? This is for you.

  - Launch Stack.
  - Put website files on S3.
  - Profit.

### What to have
  - AWS account with permissions to deploy resources through CloudFormation.
  - [A certificate provisioned through ACM](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html) in the `us-east-1` region, covering all domains being setup.
  - [An IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) with programmatic access (to sync files with S3).

### What to do
  - Click "Launch Stack" at the top. Click "Next".
  - Change the Stack Name if you want. Fill in parameters:
    - `CertArn`: ARN of certificate from ACM.
      - Make sure to create it in the `us-east-1` region!
    - `ServedDomains`: Domains to serve. Separate multiple domains with commas.
      - `example.com != www.example.com`
    - `SyncUserArn`: ARN of users with read/write access to S3 bucket. Separate multiple users with commas.
  - "Next", "Next", "Create Stack".
  - Once stack creation is complete, load in website and error page files on the created S3 bucket.
    - AWS console, `aws s3 cp` or `aws s3 sync`.
    - Put files for each domain in its own folder (e.g. `/example.com/index.htm` for `example.com`).
    - Put 404 error (only error I have right now) page design on `/error/404.htm`.

### What to know
  - Stack must be launched on `us-east-1` region. Lambda@Edge requirements.
  - No server-side content, since the files are hosted in S3.
    - Client-side JS and an API can provide server-side content.
  - The same error page design applies to all websites.
    - CloudFront wipes request headers when returning a request to origin for the error document.
    - If you know how to bypass this wipe, [tweet me](https://twitter.com/Peso255) or open a pull request!
  - Stack deletions ***will not work the first time***.
    - Lambda won't let you delete functions until their caches have been removed from all CloudFront edges.
    - Let it fail, wait at least 1 hour, try deleting again. It'll work after some time.

---

### Contributing
Pull Requests, Issues, you know the deal.
