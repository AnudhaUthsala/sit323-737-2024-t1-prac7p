code@code-Satellite-C870-D7K:~/Desktop/01$ docker-compose down
Removing 01_app_1   ... done
Removing 01_mongo_1 ... done
Removing network 01_app-network
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl apply -f k8s/mongodb-pv.yaml
error: error validating "k8s/mongodb-pv.yaml": error validating data: failed to download openapi: Get "https://192.168.49.2:8443/openapi/v2?timeout=32s": dial tcp 192.168.49.2:8443: connect: no route to host; if you choose to ignore these errors, turn validation off with --validate=false
code@code-Satellite-C870-D7K:~/Desktop/01$ minikube start
😄  minikube v1.35.0 on Ubuntu 22.04
✨  Using the docker driver based on existing profile
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.46 ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.32.0 on Docker 27.4.1 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl apply -f k8s/mongodb-admin-secret.yaml -f k8s/mongodb-init-configmap.yaml -f k8s/mongodb-secret.yaml
secret/mongodb-admin-secret created
configmap/mongodb-init-script created
secret/mongodb-secret created
code@code-Satellite-C870-D7K:~/Desktop/01$ minikube start
😄  minikube v1.35.0 on Ubuntu 22.04
✨  Using the docker driver based on existing profile
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.46 ...
🏃  Updating the running docker "minikube" container ...
🐳  Preparing Kubernetes v1.32.0 on Docker 27.4.1 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl apply -f k8s/mongodb-secret.yaml -f k8s/mongodb-pv.yaml -f k8s/mongodb-deployment.yaml -f k8s/mongodb-init-configmap.yaml
secret/mongodb-secret unchanged
persistentvolume/mongodb-pv created
persistentvolumeclaim/mongodb-pvc created
deployment.apps/mongodb created
service/mongodb created
configmap/mongodb-init-script unchanged
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl apply -f k8s/deployment.yaml
deployment.apps/cloud-native-app created
service/cloud-native-app created
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl get pods
NAME                              READY   STATUS         RESTARTS      AGE
calculator-app-6479886459-6fnrs   1/1     Running        2 (71s ago)   23d
calculator-app-6479886459-8h7kp   1/1     Running        2 (71s ago)   23d
cloud-native-app-5698f6f-2bkvv    0/1     Pending        0             10s
cloud-native-app-5698f6f-tc6xc    0/1     Pending        0             10s
mongodb-556869bd77-nfrlf          0/1     Pending        0             20s
nginx-86c57bc6b8-49cmg            0/1     ErrImagePull   2 (23d ago)   23d
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl describe pod -l app=mongodb
Name:             mongodb-556869bd77-nfrlf
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Tue, 06 May 2025 10:35:35 +0530
Labels:           app=mongodb
                  pod-template-hash=556869bd77
Annotations:      <none>
Status:           Pending
IP:               
IPs:              <none>
Controlled By:    ReplicaSet/mongodb-556869bd77
Containers:
  mongodb:
    Container ID:   
    Image:          mongo:6.0
    Image ID:       
    Port:           27017/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  512Mi
    Requests:
      cpu:     200m
      memory:  256Mi
    Environment:
      MONGO_INITDB_ROOT_USERNAME:  <set to the key 'username' in secret 'mongodb-admin-secret'>  Optional: false
      MONGO_INITDB_ROOT_PASSWORD:  <set to the key 'password' in secret 'mongodb-admin-secret'>  Optional: false
      MONGO_INITDB_DATABASE:       cloud-native-app
    Mounts:
      /data/db from mongodb-data (rw)
      /docker-entrypoint-initdb.d/ from init-script (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-snv9l (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   False 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  mongodb-data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  mongodb-pvc
    ReadOnly:   false
  init-script:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      mongodb-init-script
    Optional:  false
  kube-api-access-snv9l:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  29s   default-scheduler  Successfully assigned default/mongodb-556869bd77-nfrlf to minikube
  Normal  Pulling    5s    kubelet            Pulling image "mongo:6.0"
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl get pods -w
NAME                              READY   STATUS              RESTARTS       AGE
calculator-app-6479886459-6fnrs   1/1     Running             2 (108s ago)   23d
calculator-app-6479886459-8h7kp   1/1     Running             2 (108s ago)   23d
cloud-native-app-5698f6f-2bkvv    0/1     ContainerCreating   0              47s
cloud-native-app-5698f6f-tc6xc    0/1     ContainerCreating   0              47s
mongodb-556869bd77-nfrlf          0/1     ContainerCreating   0              57s
nginx-86c57bc6b8-49cmg            0/1     ErrImagePull        2 (23d ago)    23d
nginx-86c57bc6b8-49cmg            1/1     Running             3 (23d ago)    23d
^Ccode@code-Satellite-C870-D7K:~/Desktop/01$ kubectl get secrets,configmaps | grep mongodb
secret/mongodb-admin-secret   Opaque   2      5m12s
secret/mongodb-secret         Opaque   3      5m12s
configmap/mongodb-init-script   1      5m12s
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl logs -l app=mongodb
Error from server (BadRequest): container "mongodb" in pod "mongodb-556869bd77-nfrlf" is waiting to start: ContainerCreating
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl get events --sort-by='.lastTimestamp'
LAST SEEN   TYPE      REASON                    OBJECT                                MESSAGE
3m49s       Normal    Starting                  node/minikube                         
3m25s       Normal    Scheduled                 pod/cloud-native-app-5698f6f-tc6xc    Successfully assigned default/cloud-native-app-5698f6f-tc6xc to minikube
3m35s       Normal    Scheduled                 pod/mongodb-556869bd77-nfrlf          Successfully assigned default/mongodb-556869bd77-nfrlf to minikube
3m25s       Normal    Scheduled                 pod/cloud-native-app-5698f6f-2bkvv    Successfully assigned default/cloud-native-app-5698f6f-2bkvv to minikube
6m          Normal    Starting                  node/minikube                         
6m16s       Normal    NodeAllocatableEnforced   node/minikube                         Updated Node Allocatable limit across pods
6m16s       Normal    NodeHasSufficientPID      node/minikube                         Node minikube status is now: NodeHasSufficientPID
6m16s       Normal    Starting                  node/minikube                         Starting kubelet.
6m15s       Normal    NodeHasNoDiskPressure     node/minikube                         Node minikube status is now: NodeHasNoDiskPressure
6m15s       Normal    NodeHasSufficientMemory   node/minikube                         Node minikube status is now: NodeHasSufficientMemory
6m8s        Warning   Rebooted                  node/minikube                         Node minikube has been rebooted, boot id: 6f703c6d-da59-4f0d-b2ee-4ae5dae2c115
6m3s        Normal    RegisteredNode            node/minikube                         Node minikube event: Registered Node minikube in Controller
4m14s       Warning   ContainerGCFailed         node/minikube                         rpc error: code = Unknown desc = Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
4m12s       Warning   Failed                    pod/nginx-86c57bc6b8-49cmg            Failed to pull image "nginx": unexpected EOF
4m12s       Warning   Failed                    pod/nginx-86c57bc6b8-49cmg            Error: ErrImagePull
4m7s        Warning   FailedSync                pod/nginx-86c57bc6b8-49cmg            error determining status: rpc error: code = Unavailable desc = connection error: desc = "error reading server preface: read unix @->/run/cri-dockerd.sock: read: connection reset by peer"
4m3s        Normal    SandboxChanged            pod/calculator-app-6479886459-6fnrs   Pod sandbox changed, it will be killed and re-created.
4m3s        Normal    SandboxChanged            pod/nginx-86c57bc6b8-49cmg            Pod sandbox changed, it will be killed and re-created.
4m3s        Normal    SandboxChanged            pod/calculator-app-6479886459-8h7kp   Pod sandbox changed, it will be killed and re-created.
3m59s       Normal    Created                   pod/calculator-app-6479886459-8h7kp   Created container: calculator
3m59s       Normal    Pulled                    pod/calculator-app-6479886459-8h7kp   Container image "sit323-calculator" already present on machine
3m58s       Normal    Pulled                    pod/calculator-app-6479886459-6fnrs   Container image "sit323-calculator" already present on machine
3m58s       Normal    Pulling                   pod/nginx-86c57bc6b8-49cmg            Pulling image "nginx"
3m58s       Normal    Created                   pod/calculator-app-6479886459-6fnrs   Created container: calculator
3m57s       Normal    Started                   pod/calculator-app-6479886459-8h7kp   Started container calculator
3m56s       Normal    Started                   pod/calculator-app-6479886459-6fnrs   Started container calculator
3m47s       Normal    RegisteredNode            node/minikube                         Node minikube event: Registered Node minikube in Controller
3m36s       Normal    SuccessfulCreate          replicaset/mongodb-556869bd77         Created pod: mongodb-556869bd77-nfrlf
3m36s       Normal    ExternalProvisioning      persistentvolumeclaim/mongodb-pvc     Waiting for a volume to be created either by the external provisioner 'k8s.io/minikube-hostpath' or manually by the system administrator. If volume creation is delayed, please verify that the provisioner is running and correctly registered.
3m36s       Normal    ScalingReplicaSet         deployment/mongodb                    Scaled up replica set mongodb-556869bd77 from 0 to 1
3m26s       Normal    SuccessfulCreate          replicaset/cloud-native-app-5698f6f   Created pod: cloud-native-app-5698f6f-2bkvv
3m26s       Normal    ScalingReplicaSet         deployment/cloud-native-app           Scaled up replica set cloud-native-app-5698f6f from 0 to 2
3m26s       Normal    SuccessfulCreate          replicaset/cloud-native-app-5698f6f   Created pod: cloud-native-app-5698f6f-tc6xc
3m11s       Normal    Pulling                   pod/mongodb-556869bd77-nfrlf          Pulling image "mongo:6.0"
3m10s       Normal    Pulling                   pod/cloud-native-app-5698f6f-tc6xc    Pulling image "cloud-native-app:latest"
3m10s       Normal    Pulling                   pod/cloud-native-app-5698f6f-2bkvv    Pulling image "cloud-native-app:latest"
91s         Normal    Pulled                    pod/nginx-86c57bc6b8-49cmg            Successfully pulled image "nginx" in 2m27.483s (2m27.483s including waiting). Image size: 192484959 bytes.
90s         Normal    Created                   pod/nginx-86c57bc6b8-49cmg            Created container: nginx
90s         Normal    Started                   pod/nginx-86c57bc6b8-49cmg            Started container nginx
code@code-Satellite-C870-D7K:~/Desktop/01$ docker build -t cloud-native-app:latest .
[+] Building 5.0s (11/11) FINISHED                    docker:default
 => [internal] load build definition from Dockerfile            0.0s
 => => transferring dockerfile: 156B                            0.0s
 => [internal] load metadata for docker.io/library/node:18-alp  4.1s
 => [auth] library/node:pull token for registry-1.docker.io     0.0s
 => [internal] load .dockerignore                               0.0s
 => => transferring context: 2B                                 0.0s
 => [1/5] FROM docker.io/library/node:18-alpine@sha256:8d6421d  0.0s
 => [internal] load build context                               0.5s
 => => transferring context: 462.28kB                           0.5s
 => CACHED [2/5] WORKDIR /app                                   0.0s
 => CACHED [3/5] COPY package*.json ./                          0.0s
 => CACHED [4/5] RUN npm install                                0.0s
 => CACHED [5/5] COPY . .                                       0.0s
 => exporting to image                                          0.0s
 => => exporting layers                                         0.0s
 => => writing image sha256:3fbeae484dfb377a027a3f4feb4a31da38  0.0s
 => => naming to docker.io/library/cloud-native-app:latest      0.0s
code@code-Satellite-C870-D7K:~/Desktop/01$ minikube image load cloud-native-app:latest
code@code-Satellite-C870-D7K:~/Desktop/01$ kubectl get pods
NAME                              READY   STATUS              RESTARTS      AGE
calculator-app-6479886459-6fnrs   1/1     Running             2 (11m ago)   23d
calculator-app-6479886459-8h7kp   1/1     Running             2 (11m ago)   23d
cloud-native-app-5698f6f-2bkvv    0/1     ErrImagePull        0             10m
cloud-native-app-5698f6f-tc6xc    0/1     ContainerCreating   0             10m
mongodb-556869bd77-nfrlf          1/1     Running             0             10m
nginx-86c57bc6b8-49cmg            1/1     Running             3 (23d ago)   23d
code@code-Satellite-C870-D7K:~/Desktop/01$ 