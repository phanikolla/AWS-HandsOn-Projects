# Recipe Sharing Application Deployment Guide

## 1. DNS Configuration & Certificate Issuance
### a. **Create Route 53 Hosted Zone**  
1. **Create Hosted Zone**:  
   - In AWS Route 53, create a hosted zone for `cloudprojects.site
     ```
     aws route53 create-hosted-zone --name cloudprojects.site --caller-reference $(date +%s)
     ```  
2. **Update Hostinger Nameservers**:  
   - Copy the 4 NS records from Route 53 (e.g., `ns-123.awsdns-45.net`).  
   - In Hostinger, navigate to **Domains → DNS / Nameservers → Custom Nameservers** and replace existing nameservers with Route 53 values 

### b. **Issue SSL Certificate via AWS ACM**  
1. **Request Certificate**:  
   - In ACM, request a public certificate for `*.cloudprojects.site` (wildcard)
2. **Validate via DNS**:  
   - Add the CNAME record provided by ACM to Hostinger’s DNS Zone
     ```
     | Host Name                   | Points To                           |
     |-----------------------------|-------------------------------------|
     | _xyz123.example.cloudprojects.site | _abc456.acm-validations.aws.       |
     ```  
   - Wait for validation (typically 5-10 minutes)[3][16].  

---

## 2. Solution Deployment
### a. **Frontend (React App)**  
1. **Deploy to S3**:  
   - Create an S3 bucket (`react-app-cloudprojects`) and enable static website hosting 
   - Upload React build files:  
     ```
     aws s3 sync ./build s3://react-app-cloudprojects --delete
     ```  
2. **CloudFront Setup**:  
   - Create a CloudFront distribution with S3 origin.  
   - Attach ACM certificate and configure alternate domain name:  
     ```
     Domain: example.cloudprojects.site
     SSL Certificate: *.cloudprojects.site (from ACM)
     ```  
   - Update Route 53 with an **A record** pointing to the CloudFront distribution.

### b. **Backend (FastAPI on EC2)**  
1. **CloudFormation Template**:  
Resources: RecipeBackendEC2: Type: AWS::EC2::Instance Properties: ImageId: ami-0abcdef1234567890 # Amazon Linux 2023 AMI InstanceType: t3.medium KeyName: my-key-pair SecurityGroupIds: - sg-0abcdef1234567890 UserData: Fn::Base64: | #!/bin/bash yum install -y python3.9 git pip3 install fastapi uvicorn git clone https://github.com/your-repo/backend.git cd backend && uvicorn main:app –host 0.0.0.0 –port 8000

2. **Reverse Proxy (Nginx)**:  
server { listen 80; server_name api.cloudprojects.site; location / { proxy_pass http://localhost:8000; } }

3. **DynamoDB Table**:  
- Create a table `Recipes` with `recipe_id` as the primary key.  

---

## 3. Testing  
### **API Endpoints**  
- **Create Recipe (Admin)**:  
curl -X POST https://api.cloudprojects.site/recipes 
-H “Authorization: Bearer <ADMIN_TOKEN>” 
-d ‘{“name”: “Pizza”, “ingredients”: “flour”, “cheese”}’

- **Get Recipe (User)**:  
curl https://api.cloudprojects.site/recipes/Pizza


### **Validation Tests**  
1. **HTTPS Validity**:  
 - Verify with [SSL Labs](https://ssllabs.com).
2. **Role-Based Access**:  
 - Confirm users get `403 Forbidden` on write operations.  

---

## 4. Best Practices  
### **Security**  
- **IAM Roles**: Assign EC2 an IAM role with least-privilege access to DynamoDB
- **Encryption**: Enable SSE-S3 for S3 and DynamoDB



- **WAF**: Block SQL injection/XSS attacks via AWS WAF

### **Monitoring**  
- **CloudWatch Alarms**: Track EC2 CPU > 80% and DynamoDB throttled requests
- **CloudTrail**: Log API activity for auditing 

### **Cost Optimization**  
- **EC2 Auto Scaling**: Scale instances based on CPU usage
- **S3 Lifecycle Policies**: Move old images to Glacier after 90 days

---

 **Authentication**: Amazon Cognito for admin/user RBAC

 

By following these steps, this application will be secure, scalable, and ready for 100k+ users.  
 
