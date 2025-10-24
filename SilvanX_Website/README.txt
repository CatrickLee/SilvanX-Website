# SilvanX.com Serverless Static Website (AWS S3 & CloudFront)

## 🔗 Live Site
[SilvanX.com](https://www.silvanx.com) 

## 💡 Project Overview:
This project demonstrates the deployment of a highly secure, scalable, and low-cost serverless static website for **SilvanX Technologies**. The architecture leverages core Amazon Web Services (AWS) solutions, aligning with best practices for the **AWS Solutions Architect - Associate** certification.

Key Goals Achieved:
1.  Serverless Hosting: Zero infrastructure to manage (no EC2 instances).
2.  Security: Enforced HTTPS with a custom domain. S3 content is not publicly exposed.
3.  Performance: Global content delivery and caching via a Content Delivery Network (CDN).

---

## 🛠️ AWS Architecture & Demonstrated Skills:
| Amazon S3 | Configured as the Origin for the static website content.
| Ensured Block Public Access is enabled for security.
| Durability/Availability (Storage Class), Security (Access Control, Bucket Policies)
| Amazon CloudFront | Implemented as a Content Delivery Network (CDN) to cache content at Edge Locations for high performance and low latency.
| Origin Access Control (OAC) | Crucial Security Implementation: Used to secure the S3 bucket. CloudFront is the only entity allowed to retrieve objects from S3, ensuring the S3 bucket remains non-publicly accessible.
| Security Best Practices, Access Management, Defense-in-Depth.
| AWS Certificate Manager (ACM) | Provisioned a Public Certificate to enable secure communication (HTTPS) between the user and the CloudFront distribution.
| Security (Encryption in Transit), SSL/TLS Management.
| Amazon Route 53 | Configured the DNS record for `SilvanX.com` using an Alias Record to point to the CloudFront distribution endpoint.
| High Availability (Alias Records), DNS Management.
| Fully decoupled serverless and stateless web tier.
| Architectural Design, Well-Architected Framework (Cost Optimization).

---

## ⚙️ Deployment Strategy
The website was deployed manually via the AWS Management Console to gain hands-on experience with service configuration.

Steps:
1.  Created the S3 bucket in a single region (us-east-1).
2.  Requested and validated an SSL/TLS certificate via ACM.
3.  Created a CloudFront Distribution, linking to the S3 bucket using Origin Access Control (OAC) for enhanced security.
4.  Created a Hosted Zone in Route 53 and added an Alias Record to map `SilvanX.com` to the CloudFront Distribution URL.
5.  Uploaded the website assets (HTML/CSS/JS) to the S3 bucket.

---
Created by Catrick Lee - WGU Cybersecurity Undergrad

## 📜 Credits
* The original HTML/CSS template used for the frontend static site was the "Massively" template by **HTML5 UP**, released under the Creative Commons (CC BY 3.0) license. The focus of this repository is the AWS cloud architecture and deployment.
