# Cloud Native Application with MongoDB

This is a cloud-native application built for SIT323/SIT737 9.1P task. It implements a RESTful API for managing products using Node.js, Express, MongoDB, Docker, and Kubernetes with comprehensive monitoring and backup solutions.

## Prerequisites

- Node.js 18+
- Docker and Docker Compose
- Kubernetes cluster (minikube or cloud provider)
- kubectl CLI
- MongoDB 6.0+

## Project Structure

```
.
├── src/
│   └── index.js                    # Main application file
├── k8s/
│   ├── deployment.yaml             # Main application deployment
│   ├── mongodb-deployment.yaml     # MongoDB deployment configuration
│   ├── mongodb-secret.yaml         # MongoDB credentials secret
│   ├── mongodb-admin-secret.yaml   # MongoDB admin credentials
│   ├── mongodb-init-configmap.yaml # MongoDB initialization scripts
│   ├── mongodb-backup-cronjob.yaml # Automated backup configuration
│   ├── mongodb-exporter.yaml       # Prometheus metrics exporter
│   └── mongodb-pv.yaml            # Persistent volume configuration
├── Dockerfile                      # Application container image
├── docker-compose.yml              # Local development setup
├── package.json                    # Node.js dependencies
└── README.md
```

## Local Development

1. Install dependencies:
   ```bash
   npm install
   ```

2. Start the application with Docker Compose:
   ```bash
   docker-compose up --build
   ```

3. The API will be available at `http://localhost:3000`

## API Endpoints

- `GET /api/products` - List all products
- `POST /api/products` - Create a new product
- `PUT /api/products/:id` - Update a product
- `DELETE /api/products/:id` - Delete a product
- `GET /health` - Health check endpoint

## Kubernetes Deployment

1. Create the persistent volume and claim:
   ```bash
   kubectl apply -f k8s/mongodb-pv.yaml
   ```

2. Create the MongoDB secrets and config:
   ```bash
   kubectl apply -f k8s/mongodb-secret.yaml
   kubectl apply -f k8s/mongodb-admin-secret.yaml
   kubectl apply -f k8s/mongodb-init-configmap.yaml
   ```

3. Deploy MongoDB:
   ```bash
   kubectl apply -f k8s/mongodb-deployment.yaml
   ```

4. Deploy the monitoring components:
   ```bash
   kubectl apply -f k8s/mongodb-exporter.yaml
   ```

5. Deploy the application:
   ```bash
   kubectl apply -f k8s/deployment.yaml
   ```

6. Set up automated backups:
   ```bash
   kubectl apply -f k8s/mongodb-backup-cronjob.yaml
   ```

7. Check deployment status:
   ```bash
   kubectl get pods
   kubectl get services
   ```

## Monitoring and Metrics

The application includes comprehensive monitoring:

1. Application Health:
   - Health check endpoint at `/health`
   - Readiness and liveness probes
   - Resource limits and requests

2. MongoDB Monitoring:
   - MongoDB Exporter (Prometheus metrics)
   - Metrics available at port 9216
   - Key metrics:
     - Connection pool status
     - Operation counters
     - Document metrics
     - Replication status

3. Resource Monitoring:
   - CPU and memory usage
   - Connection statistics
   - Database operations
   - Error rates

## Backup and Recovery

### Automated Backups
- Configured via CronJob
- Runs on a scheduled basis
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

1. Secret Management:
   - MongoDB credentials stored in Kubernetes secrets
   - Admin credentials separated from application credentials
   - Secure connection strings

2. Network Security:
   - Internal service communication
   - Resource isolation
   - Container security contexts

## Troubleshooting

### Common Issues and Solutions

1. Pod Issues:
   ```bash
   # Check pod status
   kubectl describe pod <pod-name>
   kubectl get events

   # Check logs
   kubectl logs <pod-name>
   ```

2. MongoDB Connection Issues:
   ```bash
   # Verify secrets
   kubectl describe secret mongodb-secret
   kubectl describe secret mongodb-admin-secret

   # Check MongoDB logs
   kubectl logs -l app=mongodb
   ```

3. Monitoring Issues:
   ```bash
   # Check exporter
   kubectl logs -l app=mongodb-exporter
   kubectl describe service mongodb-exporter
   ```

4. Backup Issues:
   ```bash
   # Check CronJob status
   kubectl get cronjobs
   kubectl describe cronjob mongodb-backup
   
   # Check backup logs
   kubectl logs job/mongodb-backup-<timestamp>
   ```

## Resource Management

The application uses resource limits and requests to ensure stable operation:

1. Application:
   - Requests: 100m CPU, 128Mi memory
   - Limits: 200m CPU, 256Mi memory

2. MongoDB:
   - Requests: 200m CPU, 256Mi memory
   - Limits: 500m CPU, 512Mi memory

3. MongoDB Exporter:
   - Requests: 50m CPU, 64Mi memory
   - Limits: 100m CPU, 128Mi memory

## License

This project is created for educational purposes as part of SIT323/SIT737 coursework.