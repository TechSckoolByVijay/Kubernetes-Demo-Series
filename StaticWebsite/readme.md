# Deploying a Static Webpage on Nginx Container to AKS

![alt](https://raw.githubusercontent.com/TechSckoolByVijay/Kubernetes-Demo-Series/main/StaticWebsite/includes/architecture.jpg)

### Objective
- Showcase the process of containerizing the static webpage and deploying it to AKS for scalable and reliable hosting.

## Tools and Technologies
- Nginx: Web server and reverse proxy for serving the static webpage.
- Docker: Containerization platform for packaging the static webpage into a Docker container.
- Kubernetes: Container orchestration platform for managing containerized applications.
- Azure Kubernetes Service (AKS): Managed Kubernetes service by Microsoft Azure.

## Prerequisites
- Kubernetes Cluster
- Kubectl
- AZ CLI
- VS Code + Kubernetes Extension
- Azure Subscription


## Steps to Deploy
- **Step 1: Build Docker Image**

Create a Dockerfile for the Nginx container with the static webpage content.
Use Docker CLI to build the Docker image locally.

- **Step 2: Push Docker Image**

Tag the built Docker image with an Azure Container Registry (ACR) repository.
Push the Docker image to the ACR to make it available to AKS.

- **Step 3: Deploy to AKS**

Create a Kubernetes Deployment manifest to specify the Nginx container and its settings.
Deploy the Kubernetes Deployment to AKS using kubectl.

- **Step 4: Expose the Service**

Expose the Nginx Deployment as a Kubernetes Service to make it accessible externally.
Use an AKS Load Balancer or an Ingress Controller for external access.

## Demonstrations
- Show the process of building the Docker image with the static webpage.
- Demonstrate how to deploy the Docker image to AKS and verify its successful deployment.
- Access the static webpage through the external endpoint provided by AKS.

## Benefits
- Scalability: AKS automatically scales the Nginx containers based on demand.
- High Availability: AKS ensures that the application is available even if nodes fail.
- Containerization: Docker allows for consistent deployment across different environments.

## Conclusion
- Deploying a static webpage on Nginx to AKS offers a scalable and reliable hosting solution.
- Containerization simplifies deployment and management of the application.
- AKS provides a managed Kubernetes environment for seamless operations.



# Lecture 1 : Deploying Azure Container Registry & Building Docker Image

Welcome! In this session, we will explore how to use Azure Container Registry (ACR) to create a Docker image of an HTML file and run it on Nginx.

## What is Azure Container Registry (ACR)?
Azure Container Registry is a managed Docker registry service offered by Azure. It allows users to store, manage, and deploy Docker container images for their applications securely. ACR integrates seamlessly with other Azure services, making it an ideal choice for container image management in cloud-based environments.

## Setting Up Azure Container Registry
Before we dive into creating a Docker image, let's set up our Azure Container Registry. If you don't have an Azure subscription, you can create a free account on the Azure portal. Once you have an account, follow the documentation provided in the link to create your own Azure Container Registry. This will serve as a centralized repository for our Docker images.

```
az acr create -n MyRegistry -g MyResourceGroup --sku Standard
```

## Installing Azure CLI
To interact with Azure Container Registry and perform various tasks, we need to install the Azure CLI. Azure CLI is a command-line tool that allows us to manage Azure resources from our local machine. Follow the instructions in the link provided to install Azure CLI on your system.

## Creating a Dockerfile
A Dockerfile is a text file that contains instructions for Docker to build an image. It describes the base image, required dependencies, file copies, and commands needed to set up the container.

To create a Docker image, we need to define a Dockerfile. Create a file named "Dockerfile" (without any file extension) in the same directory as your HTML file. Open the Dockerfile in a text editor and add the necessary instructions. In our case, we will use the Nginx base image and copy our HTML file into the appropriate location within the container.

```
# Use the official Nginx base image
FROM nginx:latest

# Copy the index.html file to the container's web root directory
COPY index.html /usr/share/nginx/html/index.html

# Expose the default Nginx port
EXPOSE 80

# Start Nginx when the container launches
CMD ["nginx", "-g", "daemon off;"]

```

## Building and Pushing the Docker Image
With the Dockerfile ready, we can now build our Docker image and push it to Azure Container Registry. We will use the az acr build command, which enables us to build and push the image directly to ACR. 
Open a terminal or command prompt and follow the steps outlined in the lecture to log in to your Azure account, set the target Azure Container Registry, and execute the build command.
    
    #### Queue a local context as a Linux build, tag it, and push it to the registry.

    - az acr build --image sample/hello-world:{{.Run.ID}} --registry MyRegistry .

    - az acr build --image myorgprodacr.azurecr.io/nginx:v1 --registry myorgprodacr -g myResourceGroup .

## Verifying the Docker Image in Azure Container Registry
Let's go to the Azure portal and navigate to your Azure Container Registry. By selecting the "Repositories" tab, we can confirm that our Docker image has been successfully pushed to ACR. This step ensures that the image is securely stored and can be accessed when needed.

Congratulations! You have successfully learned how to create a Docker image of an HTML file, run it on Nginx, and store it in Azure Container Registry. This knowledge opens up a world of possibilities for deploying and managing containerized applications in the cloud. Thank you for watching, and happy containerization


# Lecture 2 : Deploying an HTML Website on Nginx with Kubernetes from ACR

"Welcome! In this session, we will explore how to deploy an HTML website on Nginx using Kubernetes. We will leverage the power of Azure Container Registry (ACR) to securely store and pull our Docker image."

## Setting Up Secrets in Kubernetes

To securely authenticate with Azure Container Registry, we need to create a secret in Kubernetes. Execute the following command to generate a secret with your ACR credentials:

```
kubectl create secret docker-registry <secret-name> --docker-server=<acr-login-server> --docker-username=<acr-username> --docker-password=<acr-password> --docker-email=<your-email>

```
Replace <secret-name> with a meaningful name for your secret, <acr-login-server> with the login server URL of your ACR, <acr-username> and <acr-password> with your ACR login credentials, and <your-email> with your email address.

## Deploying the Application to Kubernetes
With our Docker image pushed to ACR and the secret created, we can deploy the application to Kubernetes. 

Create a Kubernetes deployment manifest file with the appropriate specifications, including the image name from ACR and the secret name.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: myorgprodacr.azurecr.io/nginx:v1
          ports:
            - containerPort: 80
      imagePullSecrets:
        - name:  acr-secret
```

Apply the manifest using the following command:
``` kubectl apply -f <deployment-manifest.yaml> ```


Validate the deployment using the following command:
``` kubectl get deploy ```

Congratulations on completing this lecture on deploying an HTML website on Nginx with Kubernetes! Throughout this session, we explored essential concepts related to Kubernetes deployments containerization. 


# Lecture 3 : Exposing the deployment with Service
Welcome to today's lecture! In this session, we will learn how to expose our Kubernetes deployment as a service, allowing external access to our HTML website running on Nginx.

## Understanding Kubernetes Services 
In Kubernetes, services provide a stable endpoint for accessing a set of pods. They enable communication between different components within a cluster and allow external traffic to reach our applications. By exposing our deployment as a service, we can easily access our HTML website from outside the cluster.

## Types of Service
### ClusterIP
`Definition:` ClusterIP is the default service type in Kubernetes. It exposes the service on an internal cluster IP address that is reachable only from within the cluster. It enables communication between different components within the cluster.

`Use Case:` Use ClusterIP to provide backend services that should be accessible only from within the cluster, such as databases or internal APIs.

### NodePort

`Definition:` NodePort exposes the service on a static port on each node's IP address. It allows external access to the service by forwarding traffic from the specified port on the node to the cluster IP.

`Use Case:` Use NodePort when you need to access the service from outside the cluster, but you don't want to use a LoadBalancer. It's suitable for development or testing environments.


### LoadBalancer

`Definition:` LoadBalancer service exposes the service using a cloud provider's load balancer. It automatically provisions a load balancer and assigns an external IP address to the service for external access.

`Use Case:` Use LoadBalancer in production environments when you need external clients to access your service with high availability and reliability.

### ExternalName

`Definition:` ExternalName maps a Kubernetes service to an external DNS name. It does not provide load balancing or proxying, acting as a simple DNS-based redirect.

`Use Case:` Use ExternalName when you need to connect to services outside the Kubernetes cluster by using DNS name resolution, for example, to point to external databases or APIs.




## Deploying the Service
With the service manifest ready, let's deploy it using the following command:
``` kubectl apply -f service.yaml ```

Verifying the Service
``` kubectl get service website-service ```

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-deployment
spec:
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx

```

Congratulations! You have successfully exposed your Kubernetes deployment as a service, enabling external access to your HTML website on Nginx. Services are a crucial component in Kubernetes for networking and connectivity. Thank you for joining this lecture, and happy Kubernetes exploration!


# Summary of Learning :
- We started by understanding Kubernetes and Azure Container Registry (ACR). Kubernetes provides container orchestration, simplifying application deployment and management, while ACR allows secure storage and retrieval of Docker images.
- We then created a Dockerfile to build a Docker image containing an HTML website and Nginx web server.
- Next, we deployed the Docker image to Kubernetes using a deployment manifest. This involved pushing the image to ACR and configuring the deployment specifications.
- To make our application accessible, we exposed the deployment as a service using a service manifest. This allowed external traffic to reach the Nginx web server.
- Finally, we revised advanced topics like scaling deployments, updating website content, and exploring different service types.

# Assignment : Advanced Kubernetes Deployment and Service Revision

Congratulations on completing the lecture on deploying an HTML website on Nginx with Kubernetes! Now, let's put your knowledge to the test with an advanced assignment that will further strengthen your understanding of Kubernetes deployment and service concepts.

## Assignment Instructions:
*Deployment:*
- Create a Kubernetes deployment manifest for deploying a multi-container application consisting of an Nginx web server.
- Instead of storing the html file content inside the docker image, store it in k8s ConfigMap and use Volume - Mounts to directly mount the html file on the nginx container.
- Configure the Nginx container to serve the index.html file as the website content.
- Set the resource limits and requests appropriately for both containers.

*Service:*
- Create a Kubernetes service manifest to expose the Nginx web server as a LoadBalancer service.
- Configure the service to route incoming traffic on port 80 to the Nginx container's port.
- Apply the service manifest and verify that an external IP is assigned to the service.

*Scaling and Updating:*
- Use the appropriate Kubernetes commands to scale the deployment to have three replicas.
- Validate that all three replicas are running and serving the website content.
- Update the index.html file with new content without disrupting the service availability.
- Verify that the updated content is visible when accessing the website.


## *Assignment Notes:*
  - This assignment is designed to challenge you and reinforce your understanding of advanced Kubernetes concepts. Don't hesitate to consult the Kubernetes documentation, experiment, and seek help if needed. Good luck, and have fun exploring Kubernetes!
  - Prepare a single YAML file containing the deployment and service manifests.
  - Include any additional files or instructions required for the assignment.
  - Provide a summary of the changes made and the reasoning behind your decisions.


## Assignment Solution : 
# Wait !!! Please see the solution, only if you have tried the assignment yourself.

## deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: website-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: website
  template:
    metadata:
      labels:
        app: website
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: html-volume
          mountPath: /usr/share/nginx/html
  volumes:
  - name: html-volume
    configMap:
      name: website-configmap
```

## service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: website-service
spec:
  type: LoadBalancer
  selector:
    app: website
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

## configmap.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: website-configmap
data:
  index.html: |
    <html>
      <head>
        <title>My Website</title>
      </head>
      <body>
        <h1>Welcome to my website!</h1>
      </body>
    </html>

```

### Explanation:

- The deployment.yaml file defines a Kubernetes Deployment. It specifies three replicas for high availability and uses a selector to identify the pods associated with this deployment. 
- The deployment template includes two containers: Nginx and Redis. Nginx container serves as the web server, and Redis container acts as a cache. The Nginx container is configured to mount a volume, which will contain the index.html file served as the website content. The Redis container is set up with an environment variable for the Redis password.
- The service.yaml file creates a LoadBalancer service named "website-service". It selects the pods based on the "app: website" label and exposes port 80. This allows external traffic to access the Nginx container.
- The configmap.yaml file defines a ConfigMap named "website-configmap". It contains the content of the index.html file used by the Nginx container. The index.html content can be customized as per your requirements.

#### *To deploy this solution, Apply manifest files: *
``` 
kubectl apply -f <filename> 
```

### Validate:
- Verify the deployment and service: kubectl get deployment, kubectl get pods, kubectl get service
- You can access the website by using the external IP assigned to the service. Make sure to update the index.html content in the configmap.yaml file if desired.
