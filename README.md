**Task Overview:**
1. Create a Docker image for a React application.
2. Deploy this Docker image to a Kubernetes cluster using EKS.

**aws configure**

![image](https://github.com/user-attachments/assets/f2443534-cd27-4fdd-bd79-8984e7552aee)

![image](https://github.com/user-attachments/assets/d8916d75-2775-4d2c-a870-e7add6d894d0)

**Clone the React Application
First, clone the provided React project from GitHub:**

```yaml
git clone https://github.com/MaheshRautrao/React-Todo-list.git
```

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

```yaml
docker build -t react-todo-app .
```

![image](https://github.com/user-attachments/assets/c19abc16-dd5a-4b34-8d9a-dd90b1b74690)
![image](https://github.com/user-attachments/assets/a9e37d2d-5bf9-49f9-b05e-5d8d47da0a50)

docker run -p 3000:3000 react-todo-app

![image](https://github.com/user-attachments/assets/1b1e5f65-652a-4343-a8cd-16cfd5fa3963)

![image](https://github.com/user-attachments/assets/ec748f63-0103-4f0c-838c-8c38a31433fa)

![image](https://github.com/user-attachments/assets/b94b9a5b-c987-412c-91ee-ec7b31631f63)

![image](https://github.com/user-attachments/assets/f295adb3-509a-4300-a9af-a9a2cbeca9e7)

**#Docker Image size was high which is about 1.29GB, So I have then Optimized Dockerfile for reducing the size of the image from 1.29GB size to 111MB:**

```yaml
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json .
RUN npm install
COPY .  .
RUN npm run build

FROM nginx:1.17.1
COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=build /app/build /usr/share/nginx/html
```

![image](https://github.com/user-attachments/assets/24452932-5e98-45e1-a8e8-8e5cbf367c6c)


```yaml
docker build -t react-todo-app .
```
![image](https://github.com/user-attachments/assets/455353db-61e6-41b2-ad1c-6af85b0b651b)

![image](https://github.com/user-attachments/assets/e17894b3-539f-43bc-9eed-35b4b2aa8b7f)


**EKS Cluster Creation:**

```yaml
eksctl create cluster --version 1.28 --name react-app-cluster --region eu-central-1 --nodes 2 --node-type t3.medium --nodes-min 2 --nodes-max 2 --managed
```

![image](https://github.com/user-attachments/assets/8e285e8f-8ece-45ff-b03e-bfc80d17a294)

![image](https://github.com/user-attachments/assets/98e22753-9ad1-45d4-be9a-23aa925bd242)

![image](https://github.com/user-attachments/assets/c1cb25f0-9bb5-4585-93ed-7b93532142b0)


![image](https://github.com/user-attachments/assets/13d4b259-b9bc-4674-a9f8-b75e9998f5a1)

![image](https://github.com/user-attachments/assets/b99a939c-7634-4872-9b5a-4c754b99e738)

![image](https://github.com/user-attachments/assets/e7a62577-0389-4a9a-9c53-127617c862d2)

![image](https://github.com/user-attachments/assets/697efb9b-87e2-4214-bbdc-d825f000acd4)

**Build and Push the Docker Image to ECR:**



![image](https://github.com/user-attachments/assets/cfb0bc97-32df-4d65-a00e-fa2510d5c59b)

![image](https://github.com/user-attachments/assets/75ad791f-dd4c-407c-8c89-141da3bba69e)


![image](https://github.com/user-attachments/assets/189b65bd-bf69-4059-9e6c-590116aa25c8)

![image](https://github.com/user-attachments/assets/f5c4de1e-1970-4f43-9f62-88d05519c449)


![image](https://github.com/user-attachments/assets/2985a19b-f395-4d5a-a2c7-b9feb8073b08)



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
        image: 9078006440.dkr.ecr.eu-central-1.amazonaws.com/react-todo:latest
        ports:
        - containerPort: 80
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
      targetPort: 80
```


**#Deploy the Application to Kubernetes**

```yaml
microk8s kubectl apply -f deployment.yaml
microk8s kubectl apply -f service.yaml
```

![image](https://github.com/user-attachments/assets/60d262ab-9f70-4af6-be81-cd8f440382db)



**#Verify the Deployment:**
Check if the pods and services are running properly:

```yaml
microk8s.kubectl get pods
microk8s.kubectl get svc
```
![image](https://github.com/user-attachments/assets/27947386-c186-407a-9882-e3494f805583)

```yaml
http://aeba97c523e5647afbbb84bc6151a178-741773144.eu-central-1.elb.amazonaws.com/
```

![image](https://github.com/user-attachments/assets/9a652d60-5441-40e0-854b-5a39f3b8c537)


