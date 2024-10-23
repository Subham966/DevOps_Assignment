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


**#Dockerfile:**

```yaml
FROM node:14
WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

![image](https://github.com/user-attachments/assets/4d55b6ca-0619-476e-9e12-0215b1ac0871)

docker build -t react-todo-app .

![image](https://github.com/user-attachments/assets/c19abc16-dd5a-4b34-8d9a-dd90b1b74690)
![image](https://github.com/user-attachments/assets/a9e37d2d-5bf9-49f9-b05e-5d8d47da0a50)

docker run -p 3000:3000 react-todo-app

![image](https://github.com/user-attachments/assets/1b1e5f65-652a-4343-a8cd-16cfd5fa3963)

![image](https://github.com/user-attachments/assets/ec748f63-0103-4f0c-838c-8c38a31433fa)

![image](https://github.com/user-attachments/assets/b94b9a5b-c987-412c-91ee-ec7b31631f63)

![image](https://github.com/user-attachments/assets/f295adb3-509a-4300-a9af-a9a2cbeca9e7)

**EKS Cluster Creation:**

```yaml
eksctl create cluster --version 1.28 --name react-app-cluster --region eu-central-1 --nodes 2 --node-type t3.medium --nodes-min 2 --nodes-max 2 --managed
```



**#Create a deployment.yaml file:**

```yaml
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
```

**#Create a service.yaml file:**

```yaml
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
```

**#Deploy the Application to Kubernetes**

```yaml
microk8s kubectl apply -f deployment.yaml
microk8s kubectl apply -f service.yaml
```

**#Verify the Deployment:**
Check if the pods and services are running properly:


microk8s.kubectl get pods
microk8s.kubectl get svc




