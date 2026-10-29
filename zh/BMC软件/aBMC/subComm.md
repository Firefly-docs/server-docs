# 子板通信管理

## 简介

子板通信管理（Sub Communication Manager）是 aBMC 面向阵列式服务器提供的和各子板之间建立以及维护通信链路的基础功能。通过该功能可以自定义 BMC 和子板的基础通信方式，aBMC 将通过指定的通信方式来建立和子板的通信链路，链路的可用性决定 **设备状态** 以及其他 aBMC 提供的管理功能的可用性。

<Callout title="版本支持" type="info">
    本功能自 v3.2.0 版本后开始支持。
</Callout>

## 开发愿景

1. 让用户能直观看到各子板与 BMC 之间采用的通信方式。
2. 让用户能自由选择各子板与 BMC 之间的通信方式。

# 功能使用

## 查看和配置通信方式

<CodeBlockTabs defaultValue="WEB">
    <CodeBlockTabsList>
        <CodeBlockTabsTrigger value="WEB">WEB</CodeBlockTabsTrigger>
        <CodeBlockTabsTrigger value="API">API</CodeBlockTabsTrigger>
    </CodeBlockTabsList>

    <CodeBlockTab value="WEB">
        ### 打开通信方式配置窗口 [step]

        1. 在左侧主导航栏中选择 **Devices**。
        2. 在二级导航栏中选择 **General**。
        3. 在设备列表中定位目标子板，点击 **⚙️** 图标。
        4. 选择 **Communication Method Configuration**。

        ![Open Communication configuration](../../../aBMC_img/v3.0/subComm_img/01-open-comm-config-en-v1.png)

        ### 查看通信方式列表 [step]

        | Field | 说明 |
        | --- | --- |
        | Name | 该通信方式的名称，default 代表出厂默认的通信方式 |
        | Type | 该通信方式的类型，目前有 USB ADB 和 SSH，新增时仅支持 SSH |
        | Connection Address | 该通信方式的连接地址 |
        | Username | 该通信方式的连接用户 |
        | Test Status | 该通信方式的测试结果 |
        | Enabled | 该通信方式当前是否被启用 |
        | Actions | 操作，目前仅支持删除操作 |
        
        ![Show Communication configuration](../../../aBMC_img/v3.0/subComm_img/02-show-comm-config-en-v1.png)

        ### 新增通信方式 [step]

        点击上一步示例图中的 **Add Communication Method**。

        1. 填写通信方式名称 Name。
        2. 选择通信方式类型 Type，目前仅支持 SSH。
        3. 填写 SSH 连接地址。
        4. 选择 SSH 认证方式，目前支持密码认证以及秘钥认证。选择秘钥认证时，需自行生成密钥对，将公钥存储到子板中，并在本窗口中上传私钥。
        5. 填写用户名。
        6. 填写密码或私钥。
        7. 点击 **Confirm** 确认新增。

        ![Add Communication configuration](../../../aBMC_img/v3.0/subComm_img/03-add-comm-config-en-v1.png)

        ### 测试通信方式 [step]

        新增了通信方式后，可以测试是否可用：

        1. 点击 **Test Communication**。
        2. 测试结果会呈现在 **Test Status** 这一列中。

        ![Test Communication configuration](../../../aBMC_img/v3.0/subComm_img/04-test-comm-config-en-v1.png)

        ### 启用通信方式 [step]

        新增的通信方式测试没问题后，可以选择启用该通信方式：

        1. 点击 **Enabled** 列对应的按钮。
        2. 切换通信方式后，对应设备会短暂离线，直到连接成功。

        ![Enable Communication configuration](../../../aBMC_img/v3.0/subComm_img/05-enable-comm-config-en-v1.png)

        ### 删除通信方式 [step]

        1. 点击 **Actions** 列对应的 remove 按钮。
        2. 在弹出的弹窗中选择 **Confirm** 确认删除。

        ![Delete Communication configuration](../../../aBMC_img/v3.0/subComm_img/06-delete-comm-config-en-v1.png)
    </CodeBlockTab>

    <CodeBlockTab value="API">
        | 操作 | 方法 | URI |
        | --- | --- | --- |
        | 查看通信配置集合 | GET | `/redfish/v1/Oem/Firefly/{ComputerSystemId}/CommConfigs` |
        | 新增通信配置 | POST | `/redfish/v1/Oem/Firefly/{ComputerSystemId}/CommConfigs/Actions/Create` |
        | 测试通信配置 | POST | `/redfish/v1/Oem/Firefly/{ComputerSystemId}/CommConfigs/Actions/Test` |
        | 启用通信配置 | POST | `/redfish/v1/Oem/Firefly/{ComputerSystemId}/CommConfigs/Actions/Enable` |
        | 删除通信配置 | POST | `/redfish/v1/Oem/Firefly/{ComputerSystemId}/CommConfigs/Actions/Delete` |

        <Callout title="提示" type="info">
            关于接口认证、请求参数、返回字段和错误码的详细说明，请查看 Redfish API 文档。
        </Callout>
    </CodeBlockTab>
</CodeBlockTabs>

## 常见问题 FAQ

### 1. 某个通信方式的删除按钮置灰，无法删除

名称为 default 的通信方式是出厂默认的通信方式，可以不使用，但不支持删除；当前正在使用的通信方式，需要切换至其他通信方式后再进行删除。

### 2. 切换通信方式后，设备短暂离线，随后恢复正常

设备状态由通信链路的状态决定，切换通信方式后，原有的通信链路自动断开，设备就会离线，随后建立新的通信链路，建立成功后恢复正常。
