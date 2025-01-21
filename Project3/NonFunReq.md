# Non-Functional Requirements Document

**Date:** Tuesday, January 21, 2025, 10 PM IST  
**Purpose:** This document outlines the non-functional requirements for the application to ensure scalability, global availability, and cost-effectiveness.

---

## Non-Functional Requirements

### **1. Scalability**
- **Handle Spiky Access Patterns:**  
  - Implement **AWS Auto Scaling** to dynamically adjust compute resources (e.g., EC2 instances, DynamoDB tables) based on traffic demand.
  - Use **On-Demand Capacity Mode** for DynamoDB to accommodate unpredictable spikes in traffic without manual intervention.
  - Leverage **Predictive Scaling** for EC2 to proactively scale resources during anticipated high-demand periods .
  - Include Elastic Load Balancing (ELB) to distribute traffic across instances effectively.

### **2. Global Availability**
- **Serve Users in USA and UK:**  
  - Deploy resources in AWS Regions close to target users: **US East (N. Virginia)** and **EU (London)** for low latency and high availability.
  - Use **Amazon CloudFront** as a Content Delivery Network (CDN) to cache and deliver content globally with reduced latency.
  - Implement **AWS Global Accelerator** for optimized routing of user requests to the nearest available region.

### **3. Cost Effectiveness**
- **Optimize Resource Costs:**  
  - Use **Spot Instances** for fault-tolerant workloads, reducing EC2 costs by up to 90%.
  - Adopt **Savings Plans** or Reserved Instances for predictable workloads to save up to 72% compared to On-Demand pricing.
  - Utilize AWS Cost Management tools like **AWS Compute Optimizer** and **Cost Explorer** to monitor and optimize usage .
- **Data Transfer Optimization:**  
  - Minimize data transfer costs by using AWS Direct Connect or CloudFront Regional Edge Caches.
- **Right-Sizing Resources:**  
  - Regularly review and adjust instance types and sizes using AWS Compute Optimizer for efficient resource utilization.

---

## Implementation Considerations

1. **Monitoring and Alerts:**
   - Use **Amazon CloudWatch** for monitoring application performance and setting alarms to trigger scaling actions as needed.

2. **Testing Scalability:**
   - Conduct load testing to simulate traffic spikes and validate auto-scaling configurations.

3. **Compliance and Data Residency:**
   - Ensure compliance with data residency requirements by storing user data within their respective regions (USA or UK).

---

## Cost Implications
1. Auto Scaling services incur costs based on the resources provisioned during scaling events.
2. CloudFront usage is billed based on data transfer rates and request counts.
3. Savings Plans or Reserved Instances require upfront or partial commitments but significantly reduce long-term costs.

By implementing these strategies, the application will remain performant, globally accessible, and cost-efficient under varying workloads.
