Here’s your **formatted version** of the full text — neat, readable, and ready to copy or submit 👇

---

# **Experiment 1: Git & GitHub Operations**

```bash
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.com/manojmr23/ABC.git
git push -u origin main
```

> **If an error occurs**, rename the branch and push again:

```bash
git branch -m main
git push -u origin main
```

### **Create and Push a Feature Branch**

```bash
git checkout -b feature-branch
echo "Feature branch update" >> demo.txt
git add .
git commit -m "Added demo.txt for pull request test"
git push origin feature-branch
```

---

# **Experiment 2: Simple Docker Web App**

```bash
mkdir docker-demo
cd docker-demo
```

### **Create `index.html`**

```html
<h1>Hello from Dockerized NGINX!</h1>
```

### **Create `Dockerfile`**

```dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html/index.html
```

### **Build and Run**

```bash
docker build -t myweb:v1 .
docker run -d -p 9090:80 myweb:v1
```

---

# **Experiment 3: Flask App in Docker**

**Project Structure:**

```
flask-docker-app/
├── app.py
├── requirements.txt
└── Dockerfile
```

### **app.py**

```python
from flask import Flask, render_template_string

app = Flask(__name__)

@app.route('/')
def home():
    return render_template_string("<h1>Welcome to the Flask Docker App!</h1>")

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### **requirements.txt**

```
Flask==2.2.5
```

### **Dockerfile**

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

### **Build and Run**

```bash
docker build -t flask-docker-app .
docker run -p 5000:5000 flask-docker-app
```

---

# **Experiment 4: Jenkins Setup using Docker Compose**

```bash
mkdir jenkins-static-site
cd jenkins-static-site
```

### **docker-compose.yml**

```yaml
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: unless-stopped
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - ./jenkins-config:/usr/share/jenkins/ref
      - /var/run/docker.sock:/var/run/docker.sock

volumes:
  jenkins_home:
    driver: local
```

### **Run Jenkins**

```bash
docker compose up -d
```

### **Get Admin Password**

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

> **If any error occurs:**

```bash
docker compose down
docker compose up -d
```

---

# **Experiment 5: Python CI/CD with Docker & GitHub Actions**

```bash
mkdir python-ci-docker-lab
cd python-ci-docker-lab
```

### **Project Structure**

```
python-ci-docker-lab/
│
├── __init__.py
├── app.py
├── requirements.txt
├── Dockerfile
├── .gitignore
│
├── tests/
│   └── test_app.py
│
└── .github/
    └── workflows/
        └── ci-dockerhub.yml
```

### **app.py**

```python
def add(a, b):
    return a + b

if __name__ == "__main__":
    print("Hello from Python CI Lab!")
    print("2 + 3 =", add(2, 3))
```

### **tests/test_app.py**

```python
import sys
import os

sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))
from app import add

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
```

### **requirements.txt**

```
pytest==8.3.2
```

### **Dockerfile**

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### **.gitignore**

```
__pycache__/
.venv/
.pytest_cache/
.DS_Store
*.pyc
```

### **GitHub Workflow: `.github/workflows/ci-dockerhub.yml`**

```yaml
name: ci-dockerhub

on:
  push:
    branches: [ "main" ]
    tags: [ "*" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-test-push:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v4

    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.11'

    - name: Install deps
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Run tests
      run: pytest -q

    - name: Docker meta
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: manojmr232005/python-ci-lab
        tags: |
          type=raw,value=latest,enable={{is_default_branch}}
          type=sha,prefix=sha-,format=short
          type=ref,event=tag

    - name: Set up QEMU
      uses: docker/setup-qemu-action@v3

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3

    - name: Login to Docker Hub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKERHUB_USERNAME }}
        password: ${{ secrets.DOCKERHUB_TOKEN }}

    - name: Build and push
      uses: docker/build-push-action@v6
      with:
        context: .
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        platforms: linux/amd64
```

### **Local Testing**

```bash
python app.py
python -m pytest -q
docker build -t yourname/python-ci-lab:local .
docker run --rm yourname/python-ci-lab:local
```

### **Push to GitHub**

```bash
git init
git add .
git commit -m "init lab"
git branch -M main
git remote add origin https://github.com/<your-username>/python-ci-docker-lab.git
git push -u origin main
```

> **If CI fails:**

```bash
git commit --allow-empty -m "retrigger: fix Docker Hub login"
git push
```

---

# **Experiment 6: Manage Kubernetes Resources Using CLI**

## **A. Install Docker**

**For Windows:**

1. Download and install Docker Desktop.
2. Enable **WSL2 backend**.
3. Start Docker Desktop.

**Verify Installation:**

```bash
docker --version
```

---

## **B. Install kubectl (Kubernetes CLI)**

**Using Chocolatey:**

```bash
choco install kubernetes-cli
```

**Verify Installation:**

```bash
kubectl version --client
```

---

## **C. Install and Start Minikube**

**Using Chocolatey:**

```bash
choco install minikube
minikube start --driver=docker
```

**Verify Cluster:**

```bash
minikube status
kubectl get nodes
```

---

## **Step 1: Create a Simple Pod**

Create `nginx-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

**Apply the manifest:**

```bash
kubectl apply -f nginx-pod.yaml
kubectl get pods
kubectl describe pod nginx-pod
```

**Access the Pod:**

```bash
kubectl port-forward pod/nginx-pod 8080:80
```

Visit → [http://localhost:8080](http://localhost:8080)

**Delete Pod:**

```bash
kubectl delete pod nginx-pod
```

---

## **Step 2: Deployment and Scaling**

```bash
kubectl create deployment my-nginx --image=nginx
kubectl get deployments
kubectl get pods -l app=my-nginx

kubectl scale deployment my-nginx --replicas=3
kubectl get pods

kubectl set image deployment/my-nginx nginx=nginx:1.25
kubectl rollout status deployment/my-nginx
```

**Rollback if needed:**

```bash
kubectl rollout undo deployment/my-nginx
```

---

## **Step 3: Expose Deployment as a Service**

```bash
kubectl expose deployment my-nginx --type=NodePort --port=80
kubectl get svc
minikube service my-nginx --url
```

Open the displayed URL in your browser.

**Remove the service:**

```bash
kubectl delete svc my-nginx
```

---

## **Step 4: Cleanup**

```bash
kubectl delete deployment my-nginx
kubectl delete svc my-nginx
kubectl delete all --all -n default
minikube stop
minikube delete
```

---

## **🧾 Summary**

You learned how to:

1. Install Docker, kubectl, and Minikube.
2. Create and manage Pods.
3. Deploy and scale applications.
4. Expose Deployments as services.
5. Clean up resources safely.

---

Would you like me to format this as a **lab manual PDF** (with proper titles, code blocks, and numbering) for printing or submission?
