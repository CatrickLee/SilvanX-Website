
-----

## 💾 Project: AWS S3 Cross-Region Replication and CloudFront Pilot Light Disaster Recovery

This project demonstrates the implementation of a cost-effective **Pilot Light Disaster Recovery (DR)** strategy for the S3 static website hosting the **SilvanX.com** site. This setup utilizes **S3 Cross-Region Replication (CRR)** and **S3 Batch Operations** to ensure data durability and availability in the event of a regional outage.

### I. Architecture Overview and Cost Optimization

| Component | Region | Role | Notes/Reason |
| :--- | :--- | :--- | :--- |
| **Primary Bucket (Source)** | `us-east-1` | Active Origin | Uses **S3 Standard** for high availability and frequent access. |
| **DR Bucket (Destination)** | `us-west-2` | Passive, mirrored copy (Pilot Light) | Uses **S3 One Zone-IA** (Infrequent Access) as a cost-saving measure for the DR copy. |
| **CRR** | Across regions | Continuous Data Sync | Automatically copies new and updated objects from Source to Destination. |
| **DR Storage Class** | N/A | Cost Optimization | S3 One Zone-IA is 20% cheaper than S3 Standard-IA because it stores data in only a single Availability Zone, which is acceptable for this low security risk static website DR copy. |

-----

### II. Implementation Phases

#### Phase 1: Destination Bucket Setup

1.  Ensured that **Bucket Versioning** was enabled on both the primary (Source: `us-east-1`) and DR (Destination: `us-west-2`) S3 buckets.
2.  Created the destination S3 bucket in the secondary AWS Region and **enabled Static Website Hosting** on it to act as the "pilot light" DR site.

#### Phase 2: Initial Data Migration (S3 Batch Operations)

1.  Since CRR only replicates new objects uploaded *after* the rule is created, **S3 Batch Operations** was used to copy existing objects from the source to the destination.
2.  A new **IAM Role** was created specifically for S3 Batch Operations to grant the S3 service the necessary permissions to read and write the existing content.

#### Phase 3: Continuous Replication (CRR)

1.  A **Cross-Region Replication (CRR) rule** was created on the source S3 bucket, pointing to the DR bucket in the secondary region.
2.  A new **IAM role** was created to allow S3 to manage the continuous replication process.
3.  The destination storage class was set to **S3 One Zone-IA** for cost optimization.

#### Phase 4: Disaster Recovery Activation Principle

The Pilot Light DR strategy means the infrastructure is ready to be quickly activated. The recovery process involves updating the existing **CloudFront Origin** to point to the DR S3 bucket, which maintains the HTTPS connection and avoids an insecure fallback.

-----

### III. IAM Role Policies (Least Privilege Principle)

To maintain security and adhere to the **Principle of Least Privilege**, the following two specific IAM roles were created for this project.

#### 1\. S3 Batch Operations Role (Phase 2)

This role allowed the one-time copy of existing objects from the Source to the Destination bucket.

##### Trust Policy: Allows S3 Batch Service to assume the role

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "batchoperation.s3.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

##### Permissions Policy: Grants read from source and write to destination

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:GetObjectTagging"
            ],
            "Resource": "arn:aws:s3:::SilvanX.com/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:PutObjectTagging"
            ],
            "Resource": "arn:aws:s3:::SilvanX.com-DR/*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::SilvanX.com"
        }
    ]
}
```

#### 2\. S3 CRR Role (Phase 3)

This role enabled S3 to perform continuous replication from the Source to the Destination bucket.

##### Trust Policy: Allows S3 Service to assume the role

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "s3.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

##### Permissions Policy: Grants read on source and replicate/write on destination

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:GetReplicationConfiguration",
            "Resource": "arn:aws:s3:::SilvanX.com"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObjectVersion",
                "s3:GetObjectVersionAcl",
                "s3:GetObjectVersionTagging"
            ],
            "Resource": "arn:aws:s3:::SilvanX.com/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ReplicateObject",
                "s3:ReplicateDelete",
                "s3:ReplicateTags"
            ],
            "Resource": "arn:aws:s3:::SilvanX.com-DR/*"
        }
    ]
}
```

-----

**Conclusion:** This project showcased data migration, cost optimization, IAM permissions management, and disaster recovery.

  - Cat Silvan

-----
