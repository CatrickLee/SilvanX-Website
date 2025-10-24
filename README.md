# SilvanX.com Serverless Static Website (AWS S3 & CloudFront)

🔗 Live Site
[SilvanX.com](https://www.silvanx.com) 

💡 Project Overview:
This project demonstrates the deployment of a highly secure, scalable, and low-cost serverless static website for SilvanX Technologies. The architecture uses core AWS solutions.

Key Goals Achieved:
1.  Serverless Hosting: Zero infrastructure to manage (no EC2 instances).
2.  Security: Enforced HTTPS with a custom domain. S3 content is not publicly exposed.
3.  Performance: Global content delivery and caching via a Content Delivery Network (CDN).

---

🛠️ Core AWS Architecture & Demonstrated Skills:
Deploys a secure, highly-available Serverless Static Website, demonstrating proficiency in key AWS SAA concepts and cloud security best practices.

Skills:
Amazon CloudFront: Used for global caching and low-latency delivery.
Origin Access Control: Secured the S3 origin, making CloudFront the only entity authorized to retrieve content.
Amazon S3: Configured as the non-public website Origin with Block Public Access enabled.
Amazon Route 53: Utilized Alias Records for DNS resolution, pointing the apex domain (SilvanX.com) to the CloudFront distribution.
AWS Certificate Manager: Provisioned SSL/TLS for mandated HTTPS.

Cloud Architecture:
Designed a fully decoupled web tier with zero infrastructure to manage.
Prioritized Security, Performance Efficiency, and Cost Optimization.

---

⚙️ Deployment Strategy:
The website was deployed manually via the AWS Management Console to gain hands-on experience with service configuration.

Steps:
1.  Created the S3 bucket in a single region (us-east-1).
2.  Requested and validated an SSL/TLS certificate via ACM.
3.  Created a CloudFront Distribution, linking to the S3 bucket using Origin Access Control (OAC) for enhanced security.
4.  Created a Hosted Zone in Route 53 and added an Alias Record to map `SilvanX.com` to the CloudFront Distribution URL.
5.  Uploaded the website assets (HTML/CSS/JS) to the S3 bucket.

---

Created by Cat Silvan - WGU Cybersecurity Undergrad

---

Credits:
The original HTML/CSS template used for the frontend static site was the "Massively" template by **HTML5 UP**, released under the Creative Commons (CC BY 3.0) license.
