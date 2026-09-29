# Common Commands

## Cluster Information

| Command | Purpose |
|---|---|
| `kubectl cluster-info` | Show the cluster API Server and core component information |
| `kubectl get nodes` | Show the node list, status, roles, and versions |
| `kubectl get nodes -o wide` | Show node IP addresses, operating systems, container runtimes, and other details |
| `kubectl describe node <node name>` | Show node resources, labels, taints, and events |

## Pod Management

| Command | Purpose |
|---|---|
| `kubectl get pods` | Show the Pods in the current namespace |
| `kubectl get pods -n <namespace>` | Show the Pods in the specified namespace |
| `kubectl get pods -A` | Show the Pods in all namespaces |
| `kubectl get pods -o wide` | Show the Pod IP addresses and the nodes they run on |
| `kubectl describe pod <pod name>` | Show Pod details, events, and container configuration |
| `kubectl logs <pod name>` | Show the Pod logs |
| `kubectl logs <pod name> -f` | Follow the Pod logs |
| `kubectl logs <pod name> -c <container name>` | Show the logs of the specified container |
| `kubectl delete pod <pod name>` | Delete the specified Pod; Pods managed by a Deployment are recreated automatically |

## Service Management

A Service provides a stable entry point for a group of Pods. A Pod IP may change after the Pod is created, deleted, or restarted, so other applications should not depend on Pod IPs directly. A Service finds the matching Pods through a label selector and forwards requests to the available Pods.

In short:

- **Pod**: the container instance that actually runs the application.
- **Service**: provides a stable name and address for the Pods.
- **Client**: accesses the application through the Service without caring about the individual Pod IPs.

The common Service types are:

| Type | Access scope | Typical use |
|---|---|---|
| `ClusterIP` | Inside the cluster only | Service-to-service calls; this is the default type |
| `NodePort` | Reachable from outside through a node IP and port | Testing or exposing a service in a simple way |
| `LoadBalancer` | Reachable through a load balancer | Cloud environments or clusters with a load balancer configured |

In the current K3s environment, a `NodePort` can expose a web service outside the cluster. For example, once a Service maps port `80` of a container to a port on the node, the application is reachable at `http://<node IP>:<NodePort>`.

| Command | Purpose |
|---|---|
| `kubectl get svc` | Show the Services in the current namespace |
| `kubectl get svc -n <namespace>` | Show the Services in the specified namespace |
| `kubectl get svc -A` | Show the Services in all namespaces |
| `kubectl describe svc <service name>` | Show Service port mappings and backend Pods |
| `kubectl expose deployment <deployment name> --type=NodePort --port=80 --target-port=80 --name=<service name>` | Create a NodePort Service from a Deployment |

## Deployment Management

A `Deployment` manages applications that need to run continuously, such as web services, API services, and background services. It maintains the configured number of Pod replicas through a ReplicaSet and supports rolling updates, automatic failure recovery, and replica scaling.

Typical use cases:

- Long-running stateless services
- Applications that need several Pod replicas
- Applications that need rolling updates or fast rollbacks

| Command | Purpose |
|---|---|
| `kubectl get deploy` | Show the Deployments in the current namespace |
| `kubectl get deploy -n <namespace>` | Show the Deployments in the specified namespace |
| `kubectl apply -f <YAML file>` | Create or update resources from a YAML file |
| `kubectl delete -f <YAML file>` | Delete resources defined in a YAML file |
| `kubectl scale deploy <deployment name> --replicas=<count>` | Adjust the number of Pod replicas of a Deployment |
| `kubectl rollout status deploy/<deployment name>` | Show the update status of a Deployment |
| `kubectl rollout restart deploy/<deployment name>` | Restart the Pods managed by a Deployment |

## Job Management

A `Job` runs one-off or finite tasks, such as data processing, initialization, and batch jobs. A Job creates Pods and keeps retrying until the task completes successfully or the configured retry count is reached.

Typical use cases:

- One-off data processing
- Database initialization or migration
- Batch file processing
- Cluster or application initialization

| Command | Purpose |
|---|---|
| `kubectl create job <job name> --image=<image name>` | Create a one-off Job |
| `kubectl get jobs` | Show the Jobs in the current namespace |
| `kubectl get jobs -A` | Show the Jobs in all namespaces |
| `kubectl describe job <job name>` | Show Job status, Pods, and events |
| `kubectl wait --for=condition=complete job/<job name> --timeout=180s` | Wait for the Job to complete |
| `kubectl logs job/<job name>` | Show the logs of the Pod created by the Job |
| `kubectl delete job <job name>` | Delete the Job and its Pods |
| `kubectl apply -f <job YAML file>` | Create or update a Job from a YAML file |

## CronJob Management

A `CronJob` creates Jobs periodically on a schedule, for example for backups, log cleanup, and recurring data synchronization. Each run creates a Job, and the Job creates and runs the Pod.

Typical use cases:

- Scheduled backups
- Cleaning up temporary files or logs on a schedule
- Recurring data synchronization
- Generating reports on a schedule

A `CronJob` sets its execution time with a standard Cron expression; for example, `*/5 * * * *` means "run every 5 minutes".

| Command | Purpose |
|---|---|
| `kubectl create cronjob <cronjob name> --image=<image name> --schedule="*/5 * * * *"` | Create a CronJob that runs on a schedule |
| `kubectl get cronjobs` | Show the CronJobs in the current namespace |
| `kubectl get cronjobs -A` | Show the CronJobs in all namespaces |
| `kubectl describe cronjob <cronjob name>` | Show the schedule, status, and events of a CronJob |
| `kubectl get jobs --sort-by=.metadata.creationTimestamp` | Show the Jobs created by a CronJob |
| `kubectl get pods --sort-by=.metadata.creationTimestamp` | Show the Pods created by a CronJob |
| `kubectl patch cronjob <cronjob name> -p '{"spec":{"suspend":true}}'` | Suspend further scheduling of the CronJob |
| `kubectl patch cronjob <cronjob name> -p '{"spec":{"suspend":false}}'` | Resume the schedule of the CronJob |
| `kubectl delete cronjob <cronjob name>` | Delete the CronJob and its future runs |
| `kubectl apply -f <cronjob YAML file>` | Create or update a CronJob from a YAML file |

## Namespaces and Configuration

A namespace divides a K3s cluster into isolated resource scopes. Create separate namespaces for projects, teams, or environments, for example `dev`, `test`, and `prod`, to avoid name conflicts between applications and to simplify permission and resource management.

ConfigMaps and Secrets store the configuration an application needs at runtime:

- **ConfigMap**: stores ordinary configuration such as environment variables, configuration files, and service addresses.
- **Secret**: stores sensitive information such as passwords, tokens, and certificates. A Secret is not a complete encryption solution, so restrict access and avoid committing sensitive content to a code repository.

When creating an application, specify the namespace with `metadata.namespace` in the manifest. When running commands, specify the target namespace with `-n <namespace>`. Without a namespace, a command applies to the current namespace, usually `default`.

| Command | Purpose |
|---|---|
| `kubectl get ns` | Show all namespaces |
| `kubectl create ns <namespace name>` | Create a namespace |
| `kubectl get configmap` | Show the ConfigMaps in the current namespace |
| `kubectl get secret` | Show the Secrets in the current namespace |
| `kubectl get events -A --sort-by=.lastTimestamp` | Show the events of all namespaces in chronological order |

## Resources and Troubleshooting

| Command | Purpose |
|---|---|
| `kubectl top nodes` | Show the CPU and memory usage of the nodes |
| `kubectl top pods -A` | Show the CPU and memory usage of all Pods |
| `kubectl get pods -A -o wide` | Show the status, IP, and node of all Pods |
| `kubectl describe pod <pod name> -n <namespace>` | Show Pod details and abnormal events |
