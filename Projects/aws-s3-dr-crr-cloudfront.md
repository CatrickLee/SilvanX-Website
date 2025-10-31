***

## Project: AWS S3 Cross-Region Replication and CloudFront Pilot Light Disaster Recovery

This document details the configuration of a **Pilot Light Disaster Recovery (DR)** strategy for my **SilvanX.com** static website, leveraging S3 Cross-Region Replication (CRR) and a single AWS CloudFront distribution to ensure high availability and continuous HTTPS security during a regional outage.

### I. Architecture Overview (Pilot Light Strategy)

The Pilot Light strategy minimizes costs by keeping a minimal infrastructure ready to be activated. The downside is that this is still a manual deployment.

| Component | Region | Role | Notes |
| :--- | :--- | :--- | :--- |
| **Primary Bucket (Source)** | `us-east-1` | Active Origin for CloudFront. | Uses S3 Standard for high availability. |
| **DR Bucket (Destination)** | `us-west-2` | Passive, mirrored copy (Pilot Light). | Uses S3 One Zone-IA for cost savings. |
| **CRR** | Across regions | Automatically copies new and updated objects from Source to Destination. | CRR requires bucket versioning on both source and destination. |
| **CloudFront Distribution** | Global | **Single point of access.** Handles HTTPS and DNS for `SilvanX.com`. | Hosts the ACM SSL/TLS certificate to maintain security during failover. |

---

### II. Pre-Disaster Configuration (Preparation Phase)

These steps are performed once to prepare the infrastructure for rapid failover.

1.  **S3 CRR and Static Hosting:**
    * Ensured S3 Versioning is enabled on both `SilvanX.com` (Source Bucket) and `SilvanX.com-DR` (Destination Bucket).
    * Enabled **Static Website Hosting** on the DR Bucket (`SilvanX.com-DR`) to allow S3 to handle index documents during the failover.
    * Used **S3 Batch Operations** to copy all existing objects from the source to the destination, as CRR only replicates new objects.
2.  **CloudFront Origin Integration:**
    * The existing CloudFront Distribution is configured with the Primary S3 Bucket endpoint as its active Origin.
    * The **DR S3 Website Endpoint** is added as a second, separate Origin to the same CloudFront distribution.
    * The **Default Cache Behavior** remains pointed to the Primary Origin during normal operation.

---

### III. CRR Policy Configuration (Principle of Least Privilege)

Cross-Region Replication requires both an identity-based policy (IAM Role) and a resource-based policy (Destination Bucket Policy) to function securely.

#### 🔐 A. IAM Role Policy (Attached to Replication Role on Source Bucket)

This policy is attached to the IAM role that the source bucket (`SilvanX.com`) assumes to get and replicate objects.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:ListBucket",
                "s3:GetReplicationConfiguration",
                "s3:GetObjectVersionForReplication",
                "s3:GetObjectVersionAcl",
                "s3:GetObjectVersionTagging",
                "s3:GetObjectRetention",
                "s3:GetObjectLegalHold"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::silvanx.com",
                "arn:aws:s3:::silvanx.com/*",
                "arn:aws:s3:::silvanx.com-dr",
                "arn:aws:s3:::silvanx.com-dr/*"
            ]
        },
        {
            "Action": [
                "s3:ReplicateObject",
                "s3:ReplicateDelete",
                "s3:ReplicateTags",
                "s3:ObjectOwnerOverrideToBucketOwner"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::silvanx.com/*",
                "arn:aws:s3:::silvanx.com-dr/*"
            ]
        }
    ]
}
````

#### 🛡️ B. Destination Bucket Policy (Attached to DR Bucket in us-west-2)

This policy grants explicit resource-level permission to the replication role to write objects to the destination bucket (`SilvanX.com-DR`).

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:InitiateReplication",
                "s3:GetReplicationConfiguration",
                "s3:PutInventoryConfiguration"
            ],
            "Resource": [
                "arn:aws:s3:::silvanx.com",
                "arn:aws:s3:::silvanx.com/*"
            ]
        }
    ]
}
```

-----

### IV. Disaster Recovery Activation (Failover Phase)

In the event of a regional failure in `us-east-1`, the site is quickly recovered by activating the Pilot Light infrastructure.

1.  **Manual CloudFront Origin Switch:**
      * In the AWS CloudFront console, navigate to **Behaviors**.
      * Edit the **Default Cache Behavior** (Path Pattern: `*`).
      * Change the **Origin** from the Primary S3 Bucket (`us-east-1`) to the **DR S3 Bucket** (`us-west-2`).
2.  **Cache Invalidation:**
      * Create a full CloudFront Invalidation (`/*`) to force edge locations to immediately fetch the website root and other files from the new DR Origin, speeding up the failover process.

### V. Recovery Principle and Security Validation

  * **Security Principle:** The recovery is performed by switching the **Origin** inside the *existing* CloudFront Distribution. Since the public DNS record (Route 53 CNAME) always points to CloudFront, the user's browser remains connected to the secure CloudFront edge.
  * **Validation:** When `https://SilvanX.com` loads, the website will display content from the `us-west-2` bucket while **maintaining the HTTPS connection and utilizing the existing SSL/TLS certificate**. This avoids exposing an unsecured HTTP endpoint during a disaster.

-----

Thank you - Cat Silvan
