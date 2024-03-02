In this demo you will be creating a Rekognition model that will be used to detect cats and dogs images. You will then build a simple Flask application using Docker that will be used to test the Rekognition model. This application will be uploaded to an ECR repository and deployed to an ECS cluster.

The demo is dividen in 5 stages:

Stage 1 - Create and train the Rekognition model
Stage 2 - Create the ECR repository and build the Docker image
Stage 3 - Create the ECS cluster, ECS task definition and ECS service
Stage 4 - Test the application
