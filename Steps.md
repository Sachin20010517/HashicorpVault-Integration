# Setting Up HashiCorp Vault on AWS EC2 with Ubuntu

This guide explains how to set up HashiCorp Vault on an AWS EC2 instance running Ubuntu. It includes steps to create the EC2 instance and install necessary dependencies like Docker, AWS CLI, and jq.

---

## **Step 1: Create an EC2 Instance**

### **1.1 Log in to AWS**
1. Go to the [AWS Management Console](https://aws.amazon.com/console/).
2. Navigate to the **EC2 Dashboard**.

### **1.2 Launch a New Instance**
1. Click on **Launch Instances**.
2. Configure the instance:
   - **Name**: `HashiCorpVaultServer`
   - **AMI**: Ubuntu Server 20.04 LTS (or a supported version).
   - **Instance Type**: Choose an appropriate type (e.g., `t2.micro` for testing or `t3.medium` for production).
   - **Key Pair**: Select an existing key pair or create a new one.
   - **Network Settings**:
     - Ensure the security group allows inbound traffic for SSH (port 22) and Vault (default port 8200).
   - **Storage**: Allocate at least 10GB for storage.

3. Click **Launch Instance**.
4. Wait for the instance to launch.

### **1.3 Connect to the Instance**
1. SSH into the instance using the key pair:
   ```bash
   ssh -i /path/to/key.pem ubuntu@<EC2-Instance-Public-IP>
   ```

---

## **Step 2: Update the System**
1. Update system packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

---

## **Step 3: Install Dependencies**

### **3.1 Install Docker**
1. Install Docker:
   ```bash
   sudo apt install -y docker.io
   ```
2. Enable and start the Docker service:
   ```bash
   sudo systemctl enable docker
   sudo systemctl start docker
   ```
3. Add the `ubuntu` user to the `docker` group:
   ```bash
   sudo -i
   usermod -aG docker ubuntu
   su - ubuntu
   groups
   ```

### **3.2 Install AWS CLI**
1. Download and install AWS CLI:
   ```bash
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   sudo apt install unzip 
   unzip awscliv2.zip
   sudo ./aws/install
   ```
2. Verify the installation:
   ```bash
   aws --version
   ```
3. Configure AWS:
   ```bash
   aws configure 
   ```

### **3.3 Install jq**
1. Install jq:
   ```bash
   sudo apt install -y jq
   ```
2. Verify the installation:
   ```bash
   jq --version
   ```
### **3.3 Install Certbot & Run following Certbot Command ###
Install certbot:
   ```bash
   sudo apt  install certbot
   ```
## **Step 4: Obtain SSL Certificates with Certbot**

### **4.1 Run Certbot Command**
1. Run the following command to request a wildcard SSL certificate:
   ```bash
   sudo certbot certonly --manual --preferred-challenges=dns --key-type rsa \
       --email sachinayeshmantha@gmail.com --server https://acme-v02.api.letsencrypt.org/directory \
       --agree-tos -d *.sachinayeshmantha.live
   ```
2. Certbot will prompt you to add a DNS TXT record. Copy the value provided by Certbot.

### **4.2 Add DNS TXT Record in Cloudflare**
1. Log in to your Cloudflare account and navigate to the DNS settings for your domain.
2. Add a new **TXT** record:
   - **Name**: `_acme-challenge`
   - >Note: Make sure to only copy `_acme-challenge` this part
   - **Value**: Paste the value provided by Certbot.
3. Certbot will confirm successful installation, after then make sure to copy this:
   - **Certificate Path**: `/etc/letsencrypt/live/sachinayeshmantha.live/fullchain.pem`
   - **Key Path**: `/etc/letsencrypt/live/sachinayeshmantha.live/privkey.pem`
   >Note: These location will be very crucial, because it is going to be save on Harshicope vault  configuartion

