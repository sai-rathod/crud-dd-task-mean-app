# CRUD Application - MEAN Stack

A full-stack CRUD application built with MongoDB, Express.js, Angular, and Node.js (MEAN stack), featuring automated CI/CD pipeline with Jenkins and containerized deployment using Docker.

## 🏗️ Architecture

- **Frontend**: Angular application served via Nginx (port 80)
- **Backend**: Node.js/Express.js REST API (port 8081, listening on 0.0.0.0)
- **Database**: MongoDB
- **CI/CD**: Jenkins pipeline with SonarQube analysis
- **Deployment**: Docker Compose orchestration

---

## 📋 Prerequisites

- Docker and Docker Compose installed
- Jenkins (for CI/CD pipeline)
- SonarQube (for code quality analysis)
- SSH access (for EC2 deployment)
- Docker Hub account

---

## 🚀 Local Deployment

### Step 1: Configure Frontend Service URL

Before deploying locally, update the API endpoint in the frontend:

**File**: `frontend/src/app/services/tutorial.service.ts`

Change the hardcoded EC2 IP to your local backend URL:
```typescript
// Change from EC2 IP to localhost
baseUrl = 'http://localhost:8081/api';
```

### Step 2: Build and Push Docker Images

Build and push both frontend and backend images to Docker Hub:

```bash
# Build and push backend
cd backend
docker build -t your-dockerhub-username/crud-dd-task-backend:latest .
docker push your-dockerhub-username/crud-dd-task-backend:latest

# Build and push frontend
cd ../frontend
docker build -t your-dockerhub-username/crud-dd-task-frontend:latest .
docker push your-dockerhub-username/crud-dd-task-frontend:latest
```

### Step 3: Update Docker Compose File

Update image names in `docker-compose.yml` to match your Docker Hub username.

### Step 4: Deploy Application

```bash
docker-compose up -d
```

Access the application at `http://localhost`

---

## ☁️ EC2 Deployment

### Step 1: Configure Frontend for EC2

Update the API endpoint in `frontend/src/app/services/tutorial.service.ts` with your EC2 instance IP:
```typescript
baseUrl = 'http://YOUR_EC2_IP:8081/api';
```

### Step 2: Configure Jenkins Pipeline

Update the following in your `Jenkinsfile`:
- Docker Hub credentials
- EC2 instance IP address
- SSH credentials ID
- SonarQube configuration

### Step 3: Run Jenkins Pipeline

The Jenkins pipeline will automatically:
1. Clone the repository
2. Run SonarQube analysis
3. Build Docker images
4. Push images to Docker Hub
5. Deploy to EC2 via SSH

---

## 📸 Screenshots

### Application UI
![application-ui](https://github.com/user-attachments/assets/236687ff-3af6-4f34-9576-b7db208de3d9)

---
### Jenkins Pipeline
![jenkins-pipeline](https://github.com/user-attachments/assets/35aaf72b-bbb8-4c32-8706-eb19fe1bc4da)

---

### SonarQube Results
![sonarqube-result](https://github.com/user-attachments/assets/8a0bbfaa-aa09-469f-97d3-a6350bd9665a)

---

### AWS Console
![aws-account](https://github.com/user-attachments/assets/d66f4f86-aa46-4ee7-983f-6c08b48c3b14)

---

## 🔧 Services Configuration

| Service  | Port | Container Name    |
|----------|------|-------------------|
| Frontend | 80   | crud-frontend     |
| Backend  | 8081 | crud-backend      |
| MongoDB  | 27017| crud-mongodb      |

---

## 🐳 Docker Compose Overview

The application uses a bridge network (`crud-network`) to enable communication between services. MongoDB data persists in a named volume (`mongodb_data`).

---

## 🛠️ Troubleshooting

**Issue**: Application not accessible after deployment
- Verify all containers are running: `docker ps`
- Check container logs: `docker logs <container-name>`
- Ensure security groups allow traffic on ports 80, 8081, and 27017

**Issue**: Frontend cannot connect to backend
- Verify the API URL in `tutorial.service.ts` matches your deployment environment
- Check backend container is running and healthy

---

## 👤 Author

**Sai Rathod**
- Docker Hub: [sairathod](https://hub.docker.com/u/sairathod)
- GitHub: [sai-rathod](https://github.com/sai-rathod)
