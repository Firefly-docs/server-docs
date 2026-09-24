# K3s 卸载

本章介绍 Server 与 Agent 的卸载，对应[K3s部署](install.md)中的部署方式：

* **本地部署**：表示 K3s 以 systemd 服务方式运行；在线部署与离线部署都属于本地部署，卸载方式相同。
* **Docker 部署**：K3s 以 Docker 容器方式运行，删除容器后还需要清理宿主机上的残留数据。

## Agent 卸载

<CodeBlockTabs defaultValue="本地部署">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="本地部署">本地部署</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker部署">Docker部署</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="本地部署">

    ### 执行卸载脚本 [step]

    ```bash
    sudo K3S_DATA_DIR=/userdata/k3s /usr/local/bin/k3s-agent-uninstall.sh
    ```

    ### 在 Server 上删除节点对象 [step]

    ```bash
    sudo k3s kubectl get nodes
    sudo k3s kubectl delete node sub11
    ```

  </CodeBlockTab>
  <CodeBlockTab value="Docker部署">

    ### 删除容器 [step]

    ```bash
    sudo docker rm -f k3s-agent
    ```

    ### 清理网络与规则 [step]

    ```bash
    sudo ip link delete cni0
    sudo ip link delete flannel.1
    sudo iptables-save | grep -v KUBE- | grep -v CNI- | grep -iv flannel | sudo iptables-restore

    ```

    ### 清除数据 [step]

    ```bash
    sudo rm -rf /userdata/k3s /etc/rancher/k3s /etc/rancher/node
    ```

    ### 在 Server 上删除节点对象 [step]

    ```bash
    sudo k3s kubectl delete node sub11
    ```

  </CodeBlockTab>
</CodeBlockTabs>


## Server 卸载

<CodeBlockTabs defaultValue="本地部署">
  <CodeBlockTabsList>
    <CodeBlockTabsTrigger value="本地部署">本地部署</CodeBlockTabsTrigger>
    <CodeBlockTabsTrigger value="Docker部署">Docker部署</CodeBlockTabsTrigger>
  </CodeBlockTabsList>
  <CodeBlockTab value="本地部署">

    ### 停止工作负载 [step]

    ```bash
    sudo K3S_DATA_DIR=/userdata/k3s /usr/local/bin/k3s-killall.sh
    ```

    ### 删除衍生网卡 [step]

    ```bash
    ip -o link show | grep -E 'cni0|flannel'
    ```

    ### 执行卸载脚本 [step]

    ```bash
    sudo K3S_DATA_DIR=/userdata/k3s /usr/local/bin/k3s-uninstall.sh
    ```

  </CodeBlockTab>
  <CodeBlockTab value="Docker部署">

    ### 删除容器 [step]

    ```bash
    sudo docker rm -f k3s-server
    ```

    ### 清理衍生网卡 [step]

    ```bash
    sudo ip link delete cni0
    sudo ip link delete flannel.1
    ```

    ## 清除规则

    ```shell
    sudo iptables-save | grep -v KUBE- | grep -v CNI- | grep -iv flannel | sudo iptables-restore
    ```

    ### 删除数据 [step]

    ```bash
    sudo rm -rf /userdata/k3s /etc/rancher/k3s /etc/rancher/node
    ```

  </CodeBlockTab>
</CodeBlockTabs>