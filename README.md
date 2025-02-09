# 🌐 Amazon Route 53 Failover Routing

## 🎯 Objective
This project focuses on setting up Amazon Route 53 Failover Routing for a web application. The failover routing is configured for two Amazon EC2 instances hosting a website in different Availability Zones (AZs). If the primary instance fails, Route 53 will automatically route traffic to the secondary instance.

## 🏗️ Final Architecture Overview
- 🖥️ Two EC2 instances (PrimaryInstance and SecondaryInstance) deployed in separate AZs.
- ✅ Route 53 health check monitors the primary instance.
- 🔄 If the primary instance fails, Route 53 automatically switches traffic to the secondary instance.
- 📩 AWS Simple Notification Service (SNS) sends an alert email when failover occurs.

## 📝 Implementation Steps

### 🏁 Step 1: Verify Website Availability
1. 🔹 Open the AWS Management Console.
2. 🔹 Navigate to EC2 and confirm that two instances (PrimaryInstance in AZ1 and SecondaryInstance in AZ2) are running.
3. 🔹 Copy the `PrimaryWebSiteURL` and `SecondaryWebsiteURL`.
4. 🔹 Open both URLs in a browser to verify the application is running on both instances.
5. 🔹 Test the functionality by interacting with the website.

### 🔍 Step 2: Configure Route 53 Health Check
1. 🌍 Open Route 53 in the AWS Management Console.
2. ⚙️ Go to **Health Checks** → **Create Health Check**.
3. 🔧 Configure the following:
   - **Name**: `Primary-Website-Health`
   - **Monitor**: Endpoint
   - **Specify Endpoint By**: IP Address
   - **IP Address**: Enter `PrimaryInstance` public IP.
   - **Path**: `/health`
   - **Request Interval**: Fast (10 seconds)
   - **Failure Threshold**: 2
4. 📢 Enable SNS Notification:
   - **Create Alarm**: Yes
   - **Topic Name**: `Primary-Website-Health`
   - **Recipient Email**: Your email address.
5. ✅ Click **Create Health Check**.
6. 📧 Check your email and confirm the SNS subscription.

### ⚡ Step 3: Configure Route 53 Failover Records

#### 🏗️ Step 3.1: Create an A Record for the Primary Website
1. 🌍 Go to **Route 53** → **Hosted Zones**.
2. 🏷️ Choose your domain (`your-domain.com`).
3. ➕ Click **Create Record** and enter:
   - **Record Name**: `www`
   - **Type**: `A`
   - **Value**: Enter `PrimaryInstance` IP.
   - **TTL**: 15
   - **Routing Policy**: Failover
   - **Failover Record Type**: Primary
   - **Health Check ID**: `Primary-Website-Health`
   - **Record ID**: `FailoverPrimary`
4. ✅ Click **Create Record**.

#### 🏗️ Step 3.2: Create an A Record for the Secondary Website
1. ➕ Click **Create Record** again.
2. 🔧 Enter:
   - **Record Name**: `www`
   - **Type**: `A`
   - **Value**: Enter `SecondaryInstance` IP.
   - **TTL**: 15
   - **Routing Policy**: Failover
   - **Failover Record Type**: Secondary
   - **Health Check ID**: Leave empty
   - **Record ID**: `FailoverSecondary`
3. ✅ Click **Create Record**.

### 🔎 Step 4: Verify DNS Resolution
1. 🔗 Copy the Record Name (`www.your-domain.com`).
2. 🌍 Open a browser and go to:
   ```bash
   http://www.your-domain.com/health
   ```
3. 🏆 The Primary Website should load.

### 🔄 Step 5: Test Failover
1. 🛑 Go to **EC2** → **Instances**.
2. 🔽 Select `PrimaryInstance`.
3. ⚠️ Click **Instance State** → **Stop Instance**.
4. 🔍 Go to **Route 53** → **Health Checks**.
5. ⏳ Wait for `Primary-Website-Health` to change status to **Unhealthy**.
6. 🔄 Refresh the browser tab with your website. The secondary instance (`SecondaryInstance`) should now serve the webpage.
7. 🚀 Restart `PrimaryInstance` and verify failback.

## 🎉 Conclusion
You have successfully implemented failover routing using Amazon Route 53. 
- 🔀 Route 53 automatically redirected traffic to the secondary instance when the primary instance became unavailable.
- 📩 AWS SNS sent an email alert when the primary instance failed.
