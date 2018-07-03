---
title: "Sample GCE App with Terraform Infrastructure as Code"
date: 2018-07-03
tags: [GCP, Kubernetes, Terraform]
excerpt: "Google Cloud Platform, Kubernetes, Terraform"
---

### Steps to create Infrastructure in GCP & deployment of a sample application with docker image in GCE 


You can find a Docker file and a sample GO app in the following [repo](https://github.com/TobiLayderer93/GCE_Terraform_sample)


#1. Download Terraform

[Terraform Page](https://www.terraform.io/downloads.html) 

Create PATH variable for terraform.exe or put .exe in C:/Terraform<project>

#2. Install Google Plug-In for Terraform: 

Place .exe under: 

```
%AppData%\Roaming\terraform.d\plugins\windows_amd64
```

#3. Install [Docker](https://www.docker.com/docker-windows)

#4. Install gcloud SDK

#5. Install kubectl in Google SDK:

Here is some more [info](https://cloud.google.com/sdk/docs/managing-components)

#6. Create Environment in C:\Terraform<project> 

* Provider.tf
* Components -> bucket.tf, container.tf etc. (see files in GD) 
* ServiceAccount for provider.tf -> Project Owner (Only in test env -> define role with special rights for SA )

```
$terraform plan 
```

It will show you information about what will be added / deleted / changed in GCP Project ENV. If everything is as expected the infrastructure could be build up: 

```
$terraform apply
```

Now everything should be ready for app deployment. 


#7. Create Docker Image 


* Dockerfile needed 
* Application files needed 

Place all files in defined folder: 

```
$docker build -t gcr.io/<gcp-projectname>/<imagename> .
```

You can check if the image is available with: 

```
$docker images
```

If the image is available everything is ready to push it to Container Engine in GCP. 


#8. Push Image to Container Engine: 

```
$gcloud docker -- push gcr.io/projectname/imagename
```


#9. Connect to Kubernetes UI: 

GCP -> Container Engine -> Connect -> cmd 

→ http://localhost:8001/ui


#10. Tell Kubernetes how to handle Docker Container 

Configure .yaml File and apply to Kubernetes 


```
$kubectl apply -f config.yaml
```


#11. Test sample application

```
kubectl get service hello 

```

Now you can open the provided IP Adress to test the sample App. 




