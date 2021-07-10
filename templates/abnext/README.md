## ArtbutlerNext Rancher Stack 

This Rancher stack runs the application using the code from repositories:
* https://bitbucket.org/artbutlersoftware/artbutlernext-laravel (backend)
* https://bitbucket.org/artbutlersoftware/artbutlernext-vue (frontend)

Code from repositories above is building Docker Images in Bitbucket Pipelines processes. The images are uploaded to the Amazon ECR (Docker Container Registry).
We have 2 docker images:
####  abnext/backend
Used to run services:
* Laravel (backend)
* Laravel Horizon

Image is based on PHP 8 and Apache2. We use the same images for two services to keep the same code base for Laravel Horizon jobs. 
It allows us to always keep compatible jobs to current backend codebase. 

#### abnext/frontend

Used to run service Vue, that serves static content using Nginx. 

### Environments

This Rancher Stack is prepared to run Staging and Production environments, which are based on the same Docker Images.
Domains, credentials to database and other services can be set using env variables. Which allow us configure separated environments with the same code base as production. 

To define env variables we used Rancher Questions mechanism. The questions are defined in file ```rancher-compose.yml```
Later the Rancher Dashboard allow us to fill all fields (Questions). The most of them are regular environment variables passed to the container using

### Required additional services
The application has to be connected with:
- PostgreSQL (external dedicated database)
- Redis (Rancher container)  
- ElasticSearch (Rancher container)
- AWS S3
