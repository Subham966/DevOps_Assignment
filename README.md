**Task Overview:**
1. Create a Docker image for a React application.
2. Deploy this Docker image to a Kubernetes cluster using EKS.





**aws configure**
![image](https://github.com/user-attachments/assets/f2443534-cd27-4fdd-bd79-8984e7552aee)

![image](https://github.com/user-attachments/assets/d8916d75-2775-4d2c-a870-e7add6d894d0)

**Clone the React Application
First, clone the provided React project from GitHub:**

git clone https://github.com/MaheshRautrao/React-Todo-list.git
![image](https://github.com/user-attachments/assets/af8d5379-fc9c-4f53-bbdf-caaa1551199e)

![image](https://github.com/user-attachments/assets/2da868e1-2093-4e6f-ad5f-9c20f6411f49)




curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client

curl -sL https://eksctl.io/install | bash
eksctl version

sudo apt-get update
sudo apt-get install docker.io
sudo systemctl start docker
sudo systemctl enable docker
docker --version
sudo usermod -aG docker ${USER}

**#Dockerfile:**
FROM node:14

WORKDIR /app

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]

![image](https://github.com/user-attachments/assets/4d55b6ca-0619-476e-9e12-0215b1ac0871)

docker build -t react-todo-app .

![image](https://github.com/user-attachments/assets/c19abc16-dd5a-4b34-8d9a-dd90b1b74690)
![image](https://github.com/user-attachments/assets/a9e37d2d-5bf9-49f9-b05e-5d8d47da0a50)

docker run -p 3000:3000 react-todo-app

![image](https://github.com/user-attachments/assets/1b1e5f65-652a-4343-a8cd-16cfd5fa3963)


**#Create a deployment.yaml file:**
apiVersion: apps/v1
kind: Deployment
metadata:
  name: react-todo-app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: react-todo-app
  template:
    metadata:
      labels:
        app: react-todo-app
    spec:
      containers:
      - name: react-todo-app
        image: react-todo-app:latest
        ports:
        - containerPort: 3000


**#Create a service.yaml file:**
apiVersion: v1
kind: Service
metadata:
  name: react-todo-app-service
spec:
  type: LoadBalancer
  selector:
    app: react-todo-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000


**#Deploy the Application to Kubernetes**
microk8s kubectl apply -f deployment.yaml
microk8s kubectl apply -f service.yaml


**#Verify the Deployment:**
Check if the pods and services are running properly:


microk8s.kubectl get pods
microk8s.kubectl get svc




