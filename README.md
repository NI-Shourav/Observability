# 📊 End-to-End Observability Stack & Student Application

A production-ready observability architecture deployed on AWS EC2 using Docker Compose. This stack integrates **Flask**, **MySQL**, **PromQL**, and **Redis** with **cAdvisor**, **Node Exporter**, **Prometheus**, and **Grafana** to provide comprehensive container and host monitoring.

---

## 🚀 Technology Stack

<p align="left">
  <img src="https://img.shields.io/badge/AWS%20EC2-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white" alt="AWS EC2" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker" />
  <img src="https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white" alt="Prometheus" />
  <img src="https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white" alt="Grafana" />
  <img src="https://img.shields.io/badge/Flask-000000?style=for-the-badge&logo=flask&logoColor=white" alt="Flask" />
  <img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white" alt="MySQL" />
  <img src="https://img.shields.io/badge/PromQL-E6522C?style=for-the-badge&logo=prometheus&logoColor=white" alt="PromQL" />
  <img src="https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white" alt="Redis" />
</p>

---

## 🛠️ Step-by-Step Deployment

### 📋 Prerequisites
* An AWS Account
* An EC2 Instance running **Ubuntu Server 22.04 LTS** (or similar Debian-based distribution)
* SSH access to the instance

---

### Step 1: EC2 Instance Security Group Configuration
Simply add the following ports to your EC2 instance's inbound security group rules: `5000`, `3000`, `8080`, and `9090`.

---

### Step 2: System Update & Docker Installation
Connect to your EC2 instance via SSH and execute the following commands to update packages and set up Docker:

```bash
# Update and upgrade system packages
sudo apt update && sudo apt upgrade -y

# Install Docker Engine
sudo apt install docker.io -y

# Install Docker Compose v2 Plugin
sudo apt install docker-compose-v2 -y
```

---

### Step 3: Grant Docker Privileges
To run Docker commands without prefixing them with `sudo`, add your current user to the `docker` group:

```bash
sudo usermod -aG docker $USER && newgrp docker
```

---

### Step 4: Verify Docker Installation
Confirm that Docker is active and running successfully:

```bash
docker ps
```

---

### Step 5: Clone & Start the Observability Stack
Clone this repository directly onto the EC2 instance, navigate into the project directory, and spin up the services:

```bash
# Clone the repository
git clone https://github.com/NI-Shourav/Observability.git

# Navigate to the repository
cd Observability

# Launch all services in detached background mode
docker compose up -d
```

---

## 🔍 Verifying the Services

Once the containers are running, you can access and configure the services using your **EC2 Public IP Address**:

### 1. Students Application (`Port 5000`)
* **URL:** `http://<EC2_PUBLIC_IP>:5000`
* This is the front-facing Flask web application. It will automatically communicate with the MySQL database.

---

### 2. cAdvisor (`Port 8080`)
* **URL:** `http://<EC2_PUBLIC_IP>:8080`
* cAdvisor analyzes resource usage and performance characteristics of running containers.
* > [!NOTE]
  > Statistics and graphs will take a short moment (typically 1–2 minutes) to start showing up. You can view all running Docker containers directly in the interface.

---

### 3. Prometheus (`Port 9090`)
* **URL:** `http://<EC2_PUBLIC_IP>:9090`
* **Checking Target Health:**
  1. Navigate to **Status** ➔ **Targets**.
  2. Verify that both the `prometheus` and `cadvisor` jobs are showing **UP** status.
* **Running a PromQL Query:**
  1. Go back to the **Graph** tab (or query screen).
  2. Enter the following expression in the PromQL search box:
     ```promql
     rate(container_cpu_usage_seconds_total{name="students-app"}[5m])
     ```
  3. Click **Execute**.
  4. Switch to the **Graph** tab and set the time duration slider/range to **1m** (1 minute) to see real-time container CPU activity.

---

### 4. Grafana (`Port 3000`)
* **URL:** `http://<EC2_PUBLIC_IP>:3000`
* **Initial Login:**
  * **Username:** `admin`
  * **Password:** `admin`
* > [!IMPORTANT]
  > On your first login, Grafana will require you to change the default credentials. Please set a secure personal password.

#### 🔗 Connect Prometheus Data Source
1. In the left-hand navigation sidebar, click on **Connections** (or the gear icon) ➔ **Data Sources**.
2. Click **Add data source** and select **Prometheus**.
3. In the **Connection URL** field, enter your Prometheus server endpoint:
   ```text
   http://<EC2_PUBLIC_IP>:9090
   ```
4. Scroll to the bottom of the page and click **Save & test**. You should see a green success banner confirming the data source is working.

#### 📊 Import the Node Exporter Full Dashboard
1. Go to Google and search for **"Node Exporter Full"** or navigate directly to the [Grafana Dashboards Catalog](https://grafana.com/grafana/dashboards/1860-node-exporter-full/).
2. Copy the Dashboard ID: `1860`.
3. In your Grafana UI, go to **Dashboards** (icon) ➔ **New** ➔ **Import**.
4. Paste the ID `1860` in the **Import via grafana.com** text box and click **Load**.
5. Select the **Prometheus** data source you configured in the previous step, and click **Import**.
6. Enjoy your comprehensive system metrics and real-time visualization dashboard!
