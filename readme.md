🚀 **Google Kubernetes Engine (GKE) Best Practice**



# 1. The Historical From Borg to Kubernetes

The foundation of Google Kubernetes Engine (GKE) is deeply rooted in Google's internal, secret system called Borg. Since the early 2000s (around 2003–2004), Google utilized Borg to manage billions of critical application containers (like Search and Gmail) efficiently across their massive fleet of servers.

Why Borg Was Created? Managing thousands of machines and applications manually was simply impossible. Therefore, Borg was engineered to automate:
    a. Scaling: Automatically adjusting the number of resources an application needed.
    b. Self-Healing: If a server or application failed, Borg automatically moved and restarted the workload elsewhere without human help.
    c. Efficiency: Ensuring servers were used to their maximum capacity to save costs.


Google knew that other companies faced the same container problems they did. So, in 2014, Google chose to share their solution globally.

Instead of releasing the secret Borg system, they created a new, public project called Kubernetes. Kubernetes uses all the smart ideas and lessons Google learned from running Borg.

Why They Made It: Google built Kubernetes to be the standard way to manage containers. This had two main benefits:
    Works Everywhere (Portability): Your container apps can run easily on any cloud or your own computer system.
    No Vendor Lock-in: You are free to switch between different cloud companies without trouble.

In short, Borg was Google's secret, internal blueprint, and Kubernetes is the simplified, open-source realization of that blueprint, which eventually led to the commercial service GKE.

# 2. When Should You Use Kubernetes?

You should think about using Kubernetes (K8s) when you have many container-based applications and need strong automation to manage them easily.

Condition That Suggests Using, Why K8s Helps:

1. Need to Scale,
   "When your app often has sudden changes in traffic (like many users at once), and you need resources to be added or removed automatically and fast."
2. High Availability,
   "When your app must almost never stop working. K8s automatically sees failed containers and moves them to healthy servers, keeping your app running."
3. Managing Microservices,
   "When you break one big app into many small services. K8s is excellent at managing how all these small services talk to each other.""
4. Efficient Resource Use 
   "When you want to ensure your servers are used as much as possible to save infrastructure costs"
5. Fast and Safe Updates,
   "When your team needs to release new app versions quickly and safely (like Rolling Updates), and easily switch back (rollback) if something goes wrong."
6. Hybrid or Multi-Cloud Needs
   "When you need to run the exact same app on public clouds (AWS, Azure, GCP) and your own local data center."
   
If you only run one or two small apps, Kubernetes might be too complex. K8s is worth it when the complexity of your applications and your need for automation become too much to handle by hand.

# 3. Architectural Best Practice

ubernetes is a very powerful platform, but using it correctly requires the right strategy. Below are the recommended guides and best practices for managing your cluster and applications in Kubernetes efficiently, securely, and sustainably

![Screen Shoot](./design/aristektur.png)

## 3.1 External Services (The Doors and Supplies)

These components live outside the cluster but are crucial for its operation.

| Component           | Simple Description                                                                 | Key Function                                                        |
|---------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------|
| DNS                 | The system that translates website names (like myapp.com) into computer IP addresses. | Guides external user traffic to the cluster's main entrance (Ingress). |
| Container Registry  | The central storage location (warehouse) for all application blueprints (container images like Docker images). | Stores the software images that the Worker Nodes pull when they need to start a new Pod. |


## 3.2 Namespace

A virtual way to divide the cluster's resources into separate, isolated rooms.
Helps organize and separate teams or projects running on the same cluster (e.g., separating the env dev, env testing).

## 3.3 Node Master

This is the management layer that decides where things should run and keeps the cluster in the desired state.

| Component   | Simple Description                                                                 | How It Works                                                                 |
|-------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| Ingress     | The main traffic controller that manages external HTTP/HTTPS access to services inside the cluster. | Directs incoming external requests to the correct internal Service based on the website address or path. |
| Service     | A stable, internal address that groups a set of similar Pods.                      | Acts as an internal Load Balancer, forwarding requests to healthy Pods without the user needing to know their specific location. |
| ConfigMap   | An object used to store non-sensitive configuration data (settings) for applications. | Provides configuration details (like environment variables) to the Pods that need them. |
| Secret      | An object used to store sensitive data securely, such as passwords or API keys.     | Delivers secure credentials to applications running in Pods.                  |
| Namespace   | A virtual way to divide the cluster's resources into separate, isolated rooms.      | Helps organize and separate teams or projects running on the same cluster (e.g., separating the production environment from testing). |


## 3.4 Node Worker

These are the machines where your applications actually run.

### 3.4.1 Workloads and Scaling

| Component     | Simple Description                                                        | Key Function & Relationship                                                |
|---------------|--------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Pod           | The smallest, basic running unit. It holds one or more containers that work together. | Runs a single instance of your application. Everything in Kubernetes focuses on scheduling and managing Pods. |
| Deployment    | An object that manages how updates and rollbacks are done for your application. | Ensures that the desired number of Pods is always running and handles the process of updating your app version gracefully. |
| ReplicaSet    | The mechanism that guarantees a specific, stable number of identical Pods are always available. | Constantly monitors the Pods and immediately replaces any Pod that fails or crashes. |
| StatefulSet   | Used for managing applications that require a unique identity and permanent storage (like databases). | Ensures a fixed order of deployment and gives each Pod its own specific name and dedicated storage. |

### 3.4.2 Data Storage

| Component              | Simple Description                                                        | Purpose                                                                  |
|------------------------|----------------------------------------------------------------------------|--------------------------------------------------------------------------|
| PersistentVolume (PV / vol 1)  | The actual piece of storage hardware (like a cloud disk) made available to the cluster. | Provides storage that lasts longer than a Pod. The data stays safe even if the application Pod is deleted. |
| PersistentVolumeClaim (PVC) | A developer's request for storage (e.g., "I need 10GB of fast storage"). | Acts as a claim that links a Pod to an available PV.                     |


# 4. Mapping YAML ke to Resource GKE

The files you provided in the gke folder represent the standard Kubernetes resources required for a complex     application deployment :

 1. **configmap.yaml**: Environment configuration.
 2. **deployment.yaml**: The application code/replicas.
 3. **ingress.yaml**: External access rules (HTTP/HTTPS).
 4. **secret-tls.yaml**: SSL certificate storage.
 5. **secret.yaml**: Sensitive data storage (e.g., database passwords).
 6. **service-1.yaml, service-2.yaml**: Internal networking to expose Pods.
 7. **statefulset.yaml**: Running stateful applications (e.g., databases like Redis or Kafka).


![Screen Shoot](./ss/maping-yaml.jpg)    


# 5. Basic Setup GKE

1. **Create a New GCP Project**  
    Go to the Google Cloud Console and create a new project. This project will be used to manage all your GKE resources and billing.

    ![Screen Shoot](./ss/gke-setup-1.jpg)

2. **Choose the GKE Product**  
    In the Google Cloud Console, navigate to the Kubernetes Engine section and select the GKE product to start setting up your Kubernetes cluster.

    ![Screen Shoot](./ss/gke-setup-2.jpg)


3. **Activate Billing**  
    Enable billing for your project using a debit or credit card. This is required to use GKE and other Google Cloud resources.

    ![Screen Shoot](./ss/gke-setup-3.jpg)


4. **Access the GKE Dashboard**  
    Once billing is active, you can access the GKE dashboard to create and manage your Kubernetes clusters.

    ![Screen Shoot](./ss/gke-setup-4.jpg)


# 6. Installing Google Cloud CLI and kubectl

The Google Cloud CLI is a set of tools you use to create and manage Google Cloud resources from your terminal.

## Step 1: Download and Install Google Cloud CLI

Download the installer from the official link below and follow the installation instructions:

https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKInstaller.exe

![Google Cloud SDK Installer](./ss/google-sdk-1.jpg)

## Step 2: Initialize Google Cloud CLI

After installation, open your terminal and run:

```sh
gcloud init
```
This command will help you set up your account and default project.

## Step 3: Install kubectl and GKE Auth Plugin

To manage Kubernetes clusters, install the following components:

```sh
gcloud components install kubectl
gcloud components install gke-gcloud-auth-plugin
gcloud components update
```

![Google Cloud SDK Components](./ss/google-sdk-2.jpg)



# 7. Setup Cluster GKE


## Step-by-Step: Creating a GKE Cluster

1. **Create a New GKE Cluster**  
    In the Google Cloud Console, go to Kubernetes Engine and click "Create Cluster" to start the setup process.

    ![Create New GKE Cluster](./ss/gke-cluster-1.jpg)

2. **Choose Regional Cluster**  
    Select the "Regional" option to ensure your cluster is distributed across multiple zones for higher availability.

    ![Choose Regional Cluster](./ss/gke-cluster-2.jpg)

3. **Configure Node Pool**  
    Set the number of worker nodes and choose the provisioning model (such as Standard or Autopilot) according to your needs.

    ![Configure Node Pool](./ss/gke-cluster-3.jpg)

4. **Select VM Specs for Worker Nodes**  
    Pick the VM specifications (CPU, memory, etc.) for your worker nodes based on your workload requirements.

    ![Select VM Specs](./ss/gke-cluster-4.jpg)


# 8. Process Deployment

Below are the basic steps to deploy your application and resources to your GKE cluster. Each step includes a short description and example command.

---

1. **Connect to the Cluster**  
    Connect to your GKE cluster from the Google Cloud Console to start managing resources.

    ![Connect to Cluster](./ss/gke-deploy-1.jpg)
    ![Cluster Overview](./ss/gke-deploy-2.jpg)

2. **Deploy ConfigMap**  
    Create a ConfigMap to store environment configuration for your application.

    ```sh
    kubectl apply -f configmap.yaml -n dev
    ```
    ![Apply ConfigMap](./ss/gke-dep-configmap.jpg)
    ![ConfigMap Created](./ss/gke-dep-configmap2.jpg)

3. **Deploy Secrets**  
    Add sensitive data (like passwords or certificates) using Secret resources.

    ```sh
    kubectl apply -f secret.yaml -n dev
    kubectl apply -f secret-tls.yaml -n dev
    ```
    ![Apply Secret](./ss/gke-dep-secret.jpg)
    ![Secret Created](./ss/gke-dep-secret2.jpg)

4. **Deploy Deployment**  
    Deploy your main application. For example, deploy an Nginx container from Docker Hub with 2 replicas.

    ```sh
    kubectl apply -f deployment.yaml -n dev
    ```
    ![Apply Deployment](./ss/gke-dep-deployment.jpg)
    ![Deployment Created](./ss/gke-dep-deployment2.jpg)

5. **Deploy StatefulSet**  
    Deploy a stateful application, such as Redis, with 2 replicas. This ensures each instance has its own storage.

    ```sh
    kubectl apply -f statesfulset.yaml -n dev
    ```
    ![Apply StatefulSet](./ss/gke-dep-statesfulset.jpg)
    ![StatefulSet Created](./ss/gke-dep-statesfulset2.jpg)
   
    Persistent volumes are automatically created for each replica.
    ![Persistent Volume](./ss/gke-dep-statesfulset2.jpg)

6. **Deploy Services**  
    Expose your Deployments and StatefulSets with Service resources.

    - For Nginx Deployment:
      ```sh
      kubectl apply -f service-1.yaml -n dev
      ```
      ![Nginx Service](./ss/gke-dep-service-ngnix.jpg)
      ![Nginx Service Created](./ss/gke-dep-service-ngnix2.jpg)

    - For Redis StatefulSet:
      ```sh
      kubectl apply -f service-2.yaml -n dev
      ```
      ![Redis Service](./ss/gke-dep-service-redis-statefulset.jpg)
      ![Redis Service Created](./ss/gke-dep-service-redis-statefulset2.jpg)

7. **Deploy Ingress**  
    Set up Ingress to manage external HTTP/HTTPS access to your services.

    ```sh
    kubectl apply -f ingress.yaml -n dev
    ```
    ![Apply Ingress](./ss/gke-dep-ingriss.jpg)
    ![Ingress Created](./ss/gke-dep-ingriss2.jpg)



8. **Test Your App Using the Ingress Public IP**

    After deploying Ingress, you can test your application by accessing the public IP address provided by the Ingress resource.

    ![Test App via Ingress IP](./ss/gke-dep-ingriss3.jpg)

    ![App Response via Ingress](./ss/gke-dep-ingriss3.jpg)

9. **Set Up DNS (A Record) for Your Domain**

    Get the public IP from your Ingress and create an A record in your DNS provider. This will point your domain name to the Ingress public IP, so users can access your app using a friendly URL.

    ![Get Public IP](./ss/gke-dep-ingriss3.jpg)

    Example: Set the A record to the Ingress public IP in your DNS dashboard.

    ![Set A Record](./ss/dns.jpg)
