# Rekognition-ECR Demo

In this part of the DEMO, you will be doing the following:-

- Creating a Rekognition model that will be used to detect cats and dogs in images.
- Training the model with a set of images that contain cats and dogs.

# STAGE 1 - Create and train the Rekognition model

Navigate to the Rekognition console: https://us-east-1.console.aws.amazon.com/rekognition/home?region=us-east-1#/

In the left-hand menu, click on "Use Custom Labels".

Click on the "Get started" button.

A pop-up window will appear if you **have not created** a Rekognition Custom Labels project before. This pop-up windows will ask you to create an S3 bucket to store all the data related to the model that you will be training. Save the name of the bucket that will be created, it will have the following format: `custom-labels-console-us-east-1-<random string>`. You will need it in the cleanup stage. Click on the "Create S3 bucket" button to create the S3 bucket.

In the left-hand menu, click on "Projects" and create a project with the name "skynet-cats-and-dogs".
Click on the "Create dataset" button and select the following options:
 - Configuration options: Start with a single dataset
 - Training dataset details: Upload images from your computer
 - Click Create Dataset

In the following screen, you will be able to upload the images that will be used to train the model. These images are contained in the following files:-

- [Cats-part1](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Cat-Dataset-part1.zip)
- [Cats-part2](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Cat-Dataset-part2.zip)
- [Cats-part3](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Cat-Dataset-part3.zip)
- [Cats-part4](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Cat-Dataset-part4.zip)
- [Cats-part5](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Cat-Dataset-part5.zip)
- [Cats-part6](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Cat-Dataset-part6.zip)
- [Dogs-part1](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Dog-Dataset-part1.zip)
- [Dogs-part2](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Dog-Dataset-part2.zip)
- [Dogs-part3](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Dog-Dataset-part3.zip)
- [Dogs-part4](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Dog-Dataset-part4.zip)
- [Dogs-part5](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Dog-Dataset-part5.zip)
- [Dogs-part6](https://learn-cantrill-labs.s3.amazonaws.com/aws-pet-rekognition-ecr/Dog-Dataset-part6.zip)

Unzip the files. You should see a total of 12 foldeds.  
Currently, the AWS console allows you to upload 30 images at a time, so you will need to do it in multiple attempts. 


- Click on the top-right "Actions" button and then click on "Add images to training dataset" to start uploading the images. 
- Click Choose files
- Go inside the folder for the first part of the cat images
- Select all the images
- Click open
- Then Click `Upload Images`

Wait until you see `You successfully saved the changes to your training dataset.`. Then repeat the process for the remaining 5 folders of Cat images (*we will be doing the dog images in a separate step*)

Once you have uploaded all `6` of the cat image folders, click `Start Labeling` then click `Add Labels`, and select `Add Labels`.  
Add a label `cat` click `Add label` 
and add another `dog`, click `Add label` then click `Save`  

Select `Unlabeled` on the `Labels` list on the left, and go through each page of images :

- select all the images
- Click `Assign Image-Level Labels`
- Type `cat` and select the `cat` label and click `Assign`

Move to the next page and Continue the above process for all pages of cat images.  
Once you've finished you can click `Save Changes`  
Once saved, you should have `0` unlabeled images.  

Now it's time to repeat the process for the dog images within the dataset.  

- Click on the top-right "Actions" button and then click on "Add images to training dataset" to start uploading the images. 
- Click Choose files
- Go inside the folder for the first part of the dog images
- Select all the images
- Click open
- Then Click `Upload Images`

Wait until you see `You successfully saved the changes to your training dataset.`. Then repeat the process for the remaining 5 folders of Dog images 

Once finished, select `Unlabeled` from the labels menu on the bottom left. You should see all the dog images.  
Click `Start Labelling`  and go through each page of images

- select all the images
- Click `Assign Image-Level Labels`
- Type `dog` and select the `dog` label and click `Assign`

Once you have done all pages, scroll to the top and click `Save Changes`.  

STAGE 2 - Create the ECR repository and build the Docker image
Navigate to the ECR console: https://us-east-1.console.aws.amazon.com/ecr/home?region=us-east-1

Click the "Get started" button on the right-hand side.

Make sure to select "Private" and enter "skynet-repo" as the name of the repository.

Click the "Create repository" button.

We need the URI of the repository that we just created. It will have the following format:

<AWS Account ID>.dkr.ecr.us-east-1.amazonaws.com/skynet-repo

The <AWS Account ID> is the ID of your AWS account. You can find it in the upper-right corner of the AWS console.

Navigate to the EC2 console: https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=instanceState

Click Instances in the left menu.

Locate and select the AWS-EC2-Docker instance.

Right-click and select Connect.

Select Session Manager and click Connect.

In the terminal window, enter the following commands:

sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user
sudo su - ec2-user
unzip app.zip
Next we are going to build and push the Docker image to the ECR repository. Enter the following commands replacing the <AWS Account ID> with the ID of your AWS account you copied in the previous step (you can find it in the upper-right corner of the AWS console):

Build the Docker image: docker build -t skynet .
Tag the Docker image: docker tag skynet:latest <AWS Account ID>.dkr.ecr.us-east-1.amazonaws.com/skynet-repo:latest
Login to the ECR repository: aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS Account ID>.dkr.ecr.us-east-1.amazonaws.com
Push the Docker image to the ECR repository: docker push <AWS Account ID>.dkr.ecr.us-east-1.amazonaws.com/skynet-repo:latest
Once the docker push command is completed, navigate to the ECR console: https://us-east-1.console.aws.amazon.com/ecr/home?region=us-east-1

Click on the repository that you created in the previous step.

You should see an image in the ECR repository with the "latest" tag under the Image tag column. Click on it to see the details and copy the URI of the image for the next stage. You should have something similar to this:

<AWS Account ID>.dkr.ecr.us-east-1.amazonaws.com/skynet-repo:latest


STAGE 3 - Create the ECS cluster, ECS task definition and ECS service
STAGE 3A - Create the ECS cluster
Move to the ECS console: https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#

Select the new ECS experience in the left-hand menu to see the new ECS console.

In the left-hand menu, click on “Clusters” and then click on the “Create cluster” button.

Enter SkynetCluster as the name of the cluster.

In the networking section select the VPC with the name A4L-VPC and the only subnet that is available.

Click “Create” button.

STAGE 3B - Create the ECS task definition
Before you can continue, you will need to have the stage 1 completed.

In the left-hand menu, click on “Task definitions”.

Click “Create new task definition” button.

Enter “SkynetTaskDefinition” as the name of the task definition.

In the container details section enter the following details:

Container name: skynet
Image URI: The one you got in the previous stage
In the environment variables section add the following ones:

Key: BUCKET_NAME

Value: The output "S3BucketName" from the Cloudformation stack you deployed

Key: MODEL_ARN

Value: The one you got in the first stage

Click “Next” button.

In the Task size section change the CPU and Memory values to the following ones:

CPU: .5 vCPU
Memory: 1 GB
Use the output ECSRoleName from the Cloudformation stack you deployed for both the task role and the task execution role sections.

In the Monitoring and logging section uncheck “Use log collection”.

Click “Next” button and then click “Create” button.

STAGE 3C - Create the ECS service
In the left-hand menu, click on “Task definitions”.

Select the “SkynetTaskDefinition” task definition, click “Deploy” button and then click “Create service”.

In the existing cluster section select “SkynetCluster”.

In the computer options section select Launch type and make sure the “FARGATE” launch type is selected.

In the deployment configuration section enter “SkynetService” as the service name.

In the networking section enter the following details:

VPC: A4L-AWS
Subnets: The only subnet that is available
Security group: The output "SecurityGroup" from the Cloudformation stack you deployed
Public IP: Turned on
Click “Create” button.

STAGE 4 - Test the application
After 3-5 minutes, you should see the service “SkynetService” with the status “Active”.

Click the “Tasks” tab and then click the task available to see the details. Get the Public IP of the task and paste in a new tab. You should get access to a simple web page where you can upload files of cats and dog to validate the model we trained in a previous stage. Bear in mind the following:

You need to upload a file with a cat or a dog but not a picture that contains both. If you upload a picture with both, the model will not be able to predict the correct class. The result of the prediction will be displayed in the web page with a single result (cat or dog).
The maximum size of the file is 15MB.
The file must be in JPG or PNG format.
The maximum for both width and height is 4096 pixels.











Make sure you see `0` unlabelled images.  

Click on the top-right "Train model" button. In the next screen, click on the "Train model" button and then click again on the "Train model" button that appears in the pop-up window.

To check the progress of the training, take a look at the "Model status" column. It will change the status to TRAINING_COMPLETED once the training is complete. The training process can take approximately 40-60 minutes to complete, so you can start the stages 2 and 3A while the model is being trained.
