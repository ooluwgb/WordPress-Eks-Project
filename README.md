
# WordPress Application on AWS EKS with MySQL

This project deploys a WordPress application with a MySQL database on Amazon EKS (Elastic Kubernetes Service). The application is accessible via a public LoadBalancer service, and uses Persistent Volume Claims (PVCs) to store data on AWS Elastic Block Store (EBS) volumes.

## Architecture Overview

- **MySQL**: A MySQL 5.7 database running as a Kubernetes Deployment and exposed internally within the cluster.
- **WordPress**: A WordPress application running as a Kubernetes Deployment and exposed to the internet via a LoadBalancer service.
- **Persistent Storage**: Both MySQL and WordPress use dynamically provisioned Persistent Volume Claims for data storage, leveraging AWS EBS volumes.

## Prerequisites

- AWS account with EKS configured.
- Kubernetes CLI (`kubectl`) set up and connected to your EKS cluster.
- IAM permissions for EBS volume provisioning.

## Files Overview

- `mysql-deployment.yaml`: MySQL Deployment configuration.
- `mysql-service.yaml`: Internal ClusterIP Service for MySQL.
- `mysql-pvc.yaml`: Persistent Volume Claim for MySQL data storage.
- `wordpress-deployment.yaml`: WordPress Deployment configuration.
- `wordpress-service.yaml`: LoadBalancer Service for WordPress, exposed to the internet.
- `wordpress-pvc.yaml`: Persistent Volume Claim for WordPress data storage.

## Deployment Instructions

1. **Apply the MySQL Resources**:
   ```bash
   kubectl apply -f mysql-deployment.yaml
   kubectl apply -f mysql-service.yaml
   kubectl apply -f mysql-pvc.yaml
   ```

2. **Apply the WordPress Resources**:
   ```bash
   kubectl apply -f wordpress-deployment.yaml
   kubectl apply -f wordpress-service.yaml
   kubectl apply -f wordpress-pvc.yaml
   ```

3. **Verify the Deployments**:
   Check that all pods are running:
   ```bash
   kubectl get pods
   ```

4. **Access the Application**:
   - Use the following command to get the external IP of the WordPress service:
     ```bash
     kubectl get svc wordpress
     ```
   - Open a browser and navigate to the external IP to access the WordPress setup page.

## Cleanup

To delete the resources:
```bash
kubectl delete -f mysql-deployment.yaml
kubectl delete -f mysql-service.yaml
kubectl delete -f mysql-pvc.yaml
kubectl delete -f wordpress-deployment.yaml
kubectl delete -f wordpress-service.yaml
kubectl delete -f wordpress-pvc.yaml
```

## Notes

- Ensure that AWS EKS has the necessary permissions to provision EBS volumes.
- The LoadBalancer service may incur charges based on AWS pricing.

