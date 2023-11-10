# ECR & DOCKERHUB IMAGE DEPLOYMENT:
Make sure your AWS CLI credentials are configured with the region you are working on.

## 1 - get login token- this helps for security (Optional).
- $ aws ecr get-login-password --region us-east-1

## 2 - login to ecr.
- $ aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin {give-aws-account-number}.dkr.ecr.us-east-1.amazonaws.com 

## 3 - create ECR repo.
- $ aws ecr create-repository --repository-name {give-repo-name} --region us-east-1 --image-scanning-configuration scanOnPush=true

## 4 - Build docker image from source code.
- $ docker build -t {give-name-of-image} .
- $ docker images

## 5 - NOTE: copy created repository ARN and use it for tagging.
- $ docker tag {image-name} {give-aws-account-number}.dkr.ecr.us-east-1.amazonaws.com/{repo-name}:latest
- $ docker push {give-aws-account-number}.dkr.ecr.us-east-1.amazonaws.com/{repo-name}:latest

## 6 - Making sure you successfully pushed the image.
- $ aws ecr list-images --repository-name {repo-name} --region us-east-1

## 7 - Delete image
- $ aws ecr batch-delete-image --repository-name {repo-name} --image-ids imageTag=latest --region us-east-1

## 8 - Delete Repository
- $ aws ecr delete-repository --repository-name {repo-name} --force --region us-east-1

*************************
# DOCKERHUB
## Step 1 - login to Docker Hub
```
docker login
```
## create Dockerfile for sample Nginx container (built on Ubuntu)
```
cat > Dockerfile <<EOF
FROM ubuntu:latest
RUN DEBIAN_FRONTEND=noninteractive apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get -yq install net-tools nginx
EXPOSE 80
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
EOF
```
## build your image
```
sudo docker build -t <your-dockerhub-name>/<your-docker-repo-name> .
```
## test your container locally
```
sudo docker run <your-dockerhub-name>/<your-docker-repo-name> .
```
## push it to your Docker repository:
```
sudo docker push <your-dockerhub-name>/<your-docker-repo-name> 
```
*************************
# Pushing Code to CodeCommit

## 1 Create CodeCommit Repo.
- $ aws codecommit create-repository --repository-name {repo-name} --repository-description "CodeCommit Demo repository"

## 2 Clone CodeCommit Repo to your PC for normal AWS Admin Access.
Creating Access keys for CodeCommit:
- Open Amazon IAM => Users
- On the IAM user go to credentials =>
- On => https Git: Generate credentials => download and copy to txt file. 
- CodeCommit login will pop when you try to push to CodeCommit
- Copy CodeCommit https URL and clone on your CLI.

#FOR NORMAL IAM USERS 
- $ git clone (https URL)

## 2-B Clone CodeCommit for SSO Users.
- Make sure you have pip installed for SSO users 
- Configure your IAM credentials and Region.

#FOR SSO USERS [using HTTPS(GRC)] 
- $ pip install git-remote-codecommit 
- $ git clone codecommit::us-east-1://{repo-name} 

## 3 Push Code to CodeCommit.
You could modify the server.js file in order to reflect the difference after your pipeline runs.

- $ git add . 
- $ git status 
- $ git commit -m "message" 
- $ git push origin master 
