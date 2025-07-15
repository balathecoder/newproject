# AWS Lambda

## Introduction
AWS lambda is a serverless compute service that allows you to run compute service without provisioning or managing servers. It executes code in response to events
and automatically manages the underlying compute resources. With Lambda, you can run code for virtually any type of application or backend service, paying only for the compute time
you consume.

Features :
* Lambda is a compute service
* Fully serverless
* Pay as per usage
* 1 Million requests free per month
* Up to 3.2 million seconds of compute time per month is free
* Lambda free tier

Usage :
* File processing
* Stream processing - with AWS Kenesis
* Web applications - Combining with API gateway it can automatically scale up and scale down.
* IoT backends
* Mobile backends

Free tier limit
* 1 Million free request per month.
* 1000 is the concurrency limit.
* 3.2 million seconds of compute time per month.

Lambda natively supports several programming languages, including Node.js, Python, Java, Go, C#, Ruby and Powershell. Additionally, Lambda provides a runtime API 
that allows users to use any other bringing their own run time.

Layers :
Lambda layers allow you to manage and share common code and dependencies across multiple Lambda functions, reducing code duplication and simplifying deployment. Essentially, they 
act as a way to package reusable components like libraries, custom runtimes, or configuration files and make them accessible to your Lambda functions.

## Create Sample Lambda Function
<img width="611" height="349" alt="image" src="https://github.com/user-attachments/assets/677351ed-ee71-4003-b408-f817f9d62a1e" />
Provide lambda name, runtime environment, permissions(create a new role with basic lambda permissions otherwise if any other service access required then add that as well)
