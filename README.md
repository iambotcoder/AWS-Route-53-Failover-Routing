# 🚀 Amazon Route 53 Failover Routing Project

---

## 🍽️ Let's Consider a Café Example  

Imagine a café website hosted on **two EC2 instances** (`CafeInstance1` and `CafeInstance2`) in different **Availability Zones (AZs)**. Customers access the website to view the menu and place orders.  

- **Primary Instance (`CafeInstance1`)** serves the website under normal conditions.  
- **Secondary Instance (`CafeInstance2`)** acts as a backup in case the primary instance fails.  
- **Amazon Route 53 Failover Routing** ensures high availability by automatically redirecting traffic to `CafeInstance2` when `CafeInstance1` becomes unavailable.  
- **AWS SNS** sends an email alert when failover occurs, ensuring quick response to outages.  

This setup helps maintain seamless customer experience and reliability for the café's online presence. ☕🌐

---

## 📖 Overview
This project demonstrates how to set up **Amazon Route 53 Failover Routing** for a web application. The configuration ensures high availability by automatically redirecting traffic to a secondary EC2 instance when the primary instance becomes unavailable.

---

## 📑 Table of Contents
- 🔑 Prerequisites
- 🗺️ Architecture
- 📝 Task 1: Confirm the Café Websites
- 📝 Task 2: Configure Route 53 Health Check
- 📝 Task 3: Configure Route 53 Failover Records
  - Task 3.1: Create an A Record for the Primary Website
  - Task 3.2: Create an A Record for the Secondary Website
- 📝 Task 4: Verify DNS Resolution
- 📝 Task 5: Test Failover
- 🗑️ Cleaning Up Resources
- ✅ Conclusion
- 📚 Reference

---

## 🔑 Prerequisites
Before you start, ensure you have the following:
- **AWS Management Console** access.
- **Two EC2 Instances** (`CafeInstance1` and `CafeInstance2`) running in separate **Availability Zones (AZs)**.
- **Route 53 Hosted Zone** configured with a domain.
- **AWS Simple Notification Service (SNS)** set up for email notifications.

---

## 🗺️ Architecture
### 🔄 Workflow:
1. Two EC2 instances (`CafeInstance1` and `CafeInstance2`) host a café website in different AZs.
2. **Route 53 Health Check** monitors the primary instance (`CafeInstance1`).
3. If `CafeInstance1` fails, **Route 53 automatically switches traffic** to `CafeInstance2`.
4. **AWS SNS** sends an email alert when failover occurs.

---

## 📝 Task 1: Confirm the Café Websites
1. Open the **AWS Management Console**.
2. Navigate to **EC2** → **Instances**.
3. Verify that `CafeInstance1` (AZ1) and `CafeInstance2` (AZ2) are **running**.
4. Copy the **PrimaryWebSiteURL** and **SecondaryWebsiteURL**.
5. Open both URLs in a browser and verify the café application is running.
6. Place an order to test functionality.

---

## 📝 Task 2: Configure Route 53 Health Check
1. Open **Route 53** in the AWS Management Console.
2. Go to **Health Checks** → **Create Health Check**.
3. Configure the following:
   - **Name:** `Primary-Website-Health`
   - **Monitor:** `Endpoint`
   - **Specify Endpoint By:** `IP Address`
   - **IP Address:** `CafeInstance1` public IP.
   - **Path:** `/cafe`
   - **Request Interval:** `Fast (10 seconds)`
   - **Failure Threshold:** `2`
   - **Enable SNS Notification:** ✅ Yes
   - **Topic Name:** `Primary-Website-Health`
   - **Recipient Email:** `Your Email Address`
4. Click **Create Health Check**.
5. Check your email and confirm the SNS subscription.

---

## 📝 Task 3: Configure Route 53 Failover Records
### 📝 Task 3.1: Create an A Record for the Primary Website
1. Navigate to **Route 53** → **Hosted Zones**.
2. Select your domain (`XXXXXX_XXXXXXXXXX.vocareum.training`).
3. Click **Create Record** and enter:
   - **Record Name:** `www`
   - **Type:** `A`
   - **Value:** `CafeInstance1 IP`
   - **TTL:** `15`
   - **Routing Policy:** `Failover`
   - **Failover Record Type:** `Primary`
   - **Health Check ID:** `Primary-Website-Health`
   - **Record ID:** `FailoverPrimary`
4. Click **Create Record**.

### 📝 Task 3.2: Create an A Record for the Secondary Website
1. Click **Create Record** again.
2. Enter the following details:
   - **Record Name:** `www`
   - **Type:** `A`
   - **Value:** `CafeInstance2 IP`
   - **TTL:** `15`
   - **Routing Policy:** `Failover`
   - **Failover Record Type:** `Secondary`
   - **Health Check ID:** Leave empty
   - **Record ID:** `FailoverSecondary`
3. Click **Create Record**.

---

## 📝 Task 4: Verify DNS Resolution
1. Copy the **Record Name** (`www.XXXXXX_XXXXXXXXXX.vocareum.training`).
2. Open a browser and visit:
   ```
   http://www.XXXXXX_XXXXXXXXXX.vocareum.training/cafe
   ```
3. The **Primary Website** should load successfully.

---

## 📝 Task 5: Test Failover
1. Navigate to **EC2 → Instances**.
2. Select `CafeInstance1`.
3. Click **Instance State** → **Stop Instance**.
4. Go to **Route 53** → **Health Checks**.
5. Wait for `Primary-Website-Health` to change status to `Unhealthy`.
6. Refresh your website – it should now be served by `CafeInstance2`.
7. Restart `CafeInstance1` and verify **failback**.

---

## 🗑️ Cleaning Up Resources
To avoid unnecessary costs, delete the following resources:
1. **Route 53 Health Check**
   - Open **Route 53** → **Health Checks**.
   - Select `Primary-Website-Health` and click **Delete**.
2. **Route 53 Failover Records**
   - Open **Route 53** → **Hosted Zones**.
   - Delete the failover records (`FailoverPrimary` and `FailoverSecondary`).
3. **SNS Subscription**
   - Open **Amazon SNS**.
   - Delete the **Primary-Website-Health** topic.
4. **EC2 Instances** (if no longer needed)
   - Open **EC2 Dashboard**.
   - Select `CafeInstance1` and `CafeInstance2`.
   - Click **Terminate Instances**.

---

## ✅ Conclusion
In this project, we successfully configured **Amazon Route 53 Failover Routing** to ensure high availability for a web application. The setup automatically redirected traffic to a secondary instance when the primary instance failed. Additionally, AWS SNS sent an email alert upon failover detection.

---

## 📚 Reference
This project was inspired by AWS Restart Canvas Lab: **176-[JAWS]-Activity - Route 53 Failover Routing**.
