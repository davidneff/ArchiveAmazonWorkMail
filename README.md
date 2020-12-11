# ArchiveAmazonWorkMail
CF template to facilitate exporting your Amazon WorkMail mailbox content to S3 using the `workmail start-mailbox-export-job` command. The CF template creates the prerequisites listed in the [Export mailbox content](https://docs.aws.amazon.com/workmail/latest/adminguide/mail-export.html) directions. 

## CloudFormation Instructions
Log into the AWS console in the same region as your Amazon WorkMail Organization. Be sure to access the console with a user/role that has admin privelleges.

1. Run the exportResource.yaml CloudFormation template in your AWS account. The template will ask you to provide a name for a new S3 bucket to export your mail into and an IAM role which will be the key administrator for a new KMS key. Be sure to [follow the rules for bucket naming](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules).
2. Collect and save the two output values from the completed CloudFormation stack. These output values, called oKmsKeyArn and oRoleArn, will give you the kms-key-arn and role-arn you need for the workmail export commands.

## CLI Instructions
In the AWS CLI run the following commands, noting there output. Be sure to access the CLI with a user/role that has admin privelleges.

1. Run `aws workmail list-organizations`. This command will give you the WorkMail OrganizationId you need. The OrganizationId will look like "m-abc123456789" but longer. Save this OrganizationId for the next steps.
2. Run `aws workmail list-users --organization-id ORG_ID_HERE`. Be sure to replace `ORG_ID_HERE` with the OrganizationId you got in Step 1. This will return a list of Users. Each user will have an Id associated with them. You will use that Id to export their mailbox. Save those user Id for the next steps.
3. For each of the users/mailboxes you want to export, run `aws workmail start-mailbox-export-job --organization-id ORG_ID_HERE --entity-id USER_ID_HERE --kms-key-arn KEY_ARN_HERE --role-arn ROLE_ARN_HERE --s3-bucket-name BUCKET_NAME_HERE --s3-prefix export`.

   1. Replace `ORG_ID_HERE` with the OrganizationId you got in Step 1.
   2.  Replace `USER_ID_HERE` with on of the User Ids you got in Step 2.
   3. Replace `KEY_ARN_HERE` with the oKmsKeyArn output value from the CloudFormation stack.
   4. Replace `ROLE_ARN_HERE` with the oRoleArn output value from the CloudFormation stack.
   5. Replace `BUCKET_NAME_HERE` with the name of the bucket you used as input for the CloudFormation stack.
 4. Repeat Step 3 for all of the users/mailboxes you want to export.

You can use the AWS CLI to monitor the state of the mailbox export jobs for your Amazon WorkMail organization. To do so run `aws workmail list-mailbox-export-jobs --organization-id ORG_ID_HERE`

Once the export jobs are complete, you can verify and download the exported content by navigating to the S3 bucket in the AWS console. 