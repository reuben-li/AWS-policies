# Enforce AES encryption on all incoming files

```
{
	"Version": "2012-10-17",
	"Id": "PutObjPolicy",
	"Statement": [
		{
			"Sid": "DenyIncorrectEncryptionHeader",
			"Effect": "Deny",
			"Principal": "*",
			"Action": "s3:PutObject",
			"Resource": "arn:aws:s3:::bucketname/*",
			"Condition": {
				"StringNotEquals": {
					"s3:x-amz-server-side-encryption": "AES256"
				}
			}
		},
		{
			"Sid": "DenyUnEncryptedObjectUploads",
			"Effect": "Deny",
			"Principal": "*",
			"Action": "s3:PutObject",
			"Resource": "arn:aws:s3:::bucketname/*",
			"Condition": {
				"Null": {
					"s3:x-amz-server-side-encryption": "true"
				}
			}
		}
	]
}
```

# Allowing role/user in another AWS account to access S3 bucket

Delegate access to “remote” (other accounts’) user or role:

```{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowExternal",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::1111111111111:role/remote_role"
            },
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket",
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

On the other AWS account, you also need to set policy to allow actions on the particular S3 bucket
```{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1484522264000",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname"
            ]
        }
    ]
}```
