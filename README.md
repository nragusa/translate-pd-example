# Overview

Example using Parallel Data feature with Amazon Translate. Note: all work was completed in `us-east-1`.

## Create Parallel Data

Use the file [pd.csv](pd.csv)

```bash
aws s3 cp pd.csv s3://<your bucket here>/
```

Now add the parallel data to be used in your batch translation jobs:

```bash
aws translate create-parallel-data --name my-pd --parallel-data-config S3Uri=s3://<your bucket here>/pd.csv,Format=CSV
```

Monitor the status of this job, it will be available for use once the status returned is `Active`.

```bash
aws translate get-parallel-data --name my-pd
```

## Use the Parallel Data

Amazon Translate needs a role that has access to the S3 bucket. See [Amazon Translate with IAM](https://docs.aws.amazon.com/translate/latest/dg/identity-and-access-management.html) for more details, being sure to specify `translate.amazonaws.com` as the Trusted Service Principal, and a policy that grants access to the S3 bucket (read / write) and optionally to any KMS keys being used for encryption.

Currently, parallel data can only be used with asynchronous batch translation jobs, so its use is limited to `aws translate start-text-translation-job`. For example:

```bash
aws translate start-text-translation-job --job-name pd-example \
    --input-data-config S3Uri=s3://<your bucket here>/source/,ContentType=text/plain \
    --output-data-config S3Uri=s3://<your bucket here>/output/ \
    --data-access-role-arn my-translate-role \
    --source-language-code en \
    --target-language-codes es \
    --parallel-data-names my-pd
```
