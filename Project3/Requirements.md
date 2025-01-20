# Recipe Sharing Application: Requirements and Architecture

## Requirements

### Functional Requirements
1. **Platform Admin (Owner)**:
   - Create, update, and delete recipes.
   - Manage recipe metadata (e.g., name, ingredients, tags, images).
   - Monitor application usage and performance.

2. **End Users (Consumers)**:
   - Access recipes via a user-friendly interface.
   - Search for recipes by name, ingredients, or tags.
   - View recipe details and associated images.
   - No permissions to create, modify, or delete recipes.

### Non-Functional Requirements
1. **Scalability**:
   - Handle a growing number of users and recipe requests efficiently.
2. **Security**:
   - Restrict access based on roles (Admin vs. End Users).
   - Protect sensitive data (e.g., recipe content).
3. **Performance**:
   - Ensure fast response times for recipe searches and image loading.
4. **Availability**:
   - Provide high uptime for the application.
5. **Cost Optimization**:
   - Use cost-effective AWS services to minimize expenses.

---

## Proposed Architecture

### AWS Services and Technologies
1. **Frontend**:
   - **Amazon S3**: Host static web assets like HTML, CSS, and JavaScript.
   - **Amazon CloudFront**: Distribute frontend globally with low latency.

2. **Backend**:
   - **Amazon EC2**: Host the backend application logic using a framework like Node.js or Python Flask/Django.
   - **Amazon API Gateway**: Expose APIs for frontend-backend communication securely.

3. **Database**:
   - **Amazon DynamoDB**: Store recipe data in a NoSQL database for fast access.
     - Use fine-grained access control to restrict write permissions to Admin only.

4. **Authentication & Authorization**:
   - **Amazon Cognito**:
     - Manage user authentication for both Admins and End Users.
     - Define roles to enforce permissions (Admin vs. Consumers).

5. **Monitoring & Logging**:
   - **Amazon CloudWatch**: Monitor application performance and set up alarms for anomalies.
   - **AWS CloudTrail**: Track API calls for auditing purposes.

6. **Security Enhancements**:
   - Use IAM policies to restrict access to AWS resources based on roles.
   - Enable HTTPS using SSL/TLS certificates via AWS Certificate Manager (ACM).
   - Set up AWS WAF (Web Application Firewall) to protect against web exploits.

---

## Architecture Diagram
[End User / Consumer] <---> [CloudFront] <---> [S3 (Frontend)]
|
v
[API Gateway] <---> [EC2 Backend]
|
v
[DynamoDB Database]
|
v
[Cognito Authentication]

### Explanation of the Architecture Flow
1. **Frontend**:
   - End Users access the application through a web browser or mobile app.
   - Static assets (HTML, CSS, JavaScript) are hosted on **Amazon S3** and distributed globally via **CloudFront** for low latency.

2. **Backend**:
   - User requests are routed through **API Gateway**, which acts as a secure entry point for backend services.
   - The backend logic (hosted on **EC2**) processes the requests and interacts with the database.

3. **Database**:
   - Recipe data is stored in **DynamoDB**, a fast and scalable NoSQL database.
   - Fine-grained access control ensures only Admins can write to the database, while End Users have read-only access.

4. **Authentication**:
   - User authentication and role-based access control are managed by **Amazon Cognito**.
   - Cognito ensures Admins can create, update, or delete recipes, while End Users can only view them.

5. **Monitoring & Security**:
   - Application performance is monitored using **CloudWatch**, while API calls are logged with **CloudTrail**.
   - Security is enhanced with IAM policies, HTTPS (via ACM), and AWS WAF to protect against web exploits.

---

## Role-Based Access Control (RBAC)

| Persona         | Permissions                                                                 |
|------------------|-----------------------------------------------------------------------------|
| Platform Admin  | Create, update, delete recipes; full access to all APIs and database tables |
| End Users       | Read-only access to recipes; no permissions to create/modify/delete data    |

---

## Cost Optimization Strategies
1. Use EC2 Auto Scaling to handle traffic spikes dynamically.
2. Enable S3 lifecycle policies to move unused assets to cheaper storage classes (e.g., S3 Glacier).
3. Leverage DynamoDB on-demand mode initially to avoid over-provisioning capacity.
4. Monitor costs using AWS Budgets and Cost Explorer.

---

## Benefits of the Solution
- **Dynamic Functionality**: Supports role-based actions for Admins and End Users.
- **Scalable Design**: Handles growing user base efficiently with AWS-managed services.
- **Secure Access Control**: Ensures data integrity with role-based permissions via Cognito and IAM policies.
- **High Performance & Availability**: CloudFront ensures low latency; DynamoDB provides fast querying.

This architecture aligns with the requirements while ensuring scalability, security, and cost-effectiveness.
