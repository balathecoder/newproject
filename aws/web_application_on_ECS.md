# Serverless application running on AWS ECS

## 1. Ready with application

## 2. Push application ECR
Create Dockerfile for your web application
```
FROM python:3.13-slim

COPY app ./app

COPY requirements.txt .

RUN pip install --upgrade pip
RUN pip install -r requirements.txt 

WORKDIR /app

EXPOSE 8001

CMD ["uvicorn", "main:app", "--reload", "--port", "8001"]
```

Follow the push commands provided in AWS ECR,
```
$ aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 381491823509.dkr.ecr.us-east-1.amazonaws.com
Login Succeeded
```

Build the docker image,
```
$ docker build -t testsrv/simple_webserver .
```

Tag the docker image,
```
$ docker tag testsrv/simple_webserver:latest 058264309373.dkr.ecr.us-east-1.amazonaws.com/testsrv/websrv:v1
```

Push the docker image to ECR,
```
$ docker push 058264309373.dkr.ecr.us-east-1.amazonaws.com/testsrv/websrv:v1
```

## 3. Create the ECS Cluster
### 3.1 ECS
### 3.2 Create ECS Cluster

