# AIC Deployment

This chapter builds on the K3s cluster deployed online in [K3s Deployment](install.md) and describes how to deploy AIC (Android in Container) on K3s, so that Android instances run in containers and are managed from one place.

- **What AIC is**: AIC (Android in Container) runs Android inside a Linux container. On Linux you can start Android with Docker and run several Android instances at the same time.

- **Why K3s**: Managing AIC instances as Kubernetes `Deployment` resources gives you the scheduling, restart, and failure-recovery capabilities of K3s. On a multi-node cluster you can also pin instances to a specific node with `nodeSelector`.

- **Typical scenarios**: Managing AIC instances across multiple nodes with K3s increases the Android instance density, which suits cloud phone and cloud gaming workloads.

| Operation Location | Machine | Address | What happens here |
|---|---|---|---|
| K3s Server node | `bmc` | `172.16.100.176` | Run `kubectl` to create and inspect AIC resources |
| Agent node | `sub11` | `172.16.100.177` | Prepare the image and the container configuration; AIC instances run here |
| Client (your computer) | — | — | Connect to the instances with `adb` |


## Environment Preparation

### Prepare the Package [step]

After extracting the package there are two directories: `container/` holds the scripts, configuration, and image for the Android containers and must be placed under `/userdata/` on the Agent node; `host_fw/` holds the firmware flashed to the Agent node and is used in the next step.

```text
AIC/
├── container  ## container configuration
│   ├── aic.sh
│   ├── android_config
│   │   ├── README.txt
│   │   ├── container_0.conf
│   │   ├── container_1.conf
│   │   ├── container_2.conf
│   │   ├── container_3.conf
│   │   ├── container_4.conf
│   │   ├── container_5.conf
│   │   ├── container_6.conf
│   │   ├── container_7.conf
│   │   └── container_common.conf
│   ├── daemon.json
│   └── rk3588_docker-android12-userdebug-super.img-20250117.1531.tgz
└── host_fw
    └── ROC-RK3588S-PC_Ubuntu20.04-Minimal-r3104_v1.3.0c_241107.7z
```

Package [download link](https://pan.baidu.com/s/1sbZwOc4peZfn0HDX9O0lsg) (access code: `1234`)

### Flash the Agent Firmware [step]

The Agent node must be flashed with the customized firmware first, that is `ROC-RK3588S-PC_Ubuntu20.04-Minimal-…7z` from `host_fw/` in the package. For the steps, see [Firmware Upgrade](https://community.t-firefly.com/docs/server/bmc-software/aBMC/upgrade).

### Deploy the Agent [step]

Deploy the Server and the Agent online as described in [K3s Deployment](install.md), and continue with the steps below once the Agent has joined the cluster.

### Build the Image [step]

First copy the `container/` directory from the package to `/userdata/` on the Agent node (in this example `sub11`). The image only has to be built once: push it to the private registry afterwards, and the other nodes pull it from the registry when they are deployed, so there is no need to repeat the build. The `-c` option turns the firmware package (`*.tgz`, containing the Android partition images) into a Docker image; the process is logged to `build_image.log` in the same directory:

```bash
cd /userdata/container

sudo bash aic.sh -c /userdata/container/rk3588_docker-android12-userdebug-super.img-20250117.1531.tgz
```

After the image is built, tag it with the private registry address and push it (this example uses the registry `172.16.55.55:5000`):

```bash
sudo docker tag <local image> 172.16.55.55:5000/aic/rk3588-android12:<tag>

sudo docker push 172.16.55.55:5000/aic/rk3588-android12:<tag>
```


```text
The push refers to repository [172.16.55.55:5000/aic/rk3588-android12]
ANDROID12_RKR14-zzz01171531: digest: sha256:9f926da5... size: 529
PUSH-OK
```
<Callout>
 The registry is served over HTTP, so `/etc/docker/daemon.json` on the node must list the address under `insecure-registries`; otherwise both pushing and pulling fail:

 ```json
 {
  "insecure-registries": ["http://172.16.55.55:5000"]
}
 ```
</Callout>

## Application Manifest

Save the manifest below as `aic.yml` on the Server node `bmc`; the `kubectl` commands in the "Deployment" section also run on that node. The manifest is a single file that contains the `ConfigMap`, the PVCs, the Deployments, and the Services. The example enables 2 instances and the `ConfigMap` ships 8 sets of configuration, which you can use by number when you need more instances.

```yml
# AIC Android container K3s deployment manifest (2-instance version, with ConfigMap configuration, single file)
#
# This file = ConfigMap (container_0~7.conf + container_common.conf) + PVC + Deployment + Service
# Configuration source: sub11:/userdata/container/android_config/*.conf
# Mapping: instance N -> PVC my-dataN + Service 110N->5555 + container_N.conf
# sub11 has 3.8G of memory, so at most 3 instances run at the same time; add nodes before adding more instances
# Usage: sudo k3s kubectl apply -f aic.yml
apiVersion: v1
data:
  container_0.conf: |
    # config for container 0
    container_id=0
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-1
  container_1.conf: |
    # config for container 1
    container_id=1
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-2
  container_2.conf: |
    # config for container 2
    container_id=2
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-3
  container_3.conf: |
    # config for container 3
    container_id=3
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-4
  container_4.conf: |
    # config for container 4
    container_id=4
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-5
  container_5.conf: |
    # config for container 5
    container_id=5
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-6
  container_6.conf: |
    # config for container 6
    container_id=6
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-7
  container_7.conf: |
    # config for container 7
    container_id=7
    ### display
    # Which screen to bind to; the default is VVOP:Virtual-1~Virtual-8
    # With a physical screen, use the device node of that screen
    # ls /sys/class/drm/card0 lists the supported devices
    primary_type=Virtual-8
  container_common.conf: |
    # default config for all container
    # This configuration applies to every container.
    # To change one container, edit that container's own configuration file.
    # Do not edit this file unless you want the change to apply to all containers.
    # Must be enabled; it is used by init.redroid.rc
    enable.container.config=true
    # Container id; it must be overridden with the matching id in each container's own configuration file
    container_id=0
    ### network
    # Network type: docker0 / macvlan_static / macvlan_dhcp / host
    network.type=docker0
    # DNS settings; the default is 8.8.8.8 when nothing is configured
    # dns only takes effect for docker0 / macvlan_static
    # dns is ignored for macvlan_dhcp / host
    net_dns.num=2
    net_dns1=114.114.114.114
    net_dns2=8.8.8.8
    ### HWC
    # Default display; it must be overridden in each container's own configuration
    # For multiple screens, configure primary_type=DSI-1,HDMI-A-1
    primary_type=DSI-1
    # Default resolution
    default.resolution=1080x1920@60
    ### audio
    # Disable audio output to the host
    disable.audio.output=true
    ### input event
    # Disable input events;
    # if the container needs mouse and keyboard support, keep input events enabled
    # to keep them enabled, set this to false
    disable.input.event=true
    ### ueventd
    # ueventd cold boot is disabled by default
    # If you use peripherals such as USB drives, enabling cold boot is recommended
    # to enable cold boot, set this to false
    disable.ueventd.cold_boot=true
    ### usb
    # usb.configfs is not disabled by default;
    # if you do not use USB, you can disable it by setting this to true
    disable.usb.configfs=false
kind: ConfigMap
metadata:
  name: aic-android-config
  namespace: default
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-data0
spec:
  accessModes: ["ReadWriteOnce"]
  storageClassName: longhorn
  resources:
    requests:
      storage: 5Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aic-android-0
  labels:
    app: aic-android
    instance: "0"
spec:
  replicas: 1
  strategy:
    type: Recreate # Android takes a long time to boot; avoid old and new instances coexisting during an update
  selector:
    matchLabels:
      app: aic-android
      instance: "0"
  template:
    metadata:
      labels:
        app: aic-android
        instance: "0"
    spec:
      nodeSelector:
        kubernetes.io/hostname: sub11
      hostname: android-0
      terminationGracePeriodSeconds: 60
      containers:
        - name: android
          image: 172.16.55.55:5000/aic/rk3588-android12:ANDROID12_RKR14-zzz01171531
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
          resources:
            requests:
              cpu: 500m
              memory: 1Gi
          ports:
            - name: adb
              containerPort: 5555
          volumeMounts:
            - name: data
              mountPath: /data
            - name: config
              mountPath: /vendor/etc/container/container_common.conf
              subPath: container_common.conf
            - name: config
              mountPath: /vendor/etc/container/container.conf
              subPath: container_0.conf # Different for each instance
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: my-data0
        - name: config
          configMap:
            name: aic-android-config # All 8 instances plus the common configuration live here
---
# One LoadBalancer Service per instance (k3s ServiceLB); every node binds the port, adb connect <any node IP>:1100
apiVersion: v1
kind: Service
metadata:
  name: aic-android-0
  labels:
    app: aic-android
    instance: "0"
spec:
  type: LoadBalancer
  selector:
    app: aic-android
    instance: "0"
  ports:
    - name: adb
      port: 1100
      targetPort: 5555
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-data1
spec:
  accessModes: ["ReadWriteOnce"]
  storageClassName: longhorn
  resources:
    requests:
      storage: 5Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aic-android-1
  labels:
    app: aic-android
    instance: "1"
spec:
  replicas: 1
  strategy:
    type: Recreate # Android takes a long time to boot; avoid old and new instances coexisting during an update
  selector:
    matchLabels:
      app: aic-android
      instance: "1"
  template:
    metadata:
      labels:
        app: aic-android
        instance: "1"
    spec:
      nodeSelector:
        kubernetes.io/hostname: sub11
      hostname: android-1
      terminationGracePeriodSeconds: 60
      containers:
        - name: android
          image: 172.16.55.55:5000/aic/rk3588-android12:ANDROID12_RKR14-zzz01171531
          imagePullPolicy: IfNotPresent
          securityContext:
            privileged: true
          resources:
            requests:
              cpu: 500m
              memory: 1Gi
          ports:
            - name: adb
              containerPort: 5555
          volumeMounts:
            - name: data
              mountPath: /data
            - name: config
              mountPath: /vendor/etc/container/container_common.conf
              subPath: container_common.conf
            - name: config
              mountPath: /vendor/etc/container/container.conf
              subPath: container_1.conf # Different for each instance
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: my-data1
        - name: config
          configMap:
            name: aic-android-config # All 8 instances plus the common configuration live here
---
# One LoadBalancer Service per instance (k3s ServiceLB); every node binds the port, adb connect <any node IP>:1101
apiVersion: v1
kind: Service
metadata:
  name: aic-android-1
  labels:
    app: aic-android
    instance: "1"
spec:
  type: LoadBalancer
  selector:
    app: aic-android
    instance: "1"
  ports:
    - name: adb
      port: 1101
      targetPort: 5555
```

## Deployment

### Apply the Manifest [step]

Apply the manifest on the Server node `bmc`:

```bash
sudo k3s kubectl apply -f aic.yml
```

### Verify the Instance Status [step]

Check the status of the instances, the volumes, and the Services:

```bash
sudo k3s kubectl get pods -o wide

sudo k3s kubectl get pvc

sudo k3s kubectl get svc
```

Both instances run on `sub11`, and ServiceLB, which is built into K3s, binds `1100` / `1101` on every node:

```text
NAME                             READY   STATUS    IP           NODE
aic-android-0-5b89c454d8-wl9kp   1/1     Running   10.42.1.80   sub11
aic-android-1-6c794d5f4-5gsqx    1/1     Running   10.42.1.81   sub11

NAME            TYPE           CLUSTER-IP      EXTERNAL-IP                     PORT(S)
aic-android-0   LoadBalancer   10.43.111.178   172.16.100.176,172.16.100.177   1100:30536/TCP
aic-android-1   LoadBalancer   10.43.182.80    172.16.100.176,172.16.100.177   1101:30242/TCP
```


<Callout>
Android instances start slowly (the image is 1.67 GB, and Android still has to boot after that), so the Pod stays in `Pending` or `ContainerCreating` while the image is pulled for the first time.
</Callout>


## Accessing the Instances

Each instance has its own `LoadBalancer` Service, and `110N` forwards to port `5555` of the container (the adb port). ServiceLB binds that port on every node, so this example connects through the Server node `bmc` (`172.16.100.176`) for all instances; any machine with `adb` installed can run the same commands:

```bash
adb connect 172.16.100.176:1100   # instance 0

adb connect 172.16.100.176:1101   # instance 1
```

```text
connected to 172.16.100.176:1100
connected to 172.16.100.176:1101
```

Once connected, use `-s` to pick the instance you want to work with:

```bash

adb devices

adb -s 172.16.100.176:1100 shell

```

## Manifest Description

| Resource | Purpose |
|---|---|
| `ConfigMap aic-android-config` | Holds each instance's `container_N.conf` and the shared `container_common.conf`, mounted into each container through `subPath` |
| `PersistentVolumeClaim my-dataN` | One 5Gi volume per instance (`storageClassName: longhorn`), mounted at `/data` in the container |
| `Deployment aic-android-N` | One replica per instance, pinned to a node with `nodeSelector` and running in privileged mode; the `Recreate` strategy avoids old and new instances coexisting during an update |
| `Service aic-android-N` | Of type `LoadBalancer`, mapping `110N → 5555` for `adb connect` |

A few notes:

* Instance N uses `container_N.conf`, while `container_common.conf` applies to every container;
* `container_id` in `container_common.conf` is overridden by each instance's own configuration, so check the scope before editing the shared file;
* `nodeSelector` pins an instance to a specific node, and the instance does not move when that node becomes unavailable: the Pod stays in `Pending` as long as the target node is `NotReady`;

## Instance Data Storage Optimization

### Frequent App Installs and eMMC Wear [step]

Every Android instance in AIC stores its `/data` on a Longhorn volume. In the current configuration each instance uses a 5Gi Longhorn volume, and the volume replicas are stored in `/var/lib/longhorn` on the node, which is located on the eMMC.

Installing, uninstalling, and updating Android apps, as well as running them, all write to the disk. When instances are created and destroyed and apps are installed and uninstalled frequently, the sustained write load may increase the wear of the eMMC.

The system partition and the containers themselves still generate writes that cannot be avoided completely. To reduce the writes that instance data causes on the eMMC, the Longhorn storage directory can be moved to storage devices better suited to frequent reads and writes, such as SATA or NVMe.

The general idea is: **the Android instances keep running on the sub-board nodes, but their `/data` volumes are stored on a designated SATA or NVMe disk.**

There are two common ways to deploy this.

### Option 1: Use SATA Storage on the Server Node [step]

If there are many sub-boards but the storage performance requirement is not high, connect a large SATA disk to `bmc` and let `bmc` provide Longhorn storage for the whole cluster.

The data path can be seen as:

`Android instances on sub-board nodes -> Longhorn -> network -> SATA disk on bmc`

This approach centralizes storage capacity and makes later expansion easier, which suits a small number of instances or a cost-sensitive deployment.

The configuration steps are:

1. Mount the SATA disk on `bmc` and create the Longhorn data directory, for example `/sata/longhorn`.
2. Configure that directory as a Longhorn disk and enable `allowScheduling`.
3. Add a storage pool tag to the disk, for example `pool-sata`.
4. Create a `StorageClass` that selects `pool-sata` through `nodeSelector`.
5. In the AIC manifest, set the `storageClassName` of the instance volumes `my-dataN` to that `StorageClass`.

Instance volumes created with that `StorageClass` are then provisioned on the SATA storage of `bmc` instead of the eMMC of the sub-board node.

### Option 2: Share One NVMe Between Multiple Sub-Board Nodes [step]

If there are many sub-boards, or you want to reduce the impact of a single storage node failure on all instances, group the nodes and give each group its own NVMe.

For example, every 3 sub-board nodes form a storage group; one node in the group installs an NVMe, which is configured as the Longhorn storage pool for that group.

The data path can be seen as:

`Android instances on sub-board nodes -> Longhorn -> network -> NVMe on a node of the same group`

For example:

- `sub1`, `sub2`, and `sub3` use `pool-nvme-1`
- `sub4`, `sub5`, and `sub6` use `pool-nvme-2`

The configuration steps are almost the same as in the SATA option:

1. Mount the NVMe on the storage node and create the Longhorn data directory, for example `/nvme/longhorn`.
2. Configure that directory as a Longhorn disk and enable `allowScheduling`.
3. Add the matching storage pool tag to the disk, for example `pool-nvme-1`.
4. Create the matching `StorageClass` for that storage pool.
5. Configure the AIC instances on that group of nodes to use the matching `StorageClass`.

This spreads the storage load across several nodes and avoids concentrating the data of all instances on `bmc`.

### Comparison of the Two Options [step]

| Item | SATA on the Server Node | One NVMe per 3 Nodes |
|---|---|---|
| Storage location | Centralized on `bmc` | Spread across the storage groups |
| Storage capacity | Can use a large SATA disk | Limited by the NVMe of each group |
| Storage cost | Lower | Higher |
| Network access | Instances reach the storage on `bmc` over the network | Instances reach the NVMe in their group over the network |
| Storage performance | Limited by the network and the SATA disk | Usually higher storage performance |
| Failure scope | A storage failure on `bmc` may affect all instances | A single storage node failure mainly affects its group |
| Management | Centralized storage, simple to manage | Distributed storage, managed per group |
| Typical use | Fewer instances, capacity and cost matter | More instances, storage load must be spread |

Either way, **the node that runs the AIC instances does not change; only the `StorageClass` used by the instance PVCs has to be adjusted**. Longhorn places the instance volumes on the designated SATA or NVMe storage according to the storage pool selection rules of the `StorageClass`.

For the exact disk configuration, storage directory, and `StorageClass` creation steps, see "Configuring the Storage Directory" and "Usage Example" in [Longhorn](longhorn.md).
