AWS Disaster Recovery Project: Cross-Region Replication for SilvanX.com

This project demonstrates the implementation of a cost-effective Pilot Light Disaster Recovery (DR) strategy for the S3 static website hosting the SilvanX.com site. This setup utilizes S3 Cross-Region Replication (CRR) and S3 Batch Operations to ensure data durability and availability in the event of a regional outage.

S3 Bucket:
	Region-us-east-1
	DR Region-us-west-2
	Reason: Data redundancy across geographic locations.

Source Bucket Storage Class:
	S3 Standard
	Reason: High availability and frequent access.

Destination Bucket Storage Class:
	S3 One Zone-IA (Infrequent Access)
	Reason: Cost-saving measure for the DR copy.

Phase 1: Make Destination Bucket:

	1. Ensured that Bucket Versioning was enabled on both the primary (Source) and DR (Destination) S3 buckets.
	2. Created the destination S3 bucket in the secondary AWS Region and enabled Static Website Hosting on it to act as the "pilot light" DR site.

Phase 2: Initial Data Migration (S3 Batch Operations):

	1. Since CRR only replicates new objects uploaded after the rule is created, S3 Batch Operations was used to copy existing objects from the source to the destination.
	2. Created a new IAM Role specifically for S3 Batch Operations.
		Reason: This role grants the S3 service the necessary permissions to read and write the existing content.

Phase 3: Continuous Replication (CRR):
	
	1. Created a Cross-Region Replication (CRR) rule on the source S3 bucket (SilvanX.com's origin). The rule points to the DR bucket in the secondary region.
	2. Created new role to allow S3 to manage the continuous replication process.
	3. Implemented cost optimization or the destination storage class by selecting S3 One Zone-Infrequent Access (S3 One Zone-IA).
	Reason: S3 One Zone-IA is 20% cheaper than S3 Standard-IA because it stores data in only a single Availability Zone and it's currently a low security risk static website.

Phase 4: Disaster Recovery Readiness:

	1. Used the Pilot Light DR strategy. The data is continuously replicated to the secondary region, and the infrastructure is in place and ready to be quickly activated by updating a Route 53 record in a failure scenario.

This project showcased data migration, cost optimization, IAM permissions management, and disaster recovery.

- Cat Silvan
