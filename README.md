# pyarrow-wheel-cibuild

## Configure AWS S3 bucket and appveyor.yml

* Open S3 service in AWS Management Console and create new bucket (referenced below as *YOUR_BUCKET*)
* Create new IAM user with permissions to upload new files to *YOUR_BUCKET* bucket:
    - Open IAM Management Console
    - Navigate to Groups and click on Create New Group to create *YOUR_GROUP* group
    - Create *Inline Policy* for *YOUR_GROUP* with following json template:
    ```
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "s3:ListAllMyBuckets"
                ],
                "Resource": "arn:aws:s3:::*"
            },
            {
                "Effect": "Allow",
                "Action": [
                    "s3:ListBucket",
                    "s3:GetBucketLocation"
                ],
                "Resource": "arn:aws:s3:::YOUR_BUCKET"
            },
            {
                "Effect": "Allow",
                "Action": [
                    "s3:PutObject",
                    "s3:PutObjectAcl"
                ],
                "Resource": "arn:aws:s3:::YOUR_BUCKET/*"
            }
        ]
    }
    ```
    - Navigate to *Users* and click Add user to create *YOUR_USER*
    - Under *YOUR_USER* settings navigate to *Security credentials* tab and *Create access key*
    - Copy to some safe place just generated *secret_access_key* and corresponding *Access Key ID*
    - Login under your Appveyor account, navigate to https://ci.appveyor.com/tools/encrypt and encode
    generated *secret_access_key* to secure strings. Put *Access Key ID* and encoded *secret_access_key*
    value into appropriate fields of appveyor.yml `deploy:` section:
    ```
    access_key_id: ACCESS_KEY_ID

    secret_access_key:
      secure: *ENCODED_secret_access_key*
    ```
    - Open *Groups*, navigate to *Users* and click *Add Users to Group* to add *YOUR_USER* into *YOUR_GROUP*
* Update appveyor.yml `deploy:` section with YOUR_BUCKET:
```
bucket: YOUR_BUCKET
```

## Usage of user-defined variables in appveyor.yml

* `pyarrow_version` is used to define the version of pyarrow build and title of output zip package with built python wheel file
* `pyarrow_commit` defines arrow git repo commit to build from
* `parquet_commit` defines parquet-cpp git repo commit to build from
