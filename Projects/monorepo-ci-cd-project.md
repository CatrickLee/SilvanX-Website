
-----

# Cloud-Optimized CI/CD Monorepo: SilvanX Static Web Deployment

This project showcases a secure, cost-efficient, and highly automated Continuous Integration/Continuous Deployment (CI/CD) pipeline for hosting static web assets on AWS, adhering closely to the principles of the **AWS Well-Architected Framework**. The architecture is built on a **Monorepo strategy**, allowing multiple independent projects (starting with `SilvanX.com`) to be managed and deployed from a single codebase and GitHub repository.

---

### I. Key Architectural & DevOps Achievements

| Achievement | Skill Demonstrated | SAA/Career Relevance |
| :--- | :--- | :--- |
| **Monorepo Path Filtering** | Advanced GitHub Actions Configuration | **Operational Excellence:** CI/CD only runs when changes affect the specific folder (`SilvanX_Website/`), preventing unnecessary resource consumption and speeding up deployment. |
| **Secure OIDC Authentication** | Identity and Access Management (IAM) | **Security:** Pipeline authenticates to AWS using **OpenID Connect (OIDC)** and an IAM Role (`GH_OIDC_ROLE_ARN`), eliminating the risk of static AWS access keys. |
| **CloudFront Distribution Setup** | Content Delivery Network (CDN) & Caching | **Performance Efficiency:** Utilized Amazon CloudFront for low-latency access, SSL/TLS termination, and controlled **cache invalidation** after deployment. |
| **S3 Object Ownership Integrity** | S3 Permissions and Data Management | **Security & Reliability:** Solved critical access issues by configuring the S3 Bucket's **Object Ownership** to **"Bucket owner enforced."** |

---

### II. CI/CD Workflow Code (`.github/workflows/deploy.yml`)

The following YAML code defines the core automation logic, including the secure configuration and the Monorepo path-filtering rule.

```yaml
name: Deploy SilvanX Static Website

# WHEN TO RUN: Trigger this workflow on every push to the 'main' branch
on:
  push:
    branches:
      - main
    paths:
      # Update 1: ADDED PATH FILTER - ensures job is skipped if no website files change
      - 'SilvanX_Website/**' 

# Configure job-level permissions for OIDC
permissions:
  id-token: write # Required for OIDC authentication
  contents: read # Required to checkout the code

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v4

      - name: Configure AWS Credentials using OIDC
        uses: aws-actions/configure-aws-credentials@v4
        with:
          # ✅ SECURE: Using IAM Role ARN for OIDC
          role-to-assume: ${{ secrets.GH_OIDC_ROLE_ARN }} 
          
          # ✅ SECURE: Using secret for AWS region
          aws-region: ${{ secrets.AWS_REGION }} 

      - name: Deploy files to S3 (Sync)
        # SAA Best Practice: Syncs only the website files to the S3 root
        run: aws s3 sync SilvanX_Website/ s3://${{ secrets.S3_BUCKET_NAME }} --acl bucket-owner-full-control --delete

      - name: Invalidate CloudFront Cache
        # Clears the CDN cache to ensure viewers see the new content immediately
        run: aws cloudfront create-invalidation --distribution-id ${{ secrets.CF_DISTRIBUTION_ID }} --paths "/*"
```

-----

### III. Critical Troubleshooting Log (Demonstrated Problem-Solving)

| Error Encountered | Root Cause | Architectural Solution | AWS Pillar |
| :--- | :--- | :--- | :--- |
| **Initial Deployment Access Denied** (`<Code>AccessDenied</Code>` XML) | **S3 Object Ownership Conflict:** Files uploaded by the IAM Role were not considered "owned" by the Bucket Owner (the AWS account), which is required by CloudFront. | **S3 Configuration Change:** Set the S3 Bucket **Object Ownership** to **"Bucket owner enforced"**. | **Security / Reliability** |
| **Website Still Fails to Load after S3 Sync** | **CloudFront Origin Path Misconfiguration:** CI/CD uploaded files to the **root** of S3, but the CloudFront Origin Path was incorrectly set to a subdirectory (`/SilvanX_Website`). | **Origin Correction:** Deleted the subdirectory path from the **CloudFront Distribution Origin Path**, aligning the CDN's request path with the S3 content location. | **Operational Excellence** |
| **CI/CD Pipeline Runs on Every Commit** | **Missing Efficiency Filter:** Initial workflow lacked the ability to skip runs for non-code changes (e.g., `README.md` updates). | **Workflow Update:** Implemented the **path filter** (`paths: 'SilvanX_Website/**'`) in the `deploy.yml`, successfully bypassing the entire workflow if only irrelevant files are committed. | **Operational Excellence** |

-----

### IV. Repository Structure

This repository is structured as a Monorepo to manage multiple applications efficiently.

```
CatrickLee/SilvanX-Website/
├── SilvanX_Website/          <-- Static website files (index.html, assets/)
├── .github/
│   └── workflows/
│       └── deploy.yml        <-- Single workflow orchestrating all jobs
└── README.md
```

Thank you - Cat Silvan