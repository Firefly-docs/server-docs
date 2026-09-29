# K3s Uninstallation

This chapter describes how to uninstall the Server and the Agent. The steps correspond to the deployment methods described in [K3s Deployment](install.md):

* **Local deployment**: K3s runs as a systemd service. Both the online and offline deployments are local deployments and are uninstalled in the same way.
* **Docker deployment**: K3s runs as a Docker container. After removing the container, also clean up the leftover data on the host.

## Agent Uninstallation

<CodeBlockTabs defaultValue="Local Deployment">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Local Deployment">Local Deployment</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker Deployment">Docker Deployment</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="Local Deployment">

    ### Run the Uninstall Script [step]

    ```bash
    sudo K3S_DATA_DIR=/userdata/k3s /usr/local/bin/k3s-agent-uninstall.sh
    ```

    ### Delete the Node Object on the Server [step]

    ```bash
    sudo k3s kubectl get nodes
    sudo k3s kubectl delete node sub11
    ```

  </CodeBlockTab>
  <CodeBlockTab value="Docker Deployment">

    ### Remove the Container [step]

    ```bash
    sudo docker rm -f k3s-agent
    ```

    ### Clean Up the Network and Rules [step]

    ```bash
    sudo ip link delete cni0
    sudo ip link delete flannel.1
    sudo iptables-save | grep -v KUBE- | grep -v CNI- | grep -iv flannel | sudo iptables-restore

    ```

    ### Remove the Data [step]

    ```bash
    sudo rm -rf /userdata/k3s /etc/rancher/k3s /etc/rancher/node
    ```

    ### Delete the Node Object on the Server [step]

    ```bash
    sudo k3s kubectl delete node sub11
    ```

  </CodeBlockTab>
</CodeBlockTabs>


## Server Uninstallation

<CodeBlockTabs defaultValue="Local Deployment">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="Local Deployment">Local Deployment</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker Deployment">Docker Deployment</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="Local Deployment">

    ### Stop the Workloads [step]

    ```bash
    sudo K3S_DATA_DIR=/userdata/k3s /usr/local/bin/k3s-killall.sh
    ```

    ### Remove the Derived Network Interfaces [step]

    ```bash
    ip -o link show | grep -E 'cni0|flannel'
    ```

    ### Run the Uninstall Script [step]

    ```bash
    sudo K3S_DATA_DIR=/userdata/k3s /usr/local/bin/k3s-uninstall.sh
    ```

  </CodeBlockTab>
  <CodeBlockTab value="Docker Deployment">

    ### Remove the Container [step]

    ```bash
    sudo docker rm -f k3s-server
    ```

    ### Remove the Derived Network Interfaces [step]

    ```bash
    sudo ip link delete cni0
    sudo ip link delete flannel.1
    ```

    ### Clean Up the Rules [step]

    ```shell
    sudo iptables-save | grep -v KUBE- | grep -v CNI- | grep -iv flannel | sudo iptables-restore
    ```

    ### Remove the Data [step]

    ```bash
    sudo rm -rf /userdata/k3s /etc/rancher/k3s /etc/rancher/node
    ```

  </CodeBlockTab>
</CodeBlockTabs>
