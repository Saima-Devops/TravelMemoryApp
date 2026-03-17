# Travel Memory Blog App
**This guide explains how to deploy the Travel Memory MERN-stack application using AWS services.** 

This is a Travel Memory Blog MERN Stack application, deployed using a scalable and highly available cloud architecture on Amazon Web Services (AWS), including high availability, SSL, auto scaling, and domain integration.

The application follows a three-tier architecture consisting of the presentation layer (frontend), application layer (backend), and data layer (database).

## Project Overview

The Travel Memory application is a full-stack MERN application that allows users to store and manage travel memories. This guide explains how to:
- Deploy frontend and backend on AWS EC2
- Connect to MongoDB Atlas
- Configure Nginx as a reverse proxy
- Enable HTTPS using AWS ACM
- Scale using Launch Templates and Auto Scaling Groups
- Attach an Application Load Balancer
- Connect a custom domain via Cloudflare

<img width="712" height="681" alt="Travel_Memory_Blog drawio" src="https://github.com/user-attachments/assets/f0133fbe-02f8-4db4-91ba-9680769e344b" />

## Tech Stack

- Frontend: React.js
- Backend: Node.js, Express.js
- Database: MongoDB Atlas
- Cloud: AWS EC2, ALB, ASG, ACM
- Web Server: Nginx
- Process Manager: PM2
- Domain & DNS: Cloudflare
- SSL:  AWS ACM

## Simple Workflow Model

- nginx → serves app
- PM2 → runs backend
- ALB → traffic manager
- ACM → HTTPS
- ASG → scaling
- Cloudflare → DNS

## Architecture Setup
<img width="592" height="466" alt="Arch-3" src="https://github.com/user-attachments/assets/0048a931-3c6e-4801-aa6a-ddd5bb42e5da" />

-----------

# 🌍 TRAVEL MEMORY APP DEPLOYMENT — STEP-BY-STEP

## PHASE 1 — DATABASE SETUP (MongoDB Integration)
**MongoDB Atlas Setup & Compass Connection** 

- Log in to Mongodb Atlas (https://cloud.mongodb.com)
- Create Organization & Project
- Create Cluster
    - Name: TravelMemory-cluster
    - Select M0 Free Plan
    - Click Create Deployment
- Create Database User
    - Go to Database Access
    - Add new user (username + password)
- Configure Network Access
      Add IP:    0.0.0.0/0  (For production, restrict to trusted IPs only)
- Connect MongoDB Compass
   - Click Connect → Compass
   - Copy the connection string
     (mongodb+srv://travelmemoryuser:FK********TPx@travelmemory-cluster.wn2x6ze.mongodb.net/?retryWrites=true&w=majority)

<img width="692" height="566" alt="Arch-4" src="https://github.com/user-attachments/assets/095462b1-a3bc-455d-a03f-0727969db3d1" />
<img width="692" height="566" alt="Arch-4" src="https://github.com/user-attachments/assets/c381e5fd-deef-4998-bfe1-b2f97efdc6a9" />

-----------

# PHASE 2 — EC2 BASE SERVER (Backend Setup on AWS EC2)

## Step 2.1 — Launched EC2 Instance for Backend
- Name: travel-memory-be
- Region: ap-south-1 (Mumbai Region)
- OS: Ubuntu 24 (Latest)
- Type: t2.micro

**Security Group:**
- 22 (SSH)
- 80 (HTTP)
- 443 (HTTPS)
- 3001 (TEMPORARY for backend test)   
<img width="800" height="500" alt="8" src="https://github.com/user-attachments/assets/36b35e49-e06c-4cc1-9757-5b2c9274c958" />
**(Connect via SSH)**

------

## Step 2.2 — Connect & Install the Software/Dependencies/Required Packages (Backend Setup)

```bash
## Update System Packages
sudo apt update -y
sudo apt install -y nginx git curl

## Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

## Verify Installation
nodejs -v
npm -v
```
<img width="767" height="397" alt="a1" src="https://github.com/user-attachments/assets/7107ec23-8781-4cfa-b280-5a2960a349e5" />
<br>
✅Node is working🎉

-----------
# PHASE 3 — BACKEND DEPLOYMENT 

Here, I am gonna clone the source repo, deploy the Node.js backend & connect it to the MongoDB.

## Step 3.1 — Deploy Backend Code
```bash
sudo bash
cd ~
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
cd TravelMemory/backend
```
## Step 3.2 — Step-2 In .env File
```bash
nano .env
```

Add the following:
```bash
PORT=3001
MONGO_URI='mongodb+srv://travelmemoryuser:FK********TPx@travelmemory-cluster.wn2x6ze.mongodb.net/?retryWrites=true&w=majority)'
```
<img width="755" height="611" alt="a3" src="https://github.com/user-attachments/assets/c46e91ce-7e70-49d6-8852-b91186288b8c" /><br>

Save and exit.

------

## Step 3.3 — Install Dependencies
```bash
npm install
```

## Step 3.4 — Start Backend Server
```bash
node index.js
```
<img width="886" height="505" alt="a5" src="https://github.com/user-attachments/assets/1f34e788-79da-4641-a9de-98ed20fee31f" /><br><br>

✅ Backend is working properly 🎉

-----------

# PHASE 4 — FRONTEND SETUP + NGINX PROXY

Here I am going to:
- Build **React app**
- Serve it via nginx (this is the best practice)
- Proxy API requests to the backend
- React dev server is NOT for production
- nginx is fast & standard

## Step 4.1 — Frontend Build - Frontend Deployment on AWS EC2

- Launched EC2 Instance as a Frontend
- Name:  travel-memory-fe
- Launch Ubuntu as an AMI (latest)
- Connect via SSH

✅Frontend EC2 Security Group: **Port 3000** is open in EC2 security group

------

## Step 4.2 — Installed Required Packages (Frontend Setup)
```bash
sudo apt update -y
sudo apt install nodejs -y
sudo apt install npm -y
```
**Verify:**
```bash
nodejs -v
npm -v
```

## Step 4.3 — Deployed Frontend Code
```bash
sudo bash
cd ~
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
cd TravelMemory/frontend
```

## Step 4.4 — Create .env File
```bash
nano .env
```
Added the following:
```bash
REACT_APP_BACKEND_URL=http://<backend_EC2_PUBLIC_IP>:3001
```
Save and exit.

------

## Step 4.5 — Installed Dependencies
```bash
## Update the Server:
sudo apt update -y

## Install npm
npm install
```
## Step 4.5 — Start Frontend Server
```bash
npm start
```
Frontend is running on:
```bash
http://<frontend_EC2_PUBLIC_IP>:3000
```

<img width="758" height="877" alt="a9" src="https://github.com/user-attachments/assets/c8444c89-baa4-4ed5-988e-da19e27263d4" />
<img width="768" height="722" alt="a10" src="https://github.com/user-attachments/assets/b8d3c0ed-f7e3-49ff-922d-606cf254467b" />

(Database has been updated)


<img width="767" height="657" alt="a11" src="https://github.com/user-attachments/assets/f69a71e6-08f6-4bad-beb7-eb9532f5bab1" />

✅ Frontend is working perfectly 🎉

------

# PHASE 5 — Nginx Configuration as a Reverse Proxy on Frontend

This section explains how to configure Nginx as a reverse proxy for the **Travel Memory frontend** running on port 3000.
**Frontend (Port 3000 → Port 80)**

## Step 5.1 — Install and Set Up Nginx on EC2
```bash
sudo bash
cd ~
apt update -y
apt install nginx -y
```

## Step 5.2 — Edit Nginx Configuration

Open the default configuration file:
```bash
nano /etc/nginx/sites-available/default
```
Remove the existing code and paste the following:
```bash
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://<frontend_EC2_PUBLIC_IP>:3000;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Save and exit.

## Step 5.3 — Restart and Test Nginx
```bash
nginx -t
systemctl reload nginx
```
Test in Browser (without port):
```bash
http://<frontend_EC2_PUBLIC_IP>
```
<img width="1372" height="776" alt="9" src="https://github.com/user-attachments/assets/e0e96606-776d-4e6e-afcc-624a41092e6d" />


✅ Frontend is working without port 🎉

------

# PHASE 6 — Creating a Reverse Proxy Using Nginx (Backend)

This section explains how to configure Nginx as a reverse proxy for the Travel Memory backend running on port 3001.

## Step 6.1 — Install and Set Up Nginx on EC2
```bash
sudo bash
cd ~
apt update -y
apt install nginx -y
```
------

## Step 6.2 — Edit Nginx Configuration

Open the default configuration file:
```bash
nano /etc/nginx/sites-available/default
```
Remove the existing code and paste the following:
```bash
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://<EC2_PUBLIC_IP>:3001;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Save and exit.

------

## Step 6.3 — Restart and Test Nginx
```bash
nginx -t
systemctl reload nginx
```

## Step 6.4 — Test Backend in Browser
```bash
http://<EC2_PUBLIC_IP>/trip
```

<img width="1267" height="477" alt="a13" src="https://github.com/user-attachments/assets/8330d887-f8aa-4225-8b11-832b5f5e994d" />

------

# PHASE 7 — Running Frontend & Backend with Custom Domain
This section shows how to run:

Frontend on: https://cloudexpert.store<br>
Backend API on: https://api.cloudexpert.store


## Step 7.1 — DOMAIN SETUP (Cloudflare Configuration)
Do the following settings on Cloudflare:

| Type | Name | Value (EC2 Public IP) |
|------|------|-----------------------|
| A    | @    | <EC2_PUBLIC_IP>       |
| A    | www  | ALB-DNS       |
| A    | api  | <EC2_PUBLIC_IP>       |


## Step 7.2 — Update the Nameservers

In the domain DNS provider, **update the nameservers**:

<img width="763" height="485" alt="Screenshot 2026-02-16 201423" src="https://github.com/user-attachments/assets/644bfd5d-fb55-40c4-b8e5-c4e72d9a0be2" />

<img width="1351" height="440" alt="new2" src="https://github.com/user-attachments/assets/ea8271a0-bbe7-4b1a-b252-133f12961e2e" />


Copy the nameservers from Cloudflare and paste them to the DNS provider (GoDaddy)<br>
(Wait a few minutes for DNS to propagate)

------

## Step 7.3 — Update Nginx Config Files
```bash
nano /etc/nginx/sites-available/default
```

Paste the following configurations in each config file of frontend & backend:

```bash
# Frontend - cloudexpert.store
server {
    listen 80;
    server_name cloudexpert.store www.cloudexpert.store;

    location / {
        proxy_pass http://<EC2_PUBLIC_IP>:3000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
and

```bash
# Backend - api.cloudexpert.store
server {
    listen 80;
    server_name api.cloudexpert.store;

    location / {
        proxy_pass http://<EC2_PUBLIC_IP>:3001;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

<img width="1136" height="657" alt="6" src="https://github.com/user-attachments/assets/46ad4519-5614-418b-a438-36f5e1988958" />

<img width="1136" height="657" alt="7" src="https://github.com/user-attachments/assets/937ec5b6-76e6-45d1-b392-857e392e7ada" />

------

## Step 7.4 — Enable the Configuration for Frontend & Backend
```bash
nano /etc/nginx/sites-available/default

nginx -t
systemctl reload nginx
```
------

## Step 7.5 — Test in Browser
Frontend:
```bash
http://cloudexpert.store
```
<img width="953" height="501" alt="a15" src="https://github.com/user-attachments/assets/73305fe4-f895-4148-829f-f44a932e2dcd" /><br><br>

**🎉 Domain is routing properly!**

------

# PHASE 8 —  SSL / HTTPS Strategy 
**Install and Enable Certbot**

This section explains how to secure your frontend domain using HTTPS with Certbot.

## Step 8.1 — Install Certbot
```bash
sudo bash
cd ~
apt update
apt install certbot python3-certbot-nginx -y
```

## Step 8.2 — Generate and Configure SSL Certificates
```bash
sudo certbot --nginx -d cloudexpert.store -d www.cloudexpert.store
```

**Certbot will:* — *

- Verify domain ownership
- Generate SSL certificates
- Automatically update your Nginx configuration to use HTTPS

------

## Step 8.3 — Final Nginx Configuration After SSL
```bash
server {
    server_name cloudexpert.store www.cloudexpert.store;

    location / {
        proxy_pass http://<EC2_PUBLIC_IP>:3000;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/cloudexpert.store/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/cloudexpert.store/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = www.cloudexpert.store) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    if ($host = cloudexpert.store) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    server_name cloudexpert.store www.cloudexpert.store;
    return 404; # managed by Certbot
}
```

 ## Step 8.4 — Verify DNS Records
```bash
dig +short cloudexpert.store
dig +short www.cloudexpert.store
```

## Step 8.5 — Reload and Test Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```
------

# PHASE 9 — Access Your Website Securely

🌐 cloudexpert.store <br>
🌐 www.cloudexpert.store

<img width="1116" height="681" alt="new3" src="https://github.com/user-attachments/assets/4ed31550-0e5c-4141-a845-e5a376e2b7f9" />

<img width="1616" height="297" alt="nnnnnn" src="https://github.com/user-attachments/assets/32b18321-0ad2-48f5-8e35-8958dc974129" />

------

# PHASE 10 — Install Certbot and Enable SSL for Backend API

This section explains how to secure your Backend subdomain using HTTPS with Certbot.

## Step 10.1 — Install Certbot
```bash
sudo bash
cd ~
apt update
apt install certbot python3-certbot-nginx -y
```

 ## Step 10.2 — Generate and Configure SSL Certificates
```bash
sudo certbot --nginx -d api.cloudexpert.store
```

**Certbot will:**

- Verify domain ownership
- Generate SSL certificates
- Automatically update your Nginx configuration to use HTTPS

------

## Step 10.3 — Final Nginx Configuration After SSL
```bash
server {
    server_name api.cloudexpert.store;

    location / {
        proxy_pass http://<EC2_PUBLIC_IP>:3001;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/api.cloudexpert.store/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/api.cloudexpert.store/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = api.cloudexpert.store) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name api.cloudexpert.store;
    return 404; # managed by Certbot

}
```

## Step 10.4 — Verify DNS Records
```bash
dig +short api.cloudexpert.store
```

## Step 10.5 — Reload and Test Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```

## Step 10.6 — Access Your Backend API Securely

🌐 https://api.cloudexpert.store

<img width="1907" height="1022" alt="NN1" src="https://github.com/user-attachments/assets/77493335-25bf-45e6-b612-15c0a25f2ccd" />

<img width="1898" height="386" alt="new4" src="https://github.com/user-attachments/assets/8666f409-0e17-4e2b-a47d-bb8e5d6849f0" /><br><br>


**The backend api loads with a 🔒 lock icon, which means SSL setup is successful! 🎉**

------

# PHASE 11 — MULTIPLE INSTANCES & LOAD BALANCER SETUP

This section explains how to scale the Travel Memory application by creating multiple EC2 instances using an AWS Launch Template.

**Purpose**
- Launch Template → New EC2 (To scale up the frontend server)
- Create multiple instances (as needed/depends on the maximum requirement) 
- Put ALB at the frontside

**Why Needed?**
High availability & Scalability


## Step 11.1 — Creating Multiple Instances of Frontend Server

I will select my existing EC2 instance (frontend), which was created earlier for the Travel Memory application.

**Actions → Images & Templates → Create template from instance**

- Enter the template name: tvm-fe-tmp
- Create launch template.
  
<img width="1381" height="639" alt="17" src="https://github.com/user-attachments/assets/ea0a1013-d06f-4ec1-a77c-aaddc9cd87a8" />

------

## Step 11.2 — Launch Instances from the Template

**Actions → Launch instance from template**

- Select the source template: tvm-fe-tmp
- Click Launch instance.

## Step 11.3 — Verify Instances

In the AWS Console, now there are more than 2 instances running under the same template.

<img width="1367" height="641" alt="19" src="https://github.com/user-attachments/assets/6497b5d0-9b9c-4bbd-beb4-ba2895b8b0c3" />


**Example access URL:**
```bash
http://<EC2_PUBLIC_IP>
```

------

# PHASE 12 — CREATING & ATTACHING LOAD BALANCER

This guide explains how to create and attach an Application Load Balancer (ALB) to the EC2 instances running the Travel Memory application.


## Step 12.1 — Configure the Load Balancer

- Navigate to: EC2 → Load Balancers
- Click Create Load Balancer.
- Select Application Load Balancer (ALB).
- Set the load balancer name: travel-memory-frontend-lb
- Set the scheme: internet-facing
- Set the IP address type: IPv4
- Under Availability Zones, I selected the same AZs as my EC2 instances.


## Step 12.2 — Create a Target Group

1. Under Listeners and routing, click Create a target group.
2. Choose: Target type: Instances
3. Set: Protocol: HTTPS Port: 443
4. Select the two EC2 instances running the Travel Memory application.
5. Click Include as pending.
6. Click Create target group.

>  HTTPS should be terminated at the Load Balancer using ACM, while backend traffic remains HTTPS.

## Step 12.3 — Create the Load Balancer

1. Return to Load Balancers.
2. Attach the target group you created.
3. Click Create load balancer.


<img width="1363" height="640" alt="21" src="https://github.com/user-attachments/assets/fcff5efe-84f2-4dc4-a856-42df46e8d063" /><br><br>


## Step 12.4 — Test the Load Balancer

1. Copy the DNS name of the load balancer, for example: travel-memory-alb-1368....ap-south-1.elb.amazonaws.com

2. Paste it into your browser: http://travel-memory-alb-1368....ap-south-1.elb.amazonaws.com
   
3. Verify that the Travel Memory application loads successfully.

<img width="1917" height="1021" alt="14" src="https://github.com/user-attachments/assets/1de7df76-1281-44d6-b514-fe351c224e94" /><br><br>

**Load Balancer is now correctly distributing traffic across all EC2 instances!**


------

# PHASE 13 — AWS Certificate Manager (ACM) – SSL Certificate Details

This document records the issued SSL certificate used for securing the Travel Memory application.

- **Certificate ID:**  
 `Certificate ID: ea16****************`

- **ARN:** 
`arn:aws:acm:ap-south-1:251478238405:certificate/ea16****************`

- **Type:** Amazon Issued

- **Region:** Asia Pacific (Mumbai)

- **Account:** Saima Usman

- **Status:** ✅ Issued

- **In Use:** Yes

![avi-5-a](https://github.com/user-attachments/assets/ec6ffef1-1fd4-46ea-91a8-89f205897ab0)


--- 

 ## 🌐 Domain Secured

| Domain               | Status   |
|----------------------|----------|
| lb.cloudexpert.store | Success  |

---
## Renewal

-**Renewal Eligibility:** Eligible <br>
-**Renewal Status:** Automatic (AWS-managed)

---

## Validation Method

**Type:** DNS (CNAME)

**Record Name:**
_da979cbccb069db6bbf69dbaf1308772.lb.cloudexpert.store

---

## Applying Certificate to Load Balancer

1. EC2 → Load Balancers
2. Select ALB
3. Edit Listeners
4. Add or modify: HTTPS: 443 → Forward to target group
5. Select the issued ACM certificate.

<img width="1918" height="388" alt="new5" src="https://github.com/user-attachments/assets/fe1b4926-6248-4e48-a69f-5a86307b69f6" />


🎉 **My domain is now secured with HTTPS using AWS ACM!**

---------

# PHASE 14 — Auto Scaling Group (ASG) Setup for Travel Memory Application

This document describes the Auto Scaling Group configuration used to scale the Travel Memory frontend instances automatically.

## Auto Scaling Group Details

- **Auto Scaling Group Name:**  
  `travel-memory-frontend-asg`

- **Region:**  
  Asia Pacific (Mumbai)

- **Account:**  
  Saima Usman

- **Launch Template Used:**  
  `travel-memory-frontend-template`

- **Last Updated:**  
  1 minute ago

------

## Capacity Settings

-**Desired Capacity:** 1  
- **Minimum Capacity:** 1  
- **Maximum Capacity:** 2 *(recommended for scaling)*

---

## Instance Status

- Instances are launched automatically based on the launch template.
- Health checks ensure only healthy instances receive traffic from the Load Balancer.

---

## Integration

- The ASG is attached to the Application Load Balancer target group: travel-memory-frontend-tg

---

## Benefits

- Automatic instance replacement if one fails.
- Automatic scaling based on traffic load.
- Zero-downtime deployments when combined with rolling updates.

🎉 **Travel Memory application is now fully scalable and highly available!**

---

# PHASE 15 —Running Frontend & Backend with PM2 (Professional way)

### Backend with PM2

```bash
cd ~/TravelMemory/backend
pm2 start index.js --name "travel-backend"
pm2 startup
pm2 save
```

---

### Frontend with PM2

```bash
cd ~/TravelMemory/frontend
npm install
npm run build
sudo npm install -g serve
pm2 start serve --name "travel-frontend" -- -s build -l 3000
pm2 save
```

---

### Verify

```bash
pm2 list
```

Expected:

```
travel-backend<br>
travel-frontend
```

![avi-6](https://github.com/user-attachments/assets/b6471744-63f5-4f09-a348-32d9dd8893d5)

![avi-7](https://github.com/user-attachments/assets/a0ab7ee8-4966-4aca-bcc1-171248cae0e1)


---

# 🚀 Deployment Completed!

Travel Memory application is now:<br>
✅ Secure (HTTPS)<br>
✅ Scalable (ASG + ALB)<br>
✅ Highly Available<br>
✅ Production-ready<br>

---

# Summary (ARCHITECTURE FLOW)

User requests are routed through Cloudflare DNS and secured via HTTPS. Traffic is forwarded to an Application Load Balancer, which distributes requests across multiple EC2 instances managed by an Auto Scaling Group. Each EC2 instance hosts the MERN stack application using Nginx as a reverse proxy. The backend securely connects to MongoDB Atlas for data persistence.

The AWS Deployment Architecture is fully comprised of the following layers:

## 1. User (Client Layer)
The user accesses the Travel Memory application through a web browser using the domain name https://www.cloudexpert.store

**Protocol: HTTPS Port:** 443 <br>

**Role:** 
- Initiates requests to view the application and interact with backend APIs.
- All user requests are encrypted using HTTPS to ensure secure communication.

---

## 2. Cloudflare DNS (Security Layer)
Cloudflare acts as the DNS provider and security gateway for the application. 

**Responsibilities:**
- Resolves domain names (graphtech.live and api.graphtech.live) provides SSL/TLS encryption. 
- Protects the application from DDoS attacks 
- Improves performance through caching and CDN Traffic Flow
- Incoming HTTPS requests from users are forwarded to the AWS Application Load Balancer.
- Frontend Target Group
- The frontend target group contains multiple EC2 instances hosting the React application. 

**Configuration:**
- **Protocol:** HTTP 
- **Port:** 80 
- **Health Check Path:** / 
- **The load balancer** continuously monitors the health of frontend instances and routes traffic only to healthy targets.

---

## 3. Frontend EC2 Instances (Presentation Layer)

Multiple EC2 instances are deployed to serve the frontend application. 

**Components:** React (build version) Nginx web server 

**Responsibilities:**
- Serves static frontend content 
- Handles client-side routing using React 
- Respond to user requests routed by the load balancer 
- This setup ensures high availability and scalability of the frontend layer


## Backend Target Group
The backend target group contains EC2 instances running the Node.js API. 

**Configuration:**
**Protocol:** 
- HTTP Port: 80 
- Health Check Path: /health 
- The health check endpoint ensures that only healthy backend instances receive traffic.

---

## 4. Backend EC2 Instances (Application Layer)
Multiple EC2 instances are deployed to run the backend services. 

**Components:** 
- Node.js with Express framework 
- PM2 Process Manager 
- Nginx Reverse Proxy 
- Internal Communication: Nginx listens on port 80 and forwards requests to Node.js running on port 3000 

**Responsibilities:** 
- Handles API requests 
- Authenticate users 
- Communicate with the database
- Process business logic 
- Backend instances are not publicly exposed, enhancing security, accessible via load balancer only. Port port 3000 is not exposed.

 ---

## 5. MongoDB Atlas (Data Layer)
MongoDB Atlas is used as a managed NoSQL database service.

**Responsibilities:**  
- Stores application data such as user information and travel memories 
- Provides secure and scalable data storage 
- Only backend EC2 instances are allowed to communicate with MongoDB Atlas, ensuring data security.

---

## End-to-End Traffic Flow Summary
The user sends an HTTPS request via a web browser. Cloudflare resolves the domain name and forwards the request securely. The Application Load Balancer receives the request. Based on routing rules: 
- Frontend requests are sent to frontend EC2 instances.
- API requests are sent to backend EC2 instances.
- Backend services interact with MongoDB Atlas to fetch or store data.
- Responses are sent back to the user through the same secure path.

---

## Security and Scalability 
✅ HTTPS encryption using Cloudflare SSL<br> 
✅ Load balancing across multiple EC2 instances<br> 
✅ Backend servers are not publicly accessible <br>
✅ Secure database connectivity

---

**AUTHOR:** Saima Usman<br>
**Github:** https://github.com/Saima-Devops/TravelMemoryApp
