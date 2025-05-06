# Cloud Native Application with MongoDB - Deployment Documentation

## Overview
This document provides detailed instructions for deploying a cloud-native application with MongoDB on Kubernetes. The application is built for SIT323/SIT737 9.1P task and implements a RESTful API for managing products.

## Deployment Steps

### 1. Prerequisites Setup
Before deployment, ensure you have the following installed:
- Node.js 18+
- Docker and Docker Compose
- Kubernetes cluster (minikube or cloud provider)
- kubectl CLI
- MongoDB 6.0+

### 2. Start Minikube Cluster
```bash
minikube start
```

### 3. Deploy Persistent Storage
```bash
kubectl apply -f k8s/mongodb-pv.yaml
```

### 4. Configure MongoDB Secrets
```bash
kubectl apply -f k8s/mongodb-secret.yaml
kubectl apply -f k8s/mongodb-admin-secret.yaml
kubectl apply -f k8s/mongodb-init-configmap.yaml
```

### 5. Deploy MongoDB
```bash
kubectl apply -f k8s/mongodb-deployment.yaml
```

### 6. Deploy Monitoring Components
```bash
kubectl apply -f k8s/mongodb-exporter.yaml
```

### 7. Deploy the Application
```bash
kubectl apply -f k8s/deployment.yaml
```

### 8. Set Up Automated Backups
```bash
kubectl apply -f k8s/mongodb-backup-cronjob.yaml
```

### 9. Verify Deployment
```bash
kubectl get pods
kubectl get services
```

## Troubleshooting Guide

### Common Issues and Solutions

1. **ImagePullBackOff Error**:
   - Symptom: Pods stuck in ImagePullBackOff state
   - Solution: Ensure Docker images are properly built and pushed to a repository
   ```bash
   docker build -t cloud-native-app .
   ```

2. **MongoDB Connection Issues**:
   - Symptom: Application cannot connect to MongoDB
   - Solution: Verify MongoDB is running and secrets are correctly configured
   ```bash
   kubectl describe secret mongodb-secret
   kubectl logs -l app=mongodb
   ```

3. **Persistent Volume Issues**:
   - Symptom: MongoDB fails to persist data
   - Solution: Check PV and PVC status
   ```bash
   kubectl get pv
   kubectl get pvc
   ```

4. **Monitoring Exporter Issues**:
   - Symptom: MongoDB exporter not collecting metrics
   - Solution: Check exporter logs and service configuration
   ```bash
   kubectl logs -l app=mongodb-exporter
   kubectl describe service mongodb-exporter
   ```

## Monitoring and Metrics

The deployed application includes comprehensive monitoring:

1. **Application Health**:
   - Health check endpoint at `/health`
   - Readiness and liveness probes

2. **MongoDB Monitoring**:
   - MongoDB Exporter (Prometheus metrics)
   - Metrics available at port 9216
   - Key metrics:
     - Connection pool status
     - Operation counters
     - Document metrics

3. **Resource Monitoring**:
   - CPU and memory usage
   - Connection statistics
   - Database operations

## Backup and Recovery

### Automated Backups
- Configured via CronJob
- Runs daily at 1 AM
- Stores backups in a persistent volume

### Manual Backup Operations
```bash
# Create backup
kubectl exec <mongo-pod-name> -- mongodump --db=cloud-native-app --archive=/tmp/backup.gz --gzip
kubectl cp <mongo-pod-name>:/tmp/backup.gz ./backup.gz

# Restore from backup
kubectl cp ./backup.gz <mongo-pod-name>:/tmp/backup.gz
kubectl exec <mongo-pod-name> -- mongorestore --gzip --archive=/tmp/backup.gz
```

## Security Features

1. **Secret Management**:
   - MongoDB credentials stored in Kubernetes secrets
   - Admin credentials separated from application credentials

2. **Network Security**:
   - Internal service communication
   - Resource isolation
   - Container security contexts

## Resource Management

The application uses resource limits and requests:

1. **Application**:
   - Requests: 100m CPU, 128Mi memory
   - Limits: 200m CPU, 256Mi memory

2. **MongoDB**:
   - Requests: 200m CPU, 256Mi memory
   - Limits: 500m CPU, 512Mi memory

3. **MongoDB Exporter**:
   - Requests: 50m CPU, 64Mi memory
   - Limits: 100m CPU, 128Mi memory

## Conclusion

This documentation provides a comprehensive guide for deploying and managing the cloud-native application with MongoDB on Kubernetes. The solution includes monitoring, backup, and security features to ensure reliable operation in a production environment.

For additional support, refer to the Kubernetes and MongoDB documentation or consult with your course instructor.