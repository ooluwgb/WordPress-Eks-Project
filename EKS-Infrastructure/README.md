
# AWS EKS Cluster Setup

This guide provides step-by-step instructions to set up a fully functional Amazon EKS (Elastic Kubernetes Service) cluster on AWS, including all required IAM roles, security groups, VPC, and other resources.

---

## Prerequisites

- **AWS Account** with necessary permissions for creating EKS, EC2, IAM, and VPC resources.
- **AWS CLI** and **kubectl** installed if configuring from a local machine.

---

## Step-by-Step Setup

### 1. Set Up the VPC
1. Go to the **VPC Console** in AWS.
2. Create a **VPC** with both **public** and **private subnets**.
3. Set up an **Internet Gateway** and associate it with the VPC for internet access in public subnets.
4. (Optional) If your cluster needs internet access in private subnets, create a **NAT Gateway** in a public subnet and configure route tables accordingly.

### 2. Create IAM Roles
- Go to the **IAM Console**.

#### a. EKS Cluster Role
1. Create an IAM Role for the EKS cluster with the `AmazonEKSClusterPolicy`.
2. Name it something like `eks-cluster-role` and save.

#### b. EKS Node Group Role
1. Create another IAM Role for the node group (worker nodes).
2. Attach policies:
   - `AmazonEKSWorkerNodePolicy`
   - `AmazonEC2ContainerRegistryReadOnly`
   - `AmazonEKS_CNI_Policy`
3. Name the role `eks-node-role`.

### 3. Set Up Security Groups
- Go to **VPC Console** > **Security Groups**.

#### a. Control Plane Security Group
1. Create a security group for the EKS control plane, allowing inbound traffic on:
   - TCP 443 for Kubernetes API.
   - TCP 80 if needed for HTTP traffic.
2. Allow all outbound traffic.

#### b. Worker Nodes Security Group
1. Create a security group for the worker nodes, allowing inbound traffic on:
   - TCP 443 and TCP 10250 for communication with the control plane.
   - TCP 30000-32767 for Kubernetes LoadBalancer services.
2. Allow all outbound traffic.

### 4. Provision the EKS Cluster
1. Go to the **EKS Console** > **Create Cluster**.
2. Enter a name and select Kubernetes version.
3. Select the VPC and subnets.
4. Choose the EKS cluster IAM role (`eks-cluster-role`).
5. Configure **Cluster endpoint access** settings.
6. Click **Create** to start provisioning.

### 5. Add a Node Group
1. In the EKS Console, go to **Compute** > **Add Node Group**.
2. Name the node group and choose the **Node IAM role** (`eks-node-role`).
3. Select instance types and desired capacity.
4. Choose subnets and configure scaling.
5. Click **Create**.

### 6. Set Up an EKS Workstation (Optional)
1. Launch an EC2 instance (Amazon Linux 2 recommended) as an EKS workstation.
2. Install **AWS CLI** and **kubectl**:
   ```bash
   sudo yum update
   sudo yum install -y aws-cli
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   chmod +x ./kubectl
   sudo mv ./kubectl /usr/local/bin/kubectl
   ```

### 7. Configure kubectl Access
1. Use AWS CLI to update `kubeconfig`:
   ```bash
   aws eks update-kubeconfig --name <cluster-name> --region <region>
   ```
2. Verify connection:
   ```bash
   kubectl get nodes
   ```

---

After these steps, your EKS cluster will be ready to deploy applications.

---

## Notes
- For production environments, consider additional security measures, such as limiting access to the API server.
- Be mindful of EKS charges and other AWS resource costs.
