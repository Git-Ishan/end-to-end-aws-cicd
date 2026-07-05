# 🚀 End-to-End AWS CI/CD Pipeline for VProfile Application

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazonaws)
![CodePipeline](https://img.shields.io/badge/AWS-CodePipeline-blue)
![CodeBuild](https://img.shields.io/badge/AWS-CodeBuild-blue)
![Elastic Beanstalk](https://img.shields.io/badge/AWS-Elastic%20Beanstalk-green)
![Amazon RDS](https://img.shields.io/badge/Amazon-RDS-blue)
![Amazon S3](https://img.shields.io/badge/Amazon-S3-yellow)
![Bitbucket](https://img.shields.io/badge/Bitbucket-Repository-blue?logo=bitbucket)
![Java](https://img.shields.io/badge/Java-17-red)
![Maven](https://img.shields.io/badge/Maven-Build-red)

---

## 📖 Project Overview

This project demonstrates an **end-to-end Continuous Integration and Continuous Deployment (CI/CD) pipeline on AWS** for the open-source **VProfile Java application**.

The objective was to automate the complete software delivery lifecycle—from source code management to deployment—using fully managed AWS services.

The pipeline automatically:

- Detects source code changes in Bitbucket
- Builds the Java application using AWS CodeBuild
- Generates a deployable WAR artifact
- Stores the artifact in Amazon S3
- Deploys the application to AWS Elastic Beanstalk
- Connects the deployed application with an Amazon RDS MySQL database

This project helped me understand how modern DevOps teams automate software delivery while following cloud-native deployment practices.

---

## 🏗 Architecture

<p align="center">
<img width="900"  alt="architecture" src="architecture/architecture.png" />
</p>



### Architecture Flow

```
Developer
      │
      ▼
Bitbucket Repository
      │
      ▼
AWS CodePipeline
      │
      ▼
AWS CodeBuild
      │
Build WAR Artifact
      │
      ▼
Amazon S3
      │
      ▼
Elastic Beanstalk
      │
      ▼
Application running on EC2
      │
      ▼
Amazon RDS (MySQL)
```

---

## ☁️ AWS Services Used

| Service | Purpose |
|----------|----------|
| Bitbucket | Source Code Repository |
| AWS CodePipeline | CI/CD Pipeline Orchestration |
| AWS CodeBuild | Build Automation |
| Amazon S3 | Artifact Storage |
| Elastic Beanstalk | Application Deployment |
| Amazon EC2 | Application Hosting |
| Auto Scaling Group | High Availability |
| Application Load Balancer | Traffic Distribution |
| Amazon RDS (MySQL) | Database |
| IAM | Roles & Permissions |
| CloudWatch | Build Logs & Monitoring |

---

# 🔄 CI/CD Workflow

```
Developer
     │
git push
     │
     ▼
Bitbucket
     │
     ▼
AWS CodePipeline
     │
     ▼
AWS CodeBuild
     │
     ▼
Build WAR Artifact
     │
     ▼
Amazon S3
     │
     ▼
Elastic Beanstalk
     │
     ▼
Application Running
     │
     ▼
Amazon RDS
```

---

# 📂 Source Stage – Bitbucket Repository

The VProfile application source code was migrated from GitHub to a Bitbucket repository. Bitbucket acts as the source provider for AWS CodePipeline.

Every new commit pushed to the repository automatically triggers the CI/CD pipeline.

### Screenshot

<p align="center">
<img width="1119" height="699" alt="bitbucket repository" src="screenshots/01-bitbucket repository.png" />
</p>

---

# ⚙ Build Stage – AWS CodeBuild

AWS CodeBuild acts as the Continuous Integration (CI) service.

It performs the following tasks:

- Downloads source code from Bitbucket
- Reads the `buildspec.yml` file
- Installs project dependencies
- Compiles the Java application using Maven
- Generates a deployable WAR artifact
- Uploads the artifact to Amazon S3

### Build Specification

<p align="center">
<img width="1105" height="638" alt="buildspec yml" src="screenshots/02-buildspec.yml.png" />
</p>

The project uses a `buildspec.yml` file to define each build phase:

- Install
- Pre-build
- Build
- Post-build

### CodeBuild Project

<p align="center">
<img width="1106" height="613" alt="codeBuild project" src="screenshots/03-codeBuild project.png" />
</p>

### Successful Build History

<p align="center">
<img width="869" height="482" alt="successful build history" src="screenshots/04-successful build history.png" />
</p>

---

# 📦 Artifact Storage – Amazon S3

After a successful build, AWS CodeBuild stores the generated deployment artifact in an Amazon S3 bucket.

AWS CodePipeline retrieves this artifact during the deployment stage and passes it to Elastic Beanstalk.

### Screenshot

<p align="center">
<img width="1111" height="593" alt="s3 bucket" src="screenshots/05-s3 bucket.png" />
</p>

---

# 🚀 Deployment Stage – AWS Elastic Beanstalk

AWS Elastic Beanstalk provides a managed deployment platform for the Java application.

Instead of manually provisioning EC2 instances and configuring Tomcat, Elastic Beanstalk automates:

- EC2 provisioning
- Application deployment
- Health monitoring
- Auto Scaling
- Load Balancer integration
- Rolling deployments

The deployment policy was configured as **Rolling Deployment** with a **50% batch size**, allowing one EC2 instance to be updated while the other continues serving user traffic.

### Elastic Beanstalk Environment

<p align="center">
<img width="1101" height="637" alt="elastic beanstalk environment" src="screenshots/06-elastic beanstalk environment.png" />
</p>

### Running Application

<p align="center">
<img width="1119" height="641" alt="elastic beanstalk application url" src="screenshots/07-elastic beanstalk application url.png" />
</p>

---

# 🗄 Database Layer – Amazon RDS

The application stores its persistent data in an Amazon RDS MySQL database.

The database schema was initialized using SQL scripts from the VProfile project, creating the required tables before deployment.

Database Tables:

- user
- role
- user_role

### Amazon RDS

<p align="center">
<img width="1102" height="611" alt="amazonrds" src="screenshots/08-amazonrds.png" />
</p>

### Database Verification

<p align="center">
<img width="1119" height="468" alt="database tables" src="screenshots/09-database tables.png" />
</p>

---

# ⚖ Load Balancer

An **Application Load Balancer (ALB)** was automatically provisioned by Elastic Beanstalk to distribute incoming traffic across multiple EC2 instances.

Benefits:

- High Availability
- Traffic Distribution
- Health Checks
- Supports Rolling Deployments

### Screenshot

<p align="center">
<img src="screenshots/10-load balancer.png" width="900">
</p>

---

# 📈 Auto Scaling

Elastic Beanstalk automatically created an Auto Scaling Group for the application.

Configuration used:

- Deployment Policy: **Rolling**
- Batch Size: **50%**
- Running Instances: **2**

This configuration ensures that one instance remains available while the other is updated, reducing application downtime during deployments.

### Screenshot

<p align="center">
<img src="screenshots/11-auto scaling group.png" width="900">
</p>

---

# 💻 EC2 Instances

Elastic Beanstalk provisions and manages the EC2 instances that host the application.

The instances run:

- Java Runtime
- Apache Tomcat
- VProfile Application

Developers don't have to manually configure or manage the infrastructure.

### Screenshot

<p align="center">
<img src="screenshots/12-ec2 instances.png" width="900">
</p>

---

# 🔄 AWS CodePipeline

AWS CodePipeline orchestrates the complete CI/CD workflow.

Whenever new code is pushed to the Bitbucket repository, the pipeline automatically executes the following stages:

1. Source (Bitbucket)
2. Build (AWS CodeBuild)
3. Deploy (Elastic Beanstalk)

This eliminates manual deployments and provides a repeatable deployment process.

### Pipeline Execution

<p align="center">
<img src="screenshots/13-codepipeline.png" width="900">
</p>

---

# 📁 Repository Structure

```
aws-cicd-vprofile
│
├── README.md
├── architecture
│   └── architecture.png
│
├── screenshots
│   ├── application.png
│   ├── autoscaling.png
│   ├── beanstalk.png
│   ├── bitbucket.png
│   ├── build-history.png
│   ├── buildspec.png
│   ├── codebuild.png
│   ├── codepipeline.png
│   ├── database-tables.png
│   ├── ec2.png
│   ├── loadbalancer.png
│   ├── rds.png
│   └── s3.png
│
└── buildspec.yml
```

---

# 🎯 Skills Demonstrated

- AWS CodePipeline
- AWS CodeBuild
- AWS Elastic Beanstalk
- Amazon RDS (MySQL)
- Amazon S3
- Amazon EC2
- Application Load Balancer
- Auto Scaling Groups
- IAM Roles & Policies
- CloudWatch Logs
- Bitbucket
- Git
- Maven
- Java Web Application Deployment
- Continuous Integration (CI)
- Continuous Deployment (CD)

---

# 📚 Key Learnings

During this project I gained practical experience with:

- Building an end-to-end CI/CD pipeline using AWS managed services.
- Migrating source code from GitHub to Bitbucket.
- Creating automated build workflows using AWS CodeBuild.
- Writing and understanding `buildspec.yml`.
- Deploying Java applications using Elastic Beanstalk.
- Configuring rolling deployments for minimal downtime.
- Provisioning and connecting an Amazon RDS MySQL database.
- Managing AWS IAM roles and permissions.
- Troubleshooting build failures using CloudWatch Logs.

---

# 🚀 Future Improvements

Possible enhancements for this project include:

- Containerizing the application using Docker.
- Deploying on Amazon ECS or Amazon EKS.
- Provisioning infrastructure using Terraform.
- Storing secrets in AWS Secrets Manager.
- Integrating SonarQube for code quality analysis.
- Adding security scanning with Trivy.
- Implementing Blue/Green deployments.
- Replacing Bitbucket with GitHub Actions for comparison.

---

# 📌 Acknowledgements

The application used in this project is based on the open-source **VProfile** application created by **hkhcoder**.

This repository focuses on documenting and implementing the AWS CI/CD pipeline, cloud infrastructure, and deployment workflow built around that application.

Original Repository:

https://github.com/hkhcoder/vprofile-project

---

# 👨‍💻 Author

**Ishan Chaturvedi**

B.Tech Computer Science Engineering  
Cloud & DevOps Enthusiast

- GitHub: https://github.com/Git-Ishan
- LinkedIn: *(https://www.linkedin.com/in/ishan-chaturvedi-428094283/)*

---

## ⭐ If you found this project useful, consider giving it a star!
