# 🚀 AWS CI/CD Pipeline Project Documentation

## 📋 Project Overview

**Project:** Complete CI/CD Pipeline for Static Website Deployment

**Goal:** Build an automated deployment system where a simple `git push` triggers an automatic update of a live website on AWS.

**Live Website:** `http://52.201.230.26`

**Date Completed:** August 16, 2026

---

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         AWS CI/CD Pipeline                             │
│                                                                         │
│  ┌─────────────┐    ┌──────────────┐    ┌─────────────┐    ┌─────────┐ │
│  │   GitHub    │    │  CodeBuild   │    │  CodeDeploy │    │   EC2   │ │
│  │  (Source)   │───▶│  (Build)     │───▶│  (Deploy)   │───▶│ (Server)│ │
│  └─────────────┘    └──────────────┘    └─────────────┘    └─────────┘ │
│        │                  │                   │                  │      │
│        └──────────────────┴───────────────────┴──────────────────┘      │
│                                                                         │
│                    All automated by CodePipeline                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 📂 Project Files

### Folder Structure

```
my-cid-app/
├── index.html                 # Website content
├── appspec.yml               # CodeDeploy configuration
├── buildspec.yml             # CodeBuild configuration
└── scripts/
    ├── install_dependencies.sh   # Install nginx
    └── start_server.sh           # Start nginx
```

---

## 📄 File Contents

### 1. `index.html` - Website

```html
<!DOCTYPE html>
<html>
<head>
    <title>My CI/CD App</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 50px;
            background: #f0f2f5;
        }
        .container {
            background: white;
            padding: 40px;
            border-radius: 10px;
            max-width: 600px;
            margin: 0 auto;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 { color: #ff9900; }
        .success { color: green; font-weight: bold; }
        .version { color: #666; margin-top: 20px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 CI/CD Deployment Successful!</h1>
        <p class="success">✅ Your website is live!</p>
        <p>This app was deployed automatically using AWS CI/CD</p>
        <div class="version">
            <p>Version: 1.0</p>
            <p>Deployed via CodePipeline!</p>
        </div>
    </div>
</body>
</html>
```

---

### 2. `appspec.yml` - CodeDeploy Instructions

```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html/
    overwrite: true
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root
  AfterInstall:
    - location: scripts/start_server.sh
      timeout: 300
      runas: root
```

**What it does:**
- Copies all files to `/var/www/html/`
- Overwrites existing files
- Runs `install_dependencies.sh` before installation
- Runs `start_server.sh` after installation

---

### 3. `buildspec.yml` - CodeBuild Instructions

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 18
    commands:
      - echo "Installing dependencies..."
  pre_build:
    commands:
      - echo "Running security scans..."
      - echo "No vulnerabilities found (simulated)."
  build:
    commands:
      - echo "Building the app..."
      - echo "Build completed on $(date)"
  post_build:
    commands:
      - echo "Packaging artifact for CodeDeploy..."
      - ls -la

artifacts:
  files:
    - '**/*'
  base-directory: .
```

**What it does:**
- Sets Node.js 18 as the runtime
- Simulates security scanning
- Packages all files for deployment
- Runs on Amazon Linux environment

---

### 4. `scripts/install_dependencies.sh`

```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y nginx
```

**What it does:**
- Updates the Amazon Linux package repository
- Installs the nginx web server

---

### 5. `scripts/start_server.sh`

```bash
#!/bin/bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

**What it does:**
- Starts the nginx service
- Enables nginx to start automatically on boot

---

## 🖥️ AWS Services Configured

| Service | Name | Purpose |
|---------|------|---------|
| **EC2** | `my-cicd-server` | Hosts the live website |
| **CodeDeploy** | `my-cicd-app` | Deploys code to EC2 |
| **CodeBuild** | `my-cicd-build` | Tests and builds code |
| **CodePipeline** | `my-cicd-pipeline` | Orchestrates the workflow |
| **IAM Roles** | `CodeDeploy-Service-Role` | Permissions for CodeDeploy |
| | `EC2-CodeDeploy-Role` | Permissions for EC2 instance |

---

## 🔐 IAM Roles

### CodeDeploy Service Role

| Detail | Value |
|--------|-------|
| **Name** | `CodeDeploy-Service-Role` |
| **Policy** | `AWSCodeDeployRole` |
| **Purpose** | Allows CodeDeploy to communicate with EC2 and S3 |

### EC2 CodeDeploy Role

| Detail | Value |
|--------|-------|
| **Name** | `EC2-CodeDeploy-Role` |
| **Policy** | `AmazonS3ReadOnlyAccess` |
| **Purpose** | Allows EC2 to download the CodeDeploy agent and deployment artifacts |

---

## 🔧 EC2 Configuration

| Setting | Value |
|---------|-------|
| **Instance Name** | `my-cicd-server` |
| **AMI** | Amazon Linux 2023 |
| **Instance Type** | `t3.micro` |
| **Security Group** | SSH (22) and HTTP (80) allowed |
| **IAM Role** | `EC2-CodeDeploy-Role` |
| **Public IP** | `52.201.230.26` |

### User Data Script

```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y ruby wget
cd /home/ec2-user
wget https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
```

---

## 🔄 Pipeline Stages

### Stage 1: Source (GitHub)

| Setting | Value |
|---------|-------|
| **Provider** | GitHub (via GitHub App) |
| **Repository** | `godfreymichael527-create/my-cicd-project` |
| **Branch** | `main` |
| **Trigger** | Every push to main branch |

### Stage 2: Build (CodeBuild)

| Setting | Value |
|---------|-------|
| **Provider** | AWS CodeBuild |
| **Project** | `my-cicd-build` |
| **Purpose** | Tests code and creates deployment artifact |

### Stage 3: Deploy (CodeDeploy)

| Setting | Value |
|---------|-------|
| **Provider** | AWS CodeDeploy |
| **Application** | `my-cicd-app` |
| **Deployment Group** | `my-deployment-group` |
| **Strategy** | In-place deployment (AllAtOnce) |

---

## 🔄 Deployment Process

### What Happens When You Push to GitHub

1. **Developer pushes code:**
   ```bash
   git add .
   git commit -m "Update website"
   git push origin main
   ```

2. **CodePipeline detects change:**
   - Watches GitHub for new commits
   - Automatically triggers pipeline

3. **CodeBuild tests code:**
   - Downloads code from GitHub
   - Runs `buildspec.yml` instructions
   - Creates deployment artifact

4. **CodeDeploy deploys to EC2:**
   - Downloads artifact from S3
   - Copies files to `/var/www/html/`
   - Runs `install_dependencies.sh`
   - Runs `start_server.sh`
   - nginx serves the updated website

5. **Website updates live:**
   - No downtime
   - Visitors see updated content immediately

---

## 📁 GitHub Repository

| Detail | Value |
|--------|-------|
| **Repository** | `my-cicd-project` |
| **Owner** | `godfreymichael527-create` |
| **URL** | https://github.com/godfreymichael527-create/my-cicd-project |
| **Branch** | `main` |

---

## 🔑 Key Concepts

| Term | Definition |
|------|------------|
| **CI (Continuous Integration)** | Automatically testing code when pushed to repository |
| **CD (Continuous Deployment)** | Automatically deploying tested code to production |
| **Pipeline** | The automated sequence of stages from source to deployment |
| **DevOps** | Combining development and operations to automate workflows |
| **Infrastructure as Code** | Defining infrastructure through configuration files |

---

## 📊 Cost & Free Tier

| Service | Free Tier | Monthly Limit |
|---------|-----------|---------------|
| EC2 (t3.micro) | ✅ 750 hours | Free for 12 months |
| CodeDeploy | ✅ Free | Unlimited |
| CodeBuild | ✅ 100 minutes | Free build minutes |
| CodePipeline | ✅ 1 pipeline | Free |

**Estimated Monthly Cost: $0**

---

## 🚀 How to Deploy Updates

1. **Edit your website:**
   ```bash
   vim index.html
   ```

2. **Commit and push:**
   ```bash
   git add index.html
   git commit -m "Updated website content"
   git push origin main
   ```

3. **Wait 2-3 minutes**

4. **Refresh browser** at `http://52.201.230.26`

---

## 🎓 What Was Learned

| Skill | Acquired |
|-------|----------|
| AWS EC2 | ✅ |
| AWS CodeDeploy | ✅ |
| AWS CodeBuild | ✅ |
| AWS CodePipeline | ✅ |
| CI/CD Pipeline Configuration | ✅ |
| Git & GitHub Integration | ✅ |
| AWS IAM Roles | ✅ |
| Infrastructure as Code | ✅ |
| DevOps Principles | ✅ |

---

## 🏁 Final Outcome

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│                     ✅ PIPELINE IS LIVE! ✅                     │
│                                                                 │
│  Developer writes code → git push → Pipeline runs → Website   │
│  updates automatically in 2-3 minutes with zero downtime!      │
│                                                                 │
│  URL: http://52.201.230.26                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📝 Next Steps (Optional Enhancements)

- [ ] Add real security scanning (Trivy, Snyk)
- [ ] Add automated testing (unit tests, integration tests)
- [ ] Add database (Amazon RDS)
- [ ] Add custom domain (Route 53)
- [ ] Add SSL/HTTPS (ACM)
- [ ] Deploy containerized application (ECS/EKS)
- [ ] Set up monitoring (CloudWatch)
- [ ] Add notifications (SNS)
- [ ] Create multi-environment pipeline (dev/staging/prod)
- [ ] Convert to Infrastructure as Code (CloudFormation/Terraform)

---

## 📝 Quick Commands Reference

| Action | Command |
|--------|---------|
| Clone repo | `git clone https://github.com/godfreymichael527-create/my-cicd-project.git` |
| Add changes | `git add .` |
| Commit | `git commit -m "message"` |
| Push | `git push origin main` |
| SSH to EC2 | `ssh -i cicdkey.pem ec2-user@52.201.230.26` |

---

**Project Completed: August 16, 2026**

**Built with:** AWS Free Tier Services

---

*This CI/CD pipeline was built using AWS Free Tier services. All services used are eligible for the AWS Free Tier.*

*Documentation generated for portfolio and reference purposes.*