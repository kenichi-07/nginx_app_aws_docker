# Simple Nginx Web App with AWS App Runner

This project demonstrates how to deploy a simple static web app using **Nginx** as the web server. The application is containerized using **Docker** and deployed to **AWS App Runner** for scalable and easy hosting.

## Table of Contents

- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Local Setup](#local-setup)
- [Building and Running the Docker Container Locally](#building-and-running-the-docker-container-locally)
- [Push Docker Image to AWS ECR](#push-docker-image-to-aws-ecr)
- [Deploy on AWS App Runner](#deploy-on-aws-app-runner)
- [Testing](#testing)
- [Cleanup](#cleanup)

---

## Project Structure

The project consists of the following files:

```
/nginx_app_aws_docker/
│
├── Dockerfile           # Docker configuration file for containerization
├── nginx.conf           # Nginx server configuration
├── index.html           # Simple static HTML web page
└── README.md            # This documentation file
```

- **`Dockerfile`**: Defines how the Docker image is built, including configuring Nginx to serve the static web page.
- **`nginx.conf`**: A basic configuration file for the Nginx server.
- **`index.html`**: A simple static HTML page to be served.
- **`README.md`**: Project documentation.

---

## Prerequisites

Before starting, make sure you have the following installed:

1. **Docker**: You need Docker installed on your local machine to build and test the container.
   - [Install Docker](https://docs.docker.com/get-docker/)
2. **AWS CLI**: To interact with AWS services from your local machine.
   - [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
3. **An AWS Account**: Ensure you have an AWS account with permissions to create ECR repositories and deploy services via App Runner.

---

## Local Setup

1. **Clone the repository:**

   If you have this project in version control, clone the repository to your local machine. If not, create the following files as described in the project structure.

   ```bash
   git clone <repository_url>
   cd nginx_app_aws_docker
   ```

2. **Directory Structure:**

   Make sure your project directory has the following files:

   ```
   /nginx_app_aws_docker/
   ├── Dockerfile
   ├── nginx.conf
   ├── index.html
   ```

---

## Building and Running the Docker Container Locally

1. **Build the Docker image:**

   Open a terminal and navigate to the project directory, then run the following command to build the Docker image:

   ```bash
   docker build -t nginx_app_aws_docker .
   ```

2. **Run the Docker container locally:**

   To test the application locally, run the Docker container:

   ```bash
   docker run -p 8080:80 nginx_app_aws_docker
   ```

3. **Test Locally:**

   Open a browser and navigate to `http://localhost:8080` to see the web app running. You should see the "Welcome to My Simple Web App!" message.

---

## Push Docker Image to AWS ECR

1. **Create an ECR repository:**

   Log in to the AWS Management Console and create a new **Amazon ECR** (Elastic Container Registry) repository named `nginx_app_aws_docker`.

2. **Log in to AWS ECR:**

   Use the AWS CLI to authenticate Docker with ECR. Replace `<your-region>` and `<your-account-id>` accordingly.

   ```bash
   aws ecr get-login-password --region <your-region> | docker login --username AWS --password-stdin <your-account-id>.dkr.ecr.<your-region>.amazonaws.com
   ```

3. **Tag the Docker image:**

   Tag your image to match the ECR repository URL:

   ```bash
   docker tag nginx_app_aws_docker:latest <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/nginx_app_aws_docker:latest
   ```

4. **Push the Docker image to ECR:**

   Push the image to your newly created ECR repository:

   ```bash
   docker push <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/nginx_app_aws_docker:latest
   ```

---

## Deploy on AWS App Runner

1. **Open AWS App Runner:**

   Go to the AWS Management Console and open the **App Runner** service.

2. **Create a new service:**

   - Click **"Create Service"**.
   - Choose **"Container registry"** as the source.
   - Select **"Amazon ECR"** and choose your repository (`nginx_app_aws_docker`).
   - Choose the latest image tag.
   - Click **Next**.

3. **Service Configuration:**

   - Set a **Service name** (e.g., `nginx-app-runner`).
   - Under **Port**, ensure that port `80` is set.
   - Use the default settings for auto-scaling or adjust based on your needs.

4. **Set Permissions:**

   App Runner requires an **IAM role** to pull the image from ECR. Create a new IAM role if you don’t have one that allows App Runner access to ECR.

5. **Deploy the Service:**

   - Review the settings and click **Create and deploy**.
   - The service may take a few minutes to deploy. Once it’s deployed, App Runner will provide a URL for your running service.

---

## Testing

1. **Access the Web App:**

   Once the deployment is complete, App Runner provides a public URL. Navigate to the provided URL, and you should see your simple web app ("Welcome to My Simple Web App!").

---

## Cleanup

To avoid incurring ongoing charges, make sure to clean up resources once you're done:

1. **Delete the AWS App Runner service:**
   - Go to the AWS App Runner Console and delete the service.

2. **Delete the ECR repository:**
   - Go to the ECR Console and delete the repository to remove the Docker images.

3. **Delete other AWS resources (IAM roles, etc.)** if created for this project.

---

## Notes

- **Logs and Monitoring**: App Runner automatically sets up logging to CloudWatch. You can view logs in the AWS Management Console under **CloudWatch Logs**.
- **Scaling**: AWS App Runner automatically handles scaling based on incoming traffic.

---

## Conclusion

This project demonstrates the complete process of deploying a static web app using Docker and Nginx on AWS App Runner. With this approach, you can easily scale and maintain your applications with minimal configuration. Feel free to modify the `index.html` or use a dynamic framework like Flask or Express for more advanced use cases.

---

