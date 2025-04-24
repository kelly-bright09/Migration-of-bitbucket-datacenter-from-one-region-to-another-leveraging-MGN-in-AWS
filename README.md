# Migration-of-bitbucket-datacenter-from-one-region-to-another-leveraging-MGN-in-AWS
# Bitbucket Data Center Migration Using AWS MGN

## 1. Create VPC in Source Region (us-east-1 for example)
  - Go to **VPC Dashboard**
  - Click "Create VPC"
  - Select "VPC only"
  - Name: `bitbucket-vpc`
  - IPv4 CIDR: `10.0.0.0/16`
  - Click "Create VPC"

## 2. Launch EC2 Instance
  - Go to **EC2 Dashboard**
  - Click "Launch Instance"
  - Name: `bitbucket-server`
  - AMI: Amazon Linux 2023
  - Instance type: t3.medium
  - Select your VPC under "Network"
  - Under "Advanced details" paste this in User Data:
```bash
#!/bin/bash
yum install docker -y
service docker start
docker run -d --name bitbucket -p 7990:7990 atlassian/bitbucket-server
```
Click "Launch"

3. Switch to Target region (eg: us-west-2) Before MGN Setup!
   - Click the region dropdown (top right)
   - Select Oregon (us-west-2)
   - Wait for console to reload
     
4. Set Up MGN (Now in Oregon)
 - Search for "Application Migration Service"
 - Click "Add servers"
 - It will automatically discover your N. Virginia instance
 - Click "Continue"
 - Under target settings:
    - Region: Oregon (us-west-2)
    - Instance type: Same as source
 - Click "Add servers"

4. Test Migration (Oregon)
- In MGN console, select your server
- Click "Launch test instance"
- Wait 10 minutes
- Copy the new Oregon IP
- Visit http://[OREGON-IP]:7990 to verify

5. Complete Migration
- Back in MGN console
- Select your server
- Click "Launch cutover instance"
- Wait 15 minutes
- Your Bitbucket is now running in Oregon!


Clean Up (N. Virginia)
- Go to EC2 Dashboard
- Find your original Virginia instance
- Select it → Instance state → Terminate

Congratulations!!!🎊 Your migration is successfull!
