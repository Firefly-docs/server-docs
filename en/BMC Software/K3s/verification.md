# K3s Cluster Verification

This document verifies that the K3s cluster can run workloads properly, and checks basic functions such as Jobs, Deployments, Services, and replica scaling.

> The following commands are executed on the `bmc` node running the K3s Server.

## Verification Scope

| Verification Item | Objective |
|---|---|
| Cluster status | Confirm that the Server and Agent nodes have joined the cluster and are in the `Ready` state |
| Job | Verify that the cluster can pull images and run short-lived tasks |
| Deployment | Verify that continuously running applications can create and manage Pods properly |
| NodePort Service | Verify that application services are accessible from outside the cluster |
| Replica scaling | Verify that the Deployment can adjust the number of Pod replicas as configured |

Before starting verification, confirm that the `k3s-server` container is running and that the nodes have finished initializing.

## Check the Cluster Status

### Check the Node Status

Confirm that both `bmc` and the Agent nodes have joined the cluster and that their status is `Ready`:

```bash
sudo docker exec k3s-server  kubectl get nodes -o wide
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl get nodes -o wide
NAME    STATUS   ROLES           AGE    VERSION        INTERNAL-IP   EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION    CONTAINER-RUNTIME
bmc     Ready    control-plane   41h    v1.36.3+k3s1   172.22.10.0   <none>        K3s v1.36.3+k3s1   6.1.118 (arm64)   containerd://2.3.2-k3s2
sub04   Ready    <none>          176m   v1.36.3+k3s1   172.22.14.0   <none>        K3s v1.36.3+k3s1   6.1.84 (arm64)    containerd://2.3.2-k3s2
```

## Create a Job to Verify Short-Lived Tasks

Jobs are suitable for one-off or short-running tasks. The following example creates a Job to verify that the cluster can pull images and run containers.

### Create the Job

```bash
sudo docker exec k3s-server  kubectl create job hello  --image=hello-world:latest
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl create job hello  --image=hello-world:latest
job.batch/hello created

```

### Wait for the Job to Complete

Wait for the Job to complete successfully:

```bash
sudo docker exec k3s-server  kubectl wait --for=condition=complete job/hello --timeout=180s
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl wait --for=condition=complete job/hello --timeout=180s
job.batch/hello condition met
```

### Check the Node Where the Pod Is Scheduled

Confirm that the Pod created by the Job has been scheduled to a node and its status is `Completed`:

```bash
sudo docker exec k3s-server  kubectl get pods  -l job-name=hello -o wide
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl get pods  -l job-name=hello -o wide
NAME          READY   STATUS      RESTARTS   AGE   IP          NODE    NOMINATED NODE   READINESS GATES
hello-klrx8   0/1     Completed   0          67s   10.42.1.5   sub04   <none>           <none>
```
### View the Container Output

Check the container logs of the Job to confirm the task result:

```bash
 sudo docker exec k3s-server  kubectl logs job/hello
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl logs job/hello

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/


```

### Delete the Job

After verification, delete the test Job and its Pod:

```bash
sudo docker exec k3s-server  kubectl delete job hello
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl delete job hello
job.batch "hello" deleted from default namespace
```

## Create a Deployment to Verify Long-Running Tasks

Deployments are suitable for managing continuously running applications and for maintaining a specified number of Pod replicas.

### Create the Deployment

```bash
sudo docker exec k3s-server  kubectl create deployment nginx --image=nginx:alpine --replicas=1
```

```shell
root@bmc:/# sudo docker exec k3s-server  kubectl create deployment nginx --image=nginx:alpine --replicas=1
deployment.apps/nginx created
```

### Check the Deployment Status

Confirm that the Deployment has reached the desired number of replicas:

```shell
sudo docker exec k3s-server kubectl get deployment nginx
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl get deployment nginx
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           66s
```

### Check the Pod Distribution of the Deployment

Check the running status of the NGINX Pod and the node it runs on:

```shell
sudo docker exec k3s-server kubectl get pods -l app=nginx -o wide
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl get pods -l app=nginx -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP          NODE    NOMINATED NODE   READINESS GATES
nginx-85b9b8c477-7tsdv   1/1     Running   0          97s   10.42.1.8   sub04   <none>           <none>
```

### Expose the Network Service Port

Expose the NGINX service to a node port through NodePort so it can be accessed from outside the cluster.

#### Create a NodePort Service

```shell
sudo docker exec k3s-server kubectl expose deployment nginx --name=nginx --type=NodePort --port=80 --target-port=80
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl expose deployment nginx --name=nginx --type=NodePort --port=80 --target-port=80
service/nginx exposed
```
#### Check the Assigned Port

Note the NodePort in the `PORT(S)` column, for example `32363`. This port is used later to access the service.

```shell
sudo docker exec k3s-server kubectl get service nginx
```

```shell
root@bmc:/# sudo docker exec k3s-server kubectl get service nginx
NAME    TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx   NodePort   10.43.214.154   <none>        80:32363/TCP   90s
```

#### Access the NGINX Service

Replace `32363` in the command below with the NodePort actually assigned in the previous step:

```bash
curl http://172.16.100.170:32363
```

Example response:

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy,
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

## Scale the Deployment Up and Down

By adjusting the number of Deployment replicas, verify the workload scheduling and replica management capabilities of K3s.

### Scale Down to 1 Replica

```shell
sudo docker exec k3s-server kubectl scale deployment nginx --replicas=1
```

### Scale Up to 2 Replicas

```shell
sudo docker exec k3s-server kubectl scale deployment nginx --replicas=2
```

### Watch the Replica Changes

Use `-w` to continuously watch the creation, scheduling, and readiness of Pods:

```shell
sudo docker exec k3s-server kubectl get pods -l app=nginx -o wide -w
```

```shell
bmc@bmc:~$   sudo k3s kubectl get pods -l app=nginx -o wide -w
NAME                     READY   STATUS    RESTARTS   AGE   IP          NODE    NOMINATED NODE   READINESS GATES
nginx-85b9b8c477-58lzj   1/1     Running   0          16m   10.42.2.5   sub02   <none>           <none>
nginx-85b9b8c477-ps62k   1/1     Running   0          9s    10.42.3.4   sub01   <none>           <none>
```
