# SilvanX.com: Serverless Static Website (AWS S3 & CloudFront)

## Project Overview
This project showcases the deployment of a highly **secure**, **scalable**, and **low-cost** serverless static website for **SilvanX Technologies**.

| Key Goal | AWS Feature |
| :--- | :--- |
| **Serverless Hosting** | No EC2 instances to manage (Zero Infrastructure). |
| **Enhanced Security** | **Origin Access Control (OAC)** and enforced **HTTPS**. S3 bucket is not public. |
| **Global Performance** | **CloudFront CDN** for low-latency delivery and global caching. |

---

## AWS Skills Demonstrated

This architecture demonstrates proficiency in key **AWS Well-Architected Framework** pillars: Security, Performance Efficiency, and Cost Optimization.

### Core Services
* **Amazon CloudFront:** Used as the global Content Delivery Network (CDN) for caching and distribution.
* **Origin Access Control (OAC):** Securely restricts access, ensuring CloudFront is the only entity that can retrieve content from the S3 origin.
* **Amazon S3:** Configured as the non-public static website origin. **Block Public Access** is enabled.
* **Amazon Route 53:** Utilized an **Alias Record** to map the apex domain (`SilvanX.com`) directly to the CloudFront Distribution.
* **AWS Certificate Manager (ACM):** Provisioned the required SSL/TLS certificate to mandate **HTTPS**.
* **Cross-Region Replication (CRR)** Made a Disaster Recovery bucket in different region.
* **Storage Class One-Zone-IA (Infrequent Access)** Select low cost storage for DR Bucket.

---

## Deployment Strategy
The website was deployed manually via the AWS Management Console to gain deep, hands-on experience with service configuration.

**Steps Taken:**
1.  Created the S3 bucket in the `us-east-1` region.
2.  Requested and validated an SSL/TLS certificate via ACM.
3.  Created a CloudFront Distribution, securing the origin with **OAC**.
4.  Created a Hosted Zone in Route 53 and mapped the domain to the distribution using an **Alias Record**.
5.  Uploaded the website assets (HTML/CSS/JS) to the S3 bucket.
6.  Created new S3 bucket in `us-west-2` region.
7.  Configured Cross-Region Replication (CRR) between the two S3 buckets.
8.  Tested the website to ensure proper functionality and security.
9.  Added a CI/CD auto deployment from github to my SilvanX.com AWS S3 bucket.


---

### **Created by Cat Silvan** - WGU Cybersecurity Undergrad

### **Credits**
Original HTML/CSS template by [HTML5 UP](https://html5up.net/massively), released under the Creative Commons (CC BY 3.0) license.