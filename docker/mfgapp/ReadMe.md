# ECR IMAGE DEPLOYMENT:
Make sure your AWS CLI credentials are configured with the region you are working on.

# 1 - get login token- this helps for security (Optional).
aws ecr get-login-password --region us-east-1

# 2 - login to ecr.
aws ecr get-login-password --region us-east-1 | docker login \
--username AWS \
--password-stdin \
{give-aws-account-number}.dkr.ecr.us-east-1.amazonaws.com 

# 3 - create ECR repo.
aws ecr create-repository \
--repository-name {give-repo-name} \
--region us-east-1 \
--image-scanning-configuration scanOnPush=true

# 4 - Build docker image from source code.
docker build -t {give-name-of-image} .,
docker images,

# 5 - NOTE: copy created repository ARN and use it for tagging.
docker tag {image-name} give-aws-account-number.dkr.ecr.us-east-1.amazonaws.com/{repo-name}:latest
docker push {give-aws-account-number}.dkr.ecr.us-east-1.amazonaws.com/{repo-name}:latest

# 6 - Making sure you successfully pushed the image.
aws ecr list-images --repository-name {repo-name} --region us-east-1

# 7 - Delete image
aws ecr batch-delete-image --repository-name {repo-name} --image-ids imageTag=latest --region us-east-1

# 8 - Delete Repository
aws ecr delete-repository --repository-name {repo-name} --force --region us-east-1

*************************
# Pushing Code to CodeCommit

# 1 Create CodeCommit Repo.
aws codecommit create-repository --repository-name {repo-name} --repository-description "CodeCommit Demo repository"

# 2 Clone CodeCommit Repo to your PC.
Make sure you have pip installed and your IAM credentials and Region configured.

pip install git-remote-codecommit |
git clone codecommit::us-east-1://{repo-name} [using HTTPS(GRC)] 

# 3 Push Code to CodeCommit.
You could modify the server.js file in order to reflect the difference after yourr pipeline runs.

git add . |
git status |
git commit -m "message" |
git push origin master 