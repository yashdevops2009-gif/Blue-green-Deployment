# Blue-Green Deployment Project

## Prerequisites
- Docker Desktop
- Minikube
- kubectl
- Helm
- Node.js
- Git

## Project Setup

### 1. Clone the Repository
```bash
git clone <your-repository-url>
cd blue-green-project
```

### 2. Local Development

#### Backend Setup
1. Navigate to backend directory
2. Install dependencies
```bash
cd backend
npm install
```
3. Create `.env` file with:
```
PORT=5000
MONGO_URI=your-mongodb-connection-string
```
4. Start backend server
```bash
npm start
```

#### Frontend Setup
1. Setup Blue Frontend
```bash
cd frontend-blue
npm install
```
2. Create `.env` file:
```
PORT=3100
```
3. Start blue frontend
```bash
npm start
```

<img width="940" height="479" alt="image" src="https://github.com/user-attachments/assets/442cd047-f06d-408d-a547-18d5f6a1a9aa" />


3. Repeat similar steps for Green Frontend (with PORT=3200)
   
   <img width="940" height="478" alt="image" src="https://github.com/user-attachments/assets/6ae318c2-75f3-4aa5-8c93-fa6b01fc1b3a" />

### 3. Dockerization

#### Build Docker Images
```bash
# Build Backend Image
docker build -t your-username/backend:v1 ./backend

# Build Blue Frontend Image
docker build -t your-username/frontend-blue:v1 ./frontend-blue

# Build Green Frontend Image
docker build -t your-username/frontend-green:v1 ./frontend-green
```
<img width="940" height="149" alt="image" src="https://github.com/user-attachments/assets/b9518e59-f770-452d-b4eb-0556e3bb0216" />
<img width="940" height="257" alt="image" src="https://github.com/user-attachments/assets/3f5f8184-7e52-49c5-a536-656bdb64e1b5" />



### 4. Kubernetes Deployment

#### Minikube Setup
1. Start Minikube
```bash
minikube start
```
<img width="940" height="175" alt="image" src="https://github.com/user-attachments/assets/423fd590-aaa8-411c-b635-a38c9c6a2351" />
<img width="940" height="189" alt="image" src="https://github.com/user-attachments/assets/48305b41-c885-4f83-9d37-dee0f89183ef" />



2. Enable Required Addons
```bash
minikube addons enable metrics-server
minikube addons enable ingress
```

### 5. Create Kubernetes Manifest Files

#### Required Manifest Files
Create following files in `k8s/` directory:
- `backend-deployment.yaml`
- `frontend-blue-deployment.yaml`
- `frontend-green-deployment.yaml`
- `frontend-service.yaml`
- `ingress.yaml`
- <img width="369" height="127" alt="image" src="https://github.com/user-attachments/assets/bd212356-176f-4ffd-8d68-ee13497659e1" />


#### Service File Key Concepts
Your `frontend-service.yaml` should:
- Use selector to route traffic
- Define version (blue/green)
- Map ports correctly

### 6. Deploy to Minikube
```bash
# Apply all manifests
kubectl apply -f k8s/

# Verify deployments
kubectl get deployments
kubectl get services
kubectl get pods
```

### 7. Blue-Green Switching

#### Switch Traffic Methods

1. Basic Patch Command
```bash
# Switch to Green
kubectl patch service frontend-service -p '{"spec":{"selector":{"version":"green"}}}'

# Switch back to Blue
kubectl patch service frontend-service -p '{"spec":{"selector":{"version":"blue"}}}'
```

2. Detailed Patch Command
```bash
kubectl patch service frontend-service --type='merge' -p '{
  "spec":{
    "selector":{
      "app":"frontend",
      "version":"green"
    }
  }
}'
```

### 8. Verification
- Check service endpoints
- Verify traffic routing
- Monitor application logs

### Troubleshooting
- `kubectl get pods` - Check pod status
- <img width="815" height="132" alt="image" src="https://github.com/user-attachments/assets/449b97e4-e2f0-4cd7-b238-720d92e87598" />

- `kubectl logs <pod-name>` - View logs
- 
- `kubectl describe service frontend-service` - Service details
- <img width="813" height="458" alt="image" src="https://github.com/user-attachments/assets/f938ac94-3900-410a-ae20-b97e34bec5eb" />

- 

### Cleanup
```bash
# Remove deployments
kubectl delete -f k8s/
<img width="1063" height="174" alt="image" src="https://github.com/user-attachments/assets/cc661e51-9ef8-44a8-ae3a-bab116575032" />

# Stop Minikube
minikube stop
```

## Blue-Green Deployment Flow Chart

```mermaid
graph TD
    A[Blue Environment Running] -->|Deploy Green| B[Green Environment Prepared]
    B -->|Validate Green| C{Green Ready?}
    C -->|Yes| D[Update Service Selector]
    C -->|No| B
    D -->|Redirect Traffic| E[Green Now Active]
    E -->|Rollback Option| A
```

### Flow Explanation
1. Blue environment is initial production
2. Green environment deployed alongside
3. Validate green environment 
4. Update service selector
5. Redirect traffic to green
6. Blue remains as rollback option

## Best Practices
- Implement health checks
- Use resource limits
- Configure monitoring
- Validate before switching
- Maintain rollback strategy


## License
This project is licensed under the MIT License
