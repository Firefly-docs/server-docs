# Common kubectl Commands

The following commands are executed on the `bmc` node running the K3s Server. Because the K3s Server runs in a Docker container, all commands use the following prefix:

```bash
sudo docker exec k3s-server kubectl
```

## Cluster Information

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl cluster-info` | View cluster API Server and core component information |
| `sudo docker exec k3s-server kubectl get nodes` | View the node list, status, roles, and versions |
| `sudo docker exec k3s-server kubectl get nodes -o wide` | View node details such as IP addresses, operating system, and container runtime |
| `sudo docker exec k3s-server kubectl describe node <node-name>` | View node resources, labels, taints, and events |

## Pod Management

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl get pods` | View Pods in the current namespace |
| `sudo docker exec k3s-server kubectl get pods -n <namespace>` | View Pods in a specified namespace |
| `sudo docker exec k3s-server kubectl get pods -A` | View Pods in all namespaces |
| `sudo docker exec k3s-server kubectl get pods -o wide` | View Pod IP addresses and the nodes they run on |
| `sudo docker exec k3s-server kubectl describe pod <pod-name>` | View Pod details, events, and container configuration |
| `sudo docker exec k3s-server kubectl logs <pod-name>` | View Pod logs |
| `sudo docker exec k3s-server kubectl logs <pod-name> -f` | Follow Pod logs continuously |
| `sudo docker exec k3s-server kubectl logs <pod-name> -c <container-name>` | View the logs of a specified container |
| `sudo docker exec k3s-server kubectl delete pod <pod-name>` | Delete a specified Pod; Pods managed by a Deployment are recreated automatically |

## Service Management

A Service provides a stable access entry point for a set of Pods. After a Pod is created, deleted, or restarted, its IP address may change, so other applications should not depend on Pod IPs directly. A Service locates the corresponding Pods through a label selector and forwards requests to the available Pods.

In short:

- **Pod**: The container instance that actually runs the application.
- **Service**: Provides a stable name and access address for the Pods.
- **Client**: Accesses the application through the Service without needing to know the specific Pod IPs.

The common Service types are as follows:

| Type | Access Scope | Applicable Scenarios |
|---|---|---|
| `ClusterIP` | Inside the cluster only | Service-to-service calls; the default type |
| `NodePort` | Outside the cluster via node IP and port | Testing or simply exposing a service externally |
| `LoadBalancer` | Exposed externally through a load balancer | Cloud environments or clusters with a configured load balancer |

In the current K3s environment, you can use `NodePort` to expose a web service outside the cluster. For example, after a Service maps container port `80` to a port on the node, the application can be accessed at `http://<node-ip>:<NodePort>`.

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl get svc` | View Services in the current namespace |
| `sudo docker exec k3s-server kubectl get svc -n <namespace>` | View Services in a specified namespace |
| `sudo docker exec k3s-server kubectl get svc -A` | View Services in all namespaces |
| `sudo docker exec k3s-server kubectl describe svc <service-name>` | View Service port mappings and backend Pods |
| `sudo docker exec k3s-server kubectl expose deployment <deployment-name> --type=NodePort --port=80 --target-port=80 --name=<service-name>` | Create a NodePort Service from a Deployment |

## Deployment Management

A `Deployment` manages applications that need to run continuously, such as web services, API services, and background services. It maintains a specified number of Pod replicas through a ReplicaSet and supports rolling updates, automatic fault recovery, and replica scaling.

Applicable scenarios:

- Long-running stateless services
- Applications that require multiple Pod replicas
- Applications that need rolling updates or fast rollbacks

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl get deploy` | View Deployments in the current namespace |
| `sudo docker exec k3s-server kubectl get deploy -n <namespace>` | View Deployments in a specified namespace |
| `sudo docker exec k3s-server kubectl apply -f <yaml-file>` | Create or update resources from a YAML file |
| `sudo docker exec k3s-server kubectl delete -f <yaml-file>` | Delete resources defined in a YAML file |
| `sudo docker exec k3s-server kubectl scale deploy <deployment-name> --replicas=<count>` | Adjust the number of Pod replicas of a Deployment |
| `sudo docker exec k3s-server kubectl rollout status deploy/<deployment-name>` | View the update status of a Deployment |
| `sudo docker exec k3s-server kubectl rollout restart deploy/<deployment-name>` | Restart the Pods managed by a Deployment |

## Job Management

A `Job` runs one-off or time-limited tasks, such as data processing, initialization operations, and batch tasks. A Job creates Pods and keeps retrying until the task completes successfully or the configured number of retries is reached.

Applicable scenarios:

- One-off data processing
- Database initialization or migration
- Batch file processing
- Cluster or application initialization tasks

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl create job <job-name> --image=<image-name>` | Create a one-off Job |
| `sudo docker exec k3s-server kubectl get jobs` | View Jobs in the current namespace |
| `sudo docker exec k3s-server kubectl get jobs -A` | View Jobs in all namespaces |
| `sudo docker exec k3s-server kubectl describe job <job-name>` | View Job status, Pods, and events |
| `sudo docker exec k3s-server kubectl wait --for=condition=complete job/<job-name> --timeout=180s` | Wait for a Job to complete |
| `sudo docker exec k3s-server kubectl logs job/<job-name>` | View the logs of the Pod created by a Job |
| `sudo docker exec k3s-server kubectl delete job <job-name>` | Delete a Job and its associated Pods |
| `sudo docker exec k3s-server kubectl apply -f <job-yaml-file>` | Create or update a Job from a YAML file |

## CronJob Management

A `CronJob` periodically creates Jobs according to a preset schedule, for example scheduled backups, log cleanup, and periodic data synchronization. Each scheduled run creates a Job, which in turn creates and runs a Pod.

Applicable scenarios:

- Scheduled backups
- Periodic cleanup of temporary files or logs
- Periodic data synchronization
- Scheduled report generation

A `CronJob` uses standard cron expressions to set the execution time. For example, `*/5 * * * *` means running every 5 minutes.

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl create cronjob <cronjob-name> --image=<image-name> --schedule="*/5 * * * *"` | Create a CronJob that runs on a schedule |
| `sudo docker exec k3s-server kubectl get cronjobs` | View CronJobs in the current namespace |
| `sudo docker exec k3s-server kubectl get cronjobs -A` | View CronJobs in all namespaces |
| `sudo docker exec k3s-server kubectl describe cronjob <cronjob-name>` | View the schedule, status, and events of a CronJob |
| `sudo docker exec k3s-server kubectl get jobs --sort-by=.metadata.creationTimestamp` | View the Jobs created by CronJobs |
| `sudo docker exec k3s-server kubectl get pods --sort-by=.metadata.creationTimestamp` | View the Pods created by CronJobs |
| `sudo docker exec k3s-server kubectl patch cronjob <cronjob-name> -p '{"spec":{"suspend":true}}'` | Suspend further scheduling of a CronJob |
| `sudo docker exec k3s-server kubectl patch cronjob <cronjob-name> -p '{"spec":{"suspend":false}}'` | Resume scheduling of a CronJob |
| `sudo docker exec k3s-server kubectl delete cronjob <cronjob-name>` | Delete a CronJob and its future schedules |
| `sudo docker exec k3s-server kubectl apply -f <cronjob-yaml-file>` | Create or update a CronJob from a YAML file |

## Namespaces and Configuration

Namespaces divide mutually isolated resource scopes within the same K3s cluster. You can create different namespaces by project, team, or environment — for example `dev`, `test`, and `prod` — to avoid resource name conflicts between applications and to make permission and resource management easier.

ConfigMaps and Secrets store the configuration required by applications:

- **ConfigMap**: Stores non-sensitive configuration such as environment variables, configuration files, and service addresses.
- **Secret**: Stores sensitive information such as passwords, tokens, and certificates. A Secret is not a complete encrypted storage solution; you should still restrict access and avoid committing sensitive content to code repositories.

When creating an application, you can specify the namespace in the resource manifest via `metadata.namespace`; when running commands, you can specify the target namespace with `-n <namespace>`. If no namespace is specified, commands apply to the current namespace, which is usually `default`.

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl get ns` | View all namespaces |
| `sudo docker exec k3s-server kubectl create ns <namespace-name>` | Create a namespace |
| `sudo docker exec k3s-server kubectl get configmap` | View ConfigMaps in the current namespace |
| `sudo docker exec k3s-server kubectl get secret` | View Secrets in the current namespace |
| `sudo docker exec k3s-server kubectl get events -A --sort-by=.lastTimestamp` | View events in all namespaces in chronological order |

## Resources and Troubleshooting

| Command | Purpose |
|---|---|
| `sudo docker exec k3s-server kubectl top nodes` | View CPU and memory usage of nodes |
| `sudo docker exec k3s-server kubectl top pods -A` | View CPU and memory usage of all Pods |
| `sudo docker exec k3s-server kubectl get pods -A -o wide` | View the status, IP addresses, and nodes of all Pods |
| `sudo docker exec k3s-server kubectl describe pod <pod-name> -n <namespace>` | View detailed Pod information and abnormal events |
| `sudo docker logs --tail=200 k3s-server` | View the K3s Server container logs |
