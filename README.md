# Student Registration Application — AWS Multi-Tier Deployment

A full-stack **Student Registration Application** built using **React, Spring Boot, and MySQL**, deployed on **Amazon Web Services (AWS)** using a multi-tier architecture.

The project demonstrates practical implementation of:

- AWS VPC networking
- Public and private subnets
- Application Load Balancers
- EC2 Auto Scaling
- Amazon RDS MySQL
- NAT Gateway
- Internet Gateway
- Security Groups
- IAM
- AWS Systems Manager Parameter Store
- Nginx reverse proxy
- Custom domain configuration using Route 53
- Frontend and backend automated EC2 deployment

---

# 🌐 Live Application

Custom domain:

```text
http://amzon.cyou
```

WWW domain:

```text
http://www.amzon.cyou
```

> The current deployment uses HTTP. HTTPS/SSL can be added later using AWS Certificate Manager (ACM) and an HTTPS listener on the public Application Load Balancer.

---

# 📌 Project Overview

This project deploys a full-stack Student Registration Application on AWS.

The application contains three main layers:

### Frontend

- React
- Vite
- JavaScript
- Nginx

### Backend

- Java
- Spring Boot
- Maven
- REST API
- Spring Data JPA

### Database

- Amazon RDS
- MySQL

The infrastructure separates the frontend, backend, and database into dedicated private network tiers.

Only the **public frontend Application Load Balancer** is exposed to the Internet.

---

# ✨ Application Features

The application supports:

- Student registration
- Viewing registered students
- Deleting students
- REST API communication
- Persistent database storage
- Frontend load balancing
- Backend load balancing
- EC2 Auto Scaling
- Multi-Availability-Zone application deployment
- Private RDS database
- Secure database password storage
- Custom domain access

---

# 🛠 Technology Stack

## Frontend

```text
React
Vite
JavaScript
HTML
CSS
Node.js
npm
Nginx
```

## Backend

```text
Java
Spring Boot
Maven
Spring Data JPA
REST API
```

## Database

```text
Amazon RDS
MySQL
```

## AWS Services

```text
Amazon VPC
Amazon EC2
Application Load Balancer
EC2 Auto Scaling
Amazon RDS
Internet Gateway
NAT Gateway
Elastic IP
Security Groups
IAM
AWS Systems Manager Parameter Store
Route 53
Launch Templates
Target Groups
```

## Source Control

```text
Git
GitHub
```

---

# 📂 Repository Structure

```text
student-registration-aws/
│
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   ├── vite.config.js
│   └── ...
│
├── backend/
│   ├── src/
│   ├── pom.xml
│   ├── mvnw
│   ├── mvnw.cmd
│   └── ...
│
├── aws/
│   ├── frontend-user-data.sh
│   └── backend-user-data.sh
│
├── .gitignore
└── README.md
```

The repository contains:

- Complete frontend source code
- Complete backend source code
- AWS EC2 bootstrap scripts
- Git configuration
- Project documentation

---

# 🏗 AWS Architecture

The final application architecture is:

```text
                         INTERNET
                             |
                             |
                             v
                       amzon.cyou
                             |
                             v
                       Amazon Route 53
                             |
                             v
              +-----------------------------+
              | Public Application          |
              | Load Balancer               |
              | HTTP :80                    |
              +-------------+---------------+
                            |
                  +---------+---------+
                  |                   |
                  v                   v
          +---------------+   +---------------+
          | Frontend EC2  |   | Frontend EC2  |
          | React + Nginx |   | React + Nginx |
          | us-east-1a    |   | us-east-1b    |
          | HTTP :80      |   | HTTP :80      |
          +-------+-------+   +-------+-------+
                  |                   |
                  +---------+---------+
                            |
                         /api
                            |
                            v
              +-----------------------------+
              | Internal Application        |
              | Load Balancer               |
              | HTTP :8080                  |
              +-------------+---------------+
                            |
                  +---------+---------+
                  |                   |
                  v                   v
          +---------------+   +---------------+
          | Backend EC2   |   | Backend EC2   |
          | Spring Boot   |   | Spring Boot   |
          | us-east-1a    |   | us-east-1b    |
          | Port 8080     |   | Port 8080     |
          +-------+-------+   +-------+-------+
                  |                   |
                  +---------+---------+
                            |
                            | MySQL :3306
                            v
                     +-------------+
                     | Amazon RDS  |
                     | MySQL       |
                     | student_db  |
                     | Private     |
                     +-------------+
```

---

# 🔄 Complete Request Flow

When a user visits:

```text
http://amzon.cyou
```

the request follows:

```text
User Browser
      |
      v
amzon.cyou
      |
      v
Amazon Route 53
      |
      v
Public Frontend ALB :80
      |
      v
Frontend EC2 :80
      |
      v
React + Nginx
      |
      | /api
      v
Internal Backend ALB :8080
      |
      v
Backend EC2 :8080
      |
      v
Spring Boot
      |
      | MySQL :3306
      v
Amazon RDS MySQL
```

---

# 🌎 AWS Region

The infrastructure is deployed in:

```text
Region: us-east-1
US East (N. Virginia)
```

---

# 🌐 VPC Configuration

A custom VPC was created for the application.

```text
Name: studentapp-vpc
CIDR: 10.0.0.0/16
```

The VPC provides an isolated network for the complete application infrastructure.

---

# 🧩 Subnet Architecture

A total of **8 subnets** were created across **2 Availability Zones**.

## us-east-1a

| Subnet | CIDR | Purpose |
|---|---|---|
| public-subnet-1 | 10.0.1.0/24 | Public resources / NAT |
| frontend-private-1 | 10.0.3.0/24 | Frontend EC2 |
| backend-private-1 | 10.0.5.0/24 | Backend EC2 |
| database-private-1 | 10.0.7.0/24 | Amazon RDS |

## us-east-1b

| Subnet | CIDR | Purpose |
|---|---|---|
| public-subnet-2 | 10.0.2.0/24 | Public resources |
| frontend-private-2 | 10.0.4.0/24 | Frontend EC2 |
| backend-private-2 | 10.0.6.0/24 | Backend EC2 |
| database-private-2 | 10.0.8.0/24 | Amazon RDS |

The architecture therefore separates:

```text
Public Tier
Frontend Tier
Backend Tier
Database Tier
```

---

# 🚦 Route Tables

Three route-table configurations are used.

## Public Route Table

```text
Name: studentapp-public-rt
```

Associated subnets:

```text
public-subnet-1
public-subnet-2
```

Routes:

```text
10.0.0.0/16 -> local
0.0.0.0/0   -> Internet Gateway
```

---

# Private Application Route Table

```text
Name: studentapp-private-app-rt
```

Associated with:

```text
frontend-private-1
frontend-private-2
backend-private-1
backend-private-2
```

Routes:

```text
10.0.0.0/16 -> local
0.0.0.0/0   -> NAT Gateway
```

The NAT Gateway allows private application instances to initiate outbound Internet connections.

This is necessary for operations such as:

```text
apt update
apt install
git clone
npm install
Maven dependency downloads
AWS API calls
```

The private instances do not need to accept direct inbound connections from the Internet.

---

# Database Route Table

```text
Name: studentapp-database-rt
```

Associated with:

```text
database-private-1
database-private-2
```

Route:

```text
10.0.0.0/16 -> local
```

The database subnet route table does not require a default Internet route for application database communication.

---

# 🌍 Internet Gateway

Internet Gateway:

```text
studentapp-igw
```

Attached to:

```text
studentapp-vpc
```

The Internet Gateway provides Internet connectivity for resources using the public route table.

---

# 🔁 NAT Gateway

NAT Gateway:

```text
studentapp-nat
```

Location:

```text
public-subnet-1
```

The NAT Gateway uses an Elastic IP address.

It provides outbound Internet access for frontend and backend EC2 instances located in private subnets.

---

# 🔐 Security Groups

Separate Security Groups were created for each application layer.

---

## Public ALB Security Group

```text
studentapp-public-alb-sg
```

Inbound:

```text
HTTP
TCP 80
Source: 0.0.0.0/0
```

This allows Internet users to reach the frontend load balancer.

---

## Frontend Security Group

```text
studentapp-frontend-sg
```

Inbound:

```text
HTTP
TCP 80
Source: studentapp-public-alb-sg
```

Frontend instances accept application traffic from the public ALB.

---

## Internal Backend ALB Security Group

```text
studentapp-internal-alb-sg
```

Inbound:

```text
Custom TCP
Port: 8080
Source: studentapp-frontend-sg
```

The backend load balancer receives API traffic from the frontend tier.

---

## Backend Security Group

```text
studentapp-backend-sg
```

Inbound:

```text
Custom TCP
Port: 8080
Source: studentapp-internal-alb-sg
```

Backend EC2 instances receive requests from the internal Application Load Balancer.

---

## RDS Security Group

```text
studentapp-rds-sg
```

Inbound:

```text
MySQL/Aurora
TCP 3306
Source: studentapp-backend-sg
```

The RDS database therefore accepts application database connections from the backend tier.

---

# 🔒 Security Group Communication Flow

```text
Internet
   |
   | HTTP :80
   v
Public ALB SG
   |
   | HTTP :80
   v
Frontend SG
   |
   | TCP :8080
   v
Internal ALB SG
   |
   | TCP :8080
   v
Backend SG
   |
   | MySQL :3306
   v
RDS SG
```

---

# 🖥 Frontend Tier

The frontend application is built using:

```text
React
Vite
JavaScript
```

Nginx is used as the production web server.

Frontend instances run in:

```text
frontend-private-1
frontend-private-2
```

The frontend EC2 instances are not exposed directly to users.

Users access the frontend through:

```text
Public Application Load Balancer
```

---

# Nginx Configuration

Nginx performs two important functions:

1. Serves the React application.
2. Proxies `/api` requests to the internal backend ALB.

Example configuration:

```nginx
server {
    listen 80;
    server_name _;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://internal-studentapp-backend-alb-1639984916.us-east-1.elb.amazonaws.com:8080/api/;

        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

The frontend uses:

```text
VITE_API_URL=/api
```

This means the browser sends API requests to the same public application endpoint.

Nginx then forwards those requests internally.

---

# ⚙️ Backend Tier

The backend application uses:

```text
Java
Spring Boot
Maven
Spring Data JPA
```

Backend EC2 instances run inside:

```text
backend-private-1
backend-private-2
```

Application port:

```text
8080
```

The backend is not exposed through the public frontend load balancer directly.

Instead:

```text
Frontend EC2
      ↓
Internal Backend ALB
      ↓
Backend EC2
```

---

# 🗄 Amazon RDS MySQL

Amazon RDS provides persistent database storage.

Configuration:

```text
Identifier: studentapp-db
Engine: MySQL
Database: student_db
Port: 3306
Storage: 20 GiB gp3
Public Access: Disabled
```

The RDS instance uses:

```text
studentapp-db-subnet-group
```

containing:

```text
database-private-1
database-private-2
```

This keeps the database inside dedicated private database subnets.

---

# 🔑 Database Credential Management

Database credentials are **not stored directly in this GitHub repository**.

During the project, the application configuration originally contained a hard-coded local database password.

Before pushing the project to GitHub, the configuration was changed to use environment variables:

```properties
spring.datasource.url=${DB_URL:jdbc:mariadb://localhost:3306/student_db}
spring.datasource.username=${DB_USERNAME:root}
spring.datasource.password=${DB_PASSWORD:}
```

For the AWS deployment, the database password is stored using:

```text
AWS Systems Manager Parameter Store
```

Parameter:

```text
/studentapp/db/password
```

The backend retrieves the password during EC2 initialization:

```bash
DB_PASSWORD=$(aws ssm get-parameter \
  --name "/studentapp/db/password" \
  --with-decryption \
  --region us-east-1 \
  --query "Parameter.Value" \
  --output text)
```

This prevents the production database password from being committed to GitHub.

---

# 👤 IAM Configuration

Backend EC2 instances use:

```text
studentapp-backend-role
```

The role allows backend instances to retrieve the required parameter from AWS Systems Manager Parameter Store.

The IAM role is attached to backend EC2 instances through the backend launch template.

---

# ⚖️ Public Frontend Load Balancer

```text
Name: studentapp-frontend-alb
Type: Application Load Balancer
Scheme: Internet-facing
Listener: HTTP :80
```

The ALB runs across:

```text
public-subnet-1
public-subnet-2
```

It distributes incoming requests between healthy frontend EC2 instances.

---

# 🎯 Frontend Target Group

```text
Name: studentapp-frontend-tg
Target Type: Instances
Protocol: HTTP
Port: 80
Health Check Path: /
```

Two healthy frontend instances are registered through the frontend Auto Scaling Group.

---

# ⚖️ Internal Backend Load Balancer

```text
Name: studentapp-backend-alb
Type: Application Load Balancer
Scheme: Internal
Listener: HTTP :8080
```

It runs inside:

```text
backend-private-1
backend-private-2
```

The load balancer distributes API requests between backend EC2 instances.

Because its scheme is **Internal**, it is not intended to be directly accessible from the public Internet.

---

# 🎯 Backend Target Group

```text
Name: studentapp-backend-tg
Target Type: Instances
Protocol: HTTP
Port: 8080
Health Check: /api/users
```

The backend Auto Scaling Group maintains the backend instances registered with this target group.

---

# 📈 Frontend Auto Scaling Group

```text
Name: studentapp-frontend-asg

Minimum Capacity: 2
Desired Capacity: 2
Maximum Capacity: 4
```

Subnets:

```text
frontend-private-1
frontend-private-2
```

Target group:

```text
studentapp-frontend-tg
```

---

# 📈 Backend Auto Scaling Group

```text
Name: studentapp-backend-asg

Minimum Capacity: 2
Desired Capacity: 2
Maximum Capacity: 4
```

Subnets:

```text
backend-private-1
backend-private-2
```

Target group:

```text
studentapp-backend-tg
```

---

# 🚀 Launch Templates

## Frontend Launch Template

```text
studentapp-frontend-lt
```

Configuration includes:

```text
Ubuntu 24.04 LTS
t3.micro
8 GiB gp3
Frontend Security Group
Frontend bootstrap script
```

---

## Backend Launch Template

```text
studentapp-backend-lt
```

Configuration includes:

```text
Ubuntu 24.04 LTS
t3.micro
8 GiB gp3
Backend Security Group
Backend IAM Instance Profile
Backend bootstrap script
```

---

# 📡 REST API

The Spring Boot application provides the following endpoints.

## Get Students

```http
GET /api/users
```

Returns registered students.

---

## Register Student

```http
POST /api/register
```

Creates a new student record.

---

## Delete Student

```http
DELETE /api/users/{id}
```

Deletes a student using its ID.

---

# 🚀 Frontend Automatic Deployment

Frontend instances use:

```text
aws/frontend-user-data.sh
```

During EC2 startup, the script:

1. Updates Ubuntu packages.
2. Installs Nginx.
3. Installs Git.
4. Installs Node.js and npm.
5. Clones the GitHub repository.
6. Opens the frontend project.
7. Creates the frontend environment configuration.
8. Runs `npm install`.
9. Runs `npm run build`.
10. Copies the build to `/var/www/html`.
11. Creates the Nginx configuration.
12. Configures SPA routing.
13. Configures `/api` reverse proxying.
14. Tests Nginx configuration.
15. Enables Nginx.
16. Starts Nginx.

---

# 🚀 Backend Automatic Deployment

Backend instances use:

```text
aws/backend-user-data.sh
```

During EC2 startup, the script:

1. Updates Ubuntu.
2. Installs Java 17.
3. Installs Maven.
4. Installs Git.
5. Installs AWS CLI.
6. Clones the GitHub repository.
7. Retrieves the RDS password from Parameter Store.
8. Creates the runtime Spring Boot database configuration.
9. Builds the backend using Maven.
10. Creates the application JAR.
11. Copies the JAR to `/opt/studentapp`.
12. Creates a systemd service.
13. Enables the service.
14. Starts Spring Boot.

---

# ⚙️ Backend systemd Service

The Spring Boot application is managed as a Linux service.

Example:

```ini
[Unit]
Description=Student Registration Spring Boot Application
After=network.target

[Service]
User=root
WorkingDirectory=/opt/studentapp
ExecStart=/usr/bin/java -jar /opt/studentapp/studentapp.jar
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

This allows the backend application to start automatically when an EC2 instance starts.

---

# 🌐 Custom Domain Configuration

A custom domain was connected after the application deployment was completed.

Domain:

```text
amzon.cyou
```

Domain registrar:

```text
NicNames
```

DNS management:

```text
Amazon Route 53
```

---

# Route 53 Hosted Zone

A public hosted zone was created for:

```text
amzon.cyou
```

Route 53 generated authoritative AWS nameservers.

The domain's nameserver configuration at NicNames was changed to use the Route 53 nameservers.

The resulting DNS architecture is:

```text
NicNames
   |
   | Domain Registration
   v
amzon.cyou
   |
   | AWS Nameservers
   v
Amazon Route 53
   |
   v
Public Frontend ALB
```

---

# Root Domain Record

An Alias A record was created for:

```text
amzon.cyou
```

Configuration:

```text
Record name: root / blank
Record type: A
Alias: Yes
Routing policy: Simple
Region: us-east-1
Target: studentapp-frontend-alb
```

This allows:

```text
http://amzon.cyou
```

to route directly to the public Application Load Balancer.

---

# WWW Domain Record

Another Alias A record was created for:

```text
www.amzon.cyou
```

Configuration:

```text
Record name: www
Record type: A
Alias: Yes
Routing policy: Simple
Region: us-east-1
Target: studentapp-frontend-alb
```

Therefore both:

```text
http://amzon.cyou
```

and:

```text
http://www.amzon.cyou
```

route to the frontend Application Load Balancer.

---

# 🌐 Final Domain Architecture

```text
                   NicNames
              Domain Registrar
                    |
                    v
               amzon.cyou
                    |
              AWS Nameservers
                    |
                    v
              Amazon Route 53
                    |
                    v
          Public Frontend ALB
                    |
              HTTP Port 80
                    |
           +--------+--------+
           |                 |
           v                 v
     Frontend EC2       Frontend EC2
           |                 |
           +--------+--------+
                    |
                 /api
                    |
                    v
          Internal Backend ALB
                    |
           +--------+--------+
           |                 |
           v                 v
      Backend EC2       Backend EC2
           |                 |
           +--------+--------+
                    |
                    v
              Amazon RDS
```

---

# 🔓 Current HTTP Configuration

The current deployment uses:

```text
HTTP :80
```

HTTPS was intentionally not configured during the current implementation.

Current application addresses:

```text
http://amzon.cyou
http://www.amzon.cyou
```

HTTPS can be added later using:

```text
AWS Certificate Manager (ACM)
        ↓
SSL/TLS Certificate
        ↓
Public ALB HTTPS Listener :443
        ↓
HTTP :80 → HTTPS :443 Redirect
```

---

# 🧪 Functional Testing

The complete application was tested after deployment.

---

## Frontend Test

The React frontend successfully loaded through the public Application Load Balancer.

---

## Registration Test

A student was registered using the frontend.

Request path:

```text
Browser
   ↓
Frontend ALB
   ↓
Frontend EC2
   ↓
Nginx /api
   ↓
Backend ALB
   ↓
Backend EC2
   ↓
RDS
```

The registration completed successfully.

---

## Database Verification

The database was connected to using a temporary setup EC2 instance.

The following database was selected:

```sql
USE student_db;
```

Tables were checked:

```sql
SHOW TABLES;
```

Student data was verified using:

```sql
SELECT * FROM user;
```

The registered student was successfully stored in Amazon RDS.

---

## Persistence Test

The frontend page was refreshed.

The registered student remained available.

This confirmed that the application was using persistent RDS storage rather than temporary EC2 storage.

---

## GET Test

```http
GET /api/users
```

successfully returned registered students.

---

## DELETE Test

```http
DELETE /api/users/{id}
```

was tested through the frontend.

The student was successfully deleted.

---

## Backend Health Test

The backend target group used:

```text
/api/users
```

as its health-check endpoint.

Both backend targets reached:

```text
Healthy
```

---

## Frontend Health Test

The frontend target group used:

```text
/
```

as its health-check path.

Both frontend targets reached:

```text
Healthy
```

---

# 🛠 Problems Encountered and Solutions

Several real deployment issues were encountered while building this project.

---

## 1. RDS Configuration / Cost and Free-Tier Considerations

While creating Amazon RDS, the available RDS configuration and cost/free-tier considerations had to be reviewed before proceeding.

The final database configuration used:

```text
MySQL
db.t4g.micro
20 GiB gp3
Private access
```

The database was placed in dedicated private database subnets rather than exposing it publicly.

This highlighted the importance of checking AWS pricing and current account eligibility rather than assuming every resource configuration is free.

---

## 2. Secure Database Password Storage

A database password should not be stored directly in:

```text
application.properties
```

or committed to GitHub.

To solve this, AWS Systems Manager Parameter Store was used.

Parameter:

```text
/studentapp/db/password
```

The backend EC2 instance retrieves the password at startup using its IAM role.

---

## 3. Hard-Coded Password Found Before GitHub Push

Before uploading the project to GitHub, the backend source code was checked for credentials.

A hard-coded local database password was discovered in:

```text
backend/src/main/resources/application.properties
```

Instead of committing it, the configuration was changed to:

```properties
spring.datasource.url=${DB_URL:jdbc:mariadb://localhost:3306/student_db}
spring.datasource.username=${DB_USERNAME:root}
spring.datasource.password=${DB_PASSWORD:}
```

A secret scan was performed before the final Git push.

---

## 4. Frontend Target Initially Unhealthy

After frontend EC2 instances were launched through the Auto Scaling Group, the frontend target group initially reported unhealthy targets.

The EC2 user-data/cloud-init logs were inspected.

The logs showed that the instances were still:

```text
Installing packages
Installing npm dependencies
Building the frontend
Configuring Nginx
```

Nginx eventually started successfully.

After initialization completed, both targets automatically became:

```text
Healthy
```

This demonstrated that a newly launched instance may temporarily fail health checks while its bootstrap script is still running.

---

## 5. Private EC2 Instances Needed Internet Access

Frontend and backend instances were intentionally placed inside private subnets.

However, they still needed outbound Internet access for:

```text
apt
GitHub
npm
Maven
AWS APIs
```

A NAT Gateway was therefore created in a public subnet.

The private application route table was configured with:

```text
0.0.0.0/0 -> NAT Gateway
```

This provided outbound connectivity while keeping application instances private.

---

## 6. Backend Should Not Be Public

Instead of exposing Spring Boot directly to the Internet, an internal Application Load Balancer was created.

The resulting path became:

```text
Frontend EC2
     ↓
Internal Backend ALB
     ↓
Backend EC2
```

The backend load balancer therefore remains within the VPC.

---

## 7. Browser Could Not Directly Use an Internal Backend Endpoint

Because the backend ALB is internal, a user's browser should not be expected to connect directly to it.

Nginx was configured as a reverse proxy.

Frontend:

```text
VITE_API_URL=/api
```

Nginx:

```text
/api
  ↓
Internal Backend ALB
```

This allows users to interact with the API through the public frontend endpoint while backend communication remains internal.

---

## 8. Temporary Setup EC2 Was No Longer Required

A temporary EC2 instance was used during setup and database connectivity testing.

After the complete frontend → backend → RDS flow was verified, the setup instance was no longer required.

It was terminated to avoid leaving unnecessary infrastructure running.

---

## 9. GitHub Repository Preparation

A new repository was created:

```text
student-registration-aws
```

Before pushing:

1. The old `.git` history was removed.
2. A new Git repository was initialized.
3. The branch was changed to `main`.
4. The new GitHub remote was configured.
5. Credentials were checked.
6. `.gitignore` was created.
7. AWS deployment scripts were added.
8. README documentation was created.
9. Files were committed.
10. The project was pushed to GitHub.

---

# 🔐 Git Security

The `.gitignore` prevents common sensitive or generated files from being committed.

```gitignore
# Environment / secrets
.env
.env.*
*.pem
*.key

# Frontend
frontend/node_modules/
frontend/dist/
frontend/.vite/

# Backend
backend/target/
*.jar

# IDE
.vscode/
.idea/
*.iml

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*
```

---

# 📦 GitHub Repository

Repository:

```text
https://github.com/mukitshaikh/student-registration-aws
```

The repository contains:

```text
Frontend source
Backend source
AWS scripts
.gitignore
README documentation
```

---

# 🔐 Security Features

The project implements multiple security controls:

- Frontend EC2 instances in private subnets
- Backend EC2 instances in private subnets
- RDS in private database subnets
- RDS public access disabled
- Internal backend Application Load Balancer
- Tier-specific Security Groups
- Security Group references instead of broad internal public access
- Database password stored outside Git
- AWS Systems Manager Parameter Store
- IAM role for backend EC2
- `.gitignore` for sensitive files
- No PEM/private keys committed
- No production database password committed

---

# 💰 AWS Cost Considerations

Some components used by this architecture may generate AWS charges.

Examples include:

```text
NAT Gateway
Application Load Balancers
Amazon RDS
EC2 instances
Elastic IP-related usage
Data transfer
Route 53 hosted zone
```

AWS Free Tier eligibility depends on the AWS account, resource type, configuration, and current AWS pricing/free-tier rules.

Resources should be stopped or deleted when they are no longer required for the project.

---

# 💻 Local Backend Development

Requirements:

```text
Java 17+
Maven
MySQL/MariaDB
```

Configure:

```bash
export DB_URL="jdbc:mariadb://localhost:3306/student_db"
export DB_USERNAME="root"
export DB_PASSWORD="YOUR_LOCAL_PASSWORD"
```

Run:

```bash
cd backend
mvn spring-boot:run
```

---

# 💻 Local Frontend Development

Requirements:

```text
Node.js
npm
```

Run:

```bash
cd frontend
npm install
npm run dev
```

For local development, create a local `.env` when required:

```text
VITE_API_URL=http://localhost:8080/api
```

The `.env` file should not be committed.

---

# 📊 Infrastructure Summary

| Component | Configuration |
|---|---|
| Region | us-east-1 |
| VPC | studentapp-vpc |
| VPC CIDR | 10.0.0.0/16 |
| Availability Zones | 2 |
| Total Subnets | 8 |
| Public Subnets | 2 |
| Frontend Private Subnets | 2 |
| Backend Private Subnets | 2 |
| Database Private Subnets | 2 |
| Internet Gateway | studentapp-igw |
| NAT Gateway | studentapp-nat |
| Public ALB | studentapp-frontend-alb |
| Internal ALB | studentapp-backend-alb |
| Frontend Target Group | studentapp-frontend-tg |
| Backend Target Group | studentapp-backend-tg |
| Frontend ASG | studentapp-frontend-asg |
| Backend ASG | studentapp-backend-asg |
| Frontend Capacity | Min 2 / Desired 2 / Max 4 |
| Backend Capacity | Min 2 / Desired 2 / Max 4 |
| Frontend Port | 80 |
| Backend Port | 8080 |
| Database Port | 3306 |
| Database | Amazon RDS MySQL |
| Database Name | student_db |
| RDS Public Access | Disabled |
| Secret Storage | AWS SSM Parameter Store |
| DNS | Amazon Route 53 |
| Registrar | NicNames |
| Custom Domain | amzon.cyou |
| WWW Domain | www.amzon.cyou |
| Current Protocol | HTTP |
| Source Control | GitHub |

---

# 📚 What I Learned

This project provided practical experience with:

- Designing a multi-tier AWS architecture
- Creating custom VPC networks
- CIDR planning
- Public and private subnet design
- Route-table configuration
- Internet Gateway configuration
- NAT Gateway configuration
- EC2 deployment
- Launch Templates
- Auto Scaling Groups
- Application Load Balancers
- Internal vs internet-facing load balancers
- Target groups
- Health checks
- Security Group referencing
- Amazon RDS
- Private database deployment
- Spring Boot deployment
- React production deployment
- Nginx configuration
- Reverse proxy configuration
- IAM roles
- AWS Systems Manager Parameter Store
- Protecting credentials
- Git/GitHub
- Custom domains
- Route 53
- DNS nameservers
- Alias records
- Troubleshooting unhealthy targets
- Testing frontend-to-database communication

---

# 🚧 Future Improvements

Possible future improvements include:

### HTTPS

Add an SSL/TLS certificate using:

```text
AWS Certificate Manager
```

and configure:

```text
HTTPS :443
```

on the frontend ALB.

HTTP requests could then redirect automatically to HTTPS.

---

### CI/CD

Implement GitHub Actions so that application changes can automatically:

```text
Build
Test
Deploy
```

---

### Infrastructure as Code

Recreate the infrastructure using:

```text
Terraform
```

or:

```text
AWS CloudFormation
```

---

### Monitoring

Add:

```text
Amazon CloudWatch
CloudWatch Logs
CloudWatch Alarms
```

---

### Auto Scaling Policies

Configure dynamic scaling based on metrics such as:

```text
CPU utilization
ALB request count
```

---

### AWS WAF

AWS WAF could be attached to the public ALB for additional web-application protection.

---

### IAM Hardening

IAM permissions can be further restricted so the backend role can access only the specific Parameter Store parameter required by the application.

---

# 🏁 Final Result

The completed deployment provides the following application path:

```text
http://amzon.cyou
        |
        v
Amazon Route 53
        |
        v
Public Application Load Balancer
        |
        v
Frontend Auto Scaling Group
        |
        v
React + Nginx
        |
        | /api
        v
Internal Application Load Balancer
        |
        v
Backend Auto Scaling Group
        |
        v
Spring Boot
        |
        v
Amazon RDS MySQL
```

The application successfully supports:

```text
Student Registration   ✅
View Students          ✅
Database Persistence   ✅
Delete Student         ✅
Frontend Load Balancing ✅
Backend Load Balancing  ✅
Private RDS             ✅
Auto Scaling Groups     ✅
Secure Password Storage ✅
Custom Domain           ✅
GitHub Repository       ✅
```

---

# 👨‍💻 Author

**Mukit Shaikh**

GitHub:

```text
https://github.com/mukitshaikh
```

Project Repository:

```text
https://github.com/mukitshaikh/student-registration-aws
```

Live Application:

```text
http://amzon.cyou
```

```text
http://www.amzon.cyou
```

---

## ⭐ Project Status

```text
AWS Infrastructure     : Completed
Frontend Deployment    : Completed
Backend Deployment     : Completed
RDS Integration        : Completed
Load Balancing         : Completed
Auto Scaling           : Completed
Functional Testing     : Completed
GitHub Documentation   : Completed
Custom Domain          : Configured
HTTPS / SSL            : Future Improvement
```
