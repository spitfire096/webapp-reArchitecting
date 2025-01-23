# Re-Architecting the Web Application Stack on AWS (PaaS & SaaS)  

This project implements a flexible, scalable, and automated web application stack using AWS services. The architecture leverages Platform-as-a-Service (PaaS) and Software-as-a-Service (SaaS) to simplify deployment and reduce operational overhead. Below is the step-by-step guide to setting up the infrastructure and deploying the application.

---

## **Flow of Execution**  

### **1. Login to AWS Account**  
- Sign in to your AWS Management Console with appropriate permissions to create resources.

### **2. Create a Key Pair for Beanstalk Instances**  
- Navigate to the **EC2 Dashboard** → **Key Pairs**.  
- Create a new key pair and download the `.pem` file for SSH access to instances managed by Elastic Beanstalk.  

### **3. Create Security Groups for Backend Services**  
- Define a Security Group (SG) for backend services (Amazon RDS, Amazon ElastiCache, Amazon MQ).  
- Allow internal traffic and specific ports (e.g., MySQL: 3306, ElastiCache: 11211, MQ: 5672) for communication.

### **4. Create Backend Services**  
- **Amazon RDS**:  
  - Choose a database engine (e.g., MySQL).  
  - Configure the DB instance with appropriate storage and networking.  
- **Amazon ElastiCache**:  
  - Deploy a Memcached or Redis cluster for caching.  
- **Amazon MQ**:  
  - Set up an ActiveMQ broker for message queuing.  

### **5. Create an Elastic Beanstalk Environment**  
- Use the **Elastic Beanstalk Console** to create a new application.  
- Configure it with the desired platform (e.g., Tomcat for Java apps).  

### **6. Update Backend Security Group Rules**  
- Add a rule to the backend SG to allow traffic from the Beanstalk SG.  
- Allow internal communication within the backend SG.

### **7. Launch an EC2 Instance for MySQL Access**  
- Create an EC2 instance for accessing the RDS database.  
- SSH into the instance and initialize the database schema on the RDS instance.  

### **8. Update Beanstalk Health Check**  
- Change the health check endpoint of the Beanstalk environment to `/login` to match your application logic.

### **9. Add HTTPS Listener to Load Balancer**  
- In the Elastic Beanstalk console, configure the environment’s load balancer to include a **443 HTTPS listener**.  
- Use **AWS Certificate Manager (ACM)** to create an SSL certificate and attach it to the load balancer.

### **10. Build Artifact from Source Code**  
- Build the application artifact (e.g., a WAR or JAR file) locally or via a CI/CD pipeline.  
- Ensure the backend service configurations (e.g., database URL, credentials) are included.

### **11. Deploy Artifact to Elastic Beanstalk**  
- Use the Beanstalk console or AWS CLI to deploy the built artifact to the environment.

### **12. Create Content Delivery Network with CloudFront**  
- Set up an **Amazon CloudFront Distribution** with your application as the origin.  
- Use an SSL certificate for HTTPS connections.  

### **13. Update Load Balancer Endpoint in GoDaddy**  
- Log in to your GoDaddy account.  
- Update the domain’s DNS settings to point to the CloudFront distribution or load balancer endpoint.

### **14. Validate and Test**  
- Access your application using the URL (with HTTPS).  
- Verify the functionality, performance, and scaling of your stack.  

---

## **Architecture Overview**  
The web application stack is built with the following components:  
1. **Elastic Beanstalk**: Manages the application stack with auto-scaling and load balancing.  
2. **Amazon RDS**: Provides a managed relational database.  
3. **Amazon ElastiCache**: Speeds up application performance with caching.  
4. **Amazon MQ**: Ensures reliable messaging with a managed ActiveMQ broker.  
5. **Amazon CloudFront**: Delivers content globally with low latency and SSL encryption.  
6. **AWS Certificate Manager**: Secures the application with HTTPS.  

---

## **Benefits of this Approach**  
- **Flexible Infrastructure**: Easily scales with demand.  
- **Automation**: Simplified provisioning and deployment using Elastic Beanstalk.  
- **Cost Efficiency**: Operates on a pay-as-you-go model with no upfront investment.  
- **Performance & Reliability**: High availability and global reach through managed AWS services.  
- **Business Agility**: Enables quick updates, feature additions, and scaling.  

---

## **Prerequisites**  
- AWS Account with necessary permissions.  
- Access to application source code and build tools.  
- A registered domain (e.g., GoDaddy).  

---

## **Useful Commands**  

### **AWS CLI Commands**  
- Deploy application to Elastic Beanstalk:  
  ```bash
  aws elasticbeanstalk create-application-version --application-name <AppName> --version-label <Version> --source-bundle S3Bucket=<Bucket>,S3Key=<Key>
  aws elasticbeanstalk update-environment --application-name <AppName> --environment-name <EnvName> --version-label <Version>
