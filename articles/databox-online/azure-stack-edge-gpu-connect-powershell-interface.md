---
title: 透過 Windows PowerShell 介面連線至 Microsoft Azure Stack Edge Pro GPU 裝置並進行管理 |Microsoft Docs
description: 說明如何透過 Windows PowerShell 介面連接到 Azure Stack Edge Pro GPU，然後加以管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: ba3005b1ec36e4b2406084368a3aabd778c17716
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449437"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>透過 Windows PowerShell 管理 Azure Stack Edge Pro GPU 裝置

Azure Stack Edge Pro 解決方案可讓您處理資料，並透過網路傳送至 Azure。 本文說明 Azure Stack Edge Pro 裝置的一些設定和管理工作。 您可以使用 Azure 入口網站、本機 web UI 或 Windows PowerShell 介面來管理您的裝置。

本文著重于如何連接至裝置的 PowerShell 介面，以及您可以使用此介面進行的工作。 


## <a name="connect-to-the-powershell-interface"></a>連線至 Powershell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

<!--## Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

You can also upload IoT Edge certificates to enable a secure connection between your IoT Edge device and the downstream devices that may connect to it. There are three IoT Edge certificates (*.pem* format) that you need to install:

- Root CA certificate or the owner CA
- Device CA certificate
- Device key certificate

The following example shows the usage of this cmdlet to install IoT Edge certificates:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
When you run this cmdlet, you will be prompted to provide the password for the network share.

For more information on certificates, go to [Azure IoT Edge certificates](../iot-edge/iot-edge-certs.md) or [Install certificates on a gateway](../iot-edge/how-to-create-transparent-gateway.md).-->

## <a name="view-device-information"></a>查看裝置資訊
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>查看 GPU 驅動程式資訊

如果您的裝置上已設定計算角色，您也可以透過 PowerShell 介面取得 GPU 驅動程式資訊。 

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用 `Get-HcsGpuNvidiaSmi` 取得裝置的 GPU 驅動程式資訊。

    下列範例顯示此 Cmdlet 的使用方式：

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    記下此 Cmdlet 的範例輸出中的驅動程式資訊。

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>啟用多進程服務 (MP) 

Nvidia Gpu 上的多進程服務 (MP) 提供一種機制，可讓多個作業共用 Gpu，其中每個作業都會配置一些 GPU 資源的百分比。 MP 是您 Azure Stack Edge Pro GPU 裝置上的預覽功能。 若要在您的裝置上啟用 MP，請遵循下列步驟：

1. 開始之前，請確定： 

    1. 您已使用 Azure 中的 Azure Stack Edge Pro/Data Box Gateway 資源設定並 [啟用您的 Azure Stack Edge pro 裝置](azure-stack-edge-gpu-deploy-activate.md) 。
    1. 您已 [在 Azure 入口網站中設定此裝置上的計算](azure-stack-edge-deploy-configure-compute.md#configure-compute)。
    
1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
1. 使用下列命令，在您的裝置上啟用 MP。

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>重設裝置

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>取得計算記錄

如果您的裝置上已設定計算角色，您也可以透過 PowerShell 介面取得計算記錄。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用 `Get-AzureDataBoxEdgeComputeRoleLogs` 來取得裝置的計算記錄。

    下列範例顯示此 Cmdlet 的使用方式：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    以下是用於 Cmdlet 的參數描述：
    - `Path`：提供您要在其中建立計算記錄封裝之共用的網路路徑。
    - `Credential`：提供網路共用的使用者名稱。 當您執行這個 Cmdlet 時，您將需要提供共用密碼。
    - `FullLogCollection`：此參數可確保記錄封裝將包含所有計算記錄檔。 根據預設，記錄封裝只會包含記錄的子集。


## <a name="change-kubernetes-pod-and-service-subnets"></a>變更 Kubernetes pod 和服務子網

根據預設，Azure Stack Edge 裝置上的 Kubernetes 會分別使用適用于 pod 和服務的子網 172.27.0.0/16 和 172.28.0.0/16。 如果您的網路已使用這些子網，您可以執行 `Set-HcsKubeClusterNetworkInfo` Cmdlet 來變更這些子網。

當您在此步驟中建立 Kubernetes 叢集之前，您會想要執行此設定，然後再從 Azure 入口網站設定計算。

1. [連接到裝置的 PowerShell 介面](#connect-to-the-powershell-interface)。
1. 從裝置的 PowerShell 介面，執行：

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    將取代為 <subnet details> 您要使用的子網範圍。 

1. 執行此命令之後，您可以使用 `Get-HcsKubeClusterNetworkInfo` 命令來確認 pod 和服務子網是否已變更。

以下是此命令的範例輸出。

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```


## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>與 IoT Edge 相關的 Debug Kubernetes 問題

<!--When the Kubernetes cluster is created, there are two system namespaces created: `iotedge` and `azure-arc`. --> 

<!--### Create config file for system namespace

To troubleshoot, first create the `config` file corresponding to the `iotedge` namespace with `aseuser`.

Run the `Get-HcsKubernetesUserConfig -AseUser` command and save the output as `config` file (no file extension). Save the file in the `.kube` folder of your user profile on the local machine.

Following is the sample output of the `Get-HcsKubernetesUserConfig` command.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```
-->

在已設定計算角色的 Azure Stack Edge Pro 裝置上，您可以使用兩組不同的命令針對裝置進行疑難排解或監視。

- 使用 `iotedge` 命令。 這些命令適用于您裝置的基本作業。
- 使用 `kubectl` 命令。 這些命令適用于您裝置的一組大量作業。

若要執行上述其中一組命令，您需要 [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。

### <a name="use-iotedge-commands"></a>使用 `iotedge` 命令

若要查看可用命令的清單，請 [連接到 PowerShell 介面](#connect-to-the-powershell-interface) 並使用函式 `iotedge` 。

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

下表提供下列命令的簡短描述 `iotedge` ：

|命令  |描述 |
|---------|---------|
|`list`     | 列出模組         |
|`logs`     | 提取模組的記錄        |
|`restart`     | 停止並重新啟動模組         |


若要列出在裝置上執行的所有模組，請使用 `iotedge list` 命令。

以下是此命令的範例輸出。 此命令會列出所有模組、相關聯的設定，以及與模組相關聯的外部 Ip。 例如，您可以存取中的 **web** 伺服器應用程式 `https://10.128.44.244` 。 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```


### <a name="use-kubectl-commands"></a>使用 kubectl 命令

在已設定計算角色的 Azure Stack Edge Pro 裝置上，所有 `kubectl` 命令都可以用來監視或疑難排解模組。 若要查看可用的命令清單，請 `kubectl --help` 從命令視窗執行。

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

如需完整的命令清單 `kubectl` ，請移至[ `kubectl` 速查表](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)。


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>取得 Kubernetes 叢集外部公開的服務或模組 IP

若要取得負載平衡服務的 IP 或 Kubernetes 外部公開的模組，請執行下列命令：

`kubectl get svc -n iotedge`

以下是在 Kubernetes 叢集外部公開的所有服務或模組的輸出範例。 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
[外部 IP] 資料行中的 IP 位址會對應至服務或模組的外部端點。 您也可以 [在 Kubernetes 儀表板中取得外部 IP](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)。


#### <a name="to-check-if-module-deployed-successfully"></a>若要檢查是否已成功部署模組

計算模組是已實行商務邏輯的容器。 Kubernetes pod 可以有多個執行中的容器。 

若要檢查是否已成功部署計算模組，請連接到裝置的 PowerShell 介面。
執行 `get pods` 命令，並檢查對應到計算模組) 的容器 (是否正在執行。

若要取得在特定命名空間中執行的所有 pod 清單，請執行下列命令：

`get pods -n <namespace>`

若要檢查透過 IoT Edge 部署的模組，請執行下列命令：

`get pods -n iotedge`

以下是在命名空間中執行的所有 pod 的範例輸出 `iotedge` 。

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

狀態 **狀態** 指出命名空間中的所有 pod 都在執行中，且 [ **就緒** ] 表示 pod 中部署的容器數目。 在上述範例中，所有 pod 都會執行，而且每個 pod 中部署的所有模組都會執行。 

若要檢查透過 Azure Arc 部署的模組，請執行下列命令：

`get pods -n azure-arc`

或者，您可以連線 [至 Kubernetes 儀表板，查看 IoT Edge 或 Azure Arc 部署](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status)。


如需指定命名空間的特定 pod 輸出更詳細的輸出，您可以執行下列命令：

`kubectl describe pod <pod name> -n <namespace>` 

範例輸出如下所示。

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>取得容器記錄

若要取得模組的記錄，請從裝置的 PowerShell 介面執行下列命令：

`kubectl logs <pod_name> -n <namespace> --all-containers` 

因為 `all-containers` 旗標會傾印所有容器的所有記錄，所以查看最近錯誤的好方法是使用選項 `--tail 10` 。

以下是範例輸出。 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

## <a name="connect-to-bmc"></a>連接至 BMC

基礎板管理控制器 (BMC) 可用來遠端監視和管理您的裝置。 本節說明可用來管理 BMC 設定的 Cmdlet。 在執行這些 Cmdlet 之前，請先 [連接到裝置的 PowerShell 介面](#connect-to-the-powershell-interface)。

- `Get-HcsNetBmcInterface`：使用這個 Cmdlet 取得 BMC 的網路設定屬性，例如，、、 `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` 、 `DhcpEnabled` 。 
    
    以下是範例輸出：
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`：您可以使用下列兩種方式來使用此 Cmdlet。

    - 使用 Cmdlet 來啟用或停用適用于 BMC 的 DHCP 設定，方法是使用適當的 `UseDhcp` 參數值。 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        以下是範例輸出： 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - 使用此 Cmdlet 來設定 BMC 的靜態設定。 您可以指定 `IPv4Address` 、和的值 `IPv4Gateway` `IPv4SubnetMask` 。 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        以下是範例輸出： 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`：使用這個 Cmdlet 來修改的 BMC 密碼 `EdgeUser` 。 使用者名稱- `EdgeUser` -區分大小寫。

    以下是範例輸出： 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>結束遠端會話

若要結束遠端 PowerShell 會話，請關閉 PowerShell 視窗。

## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署 [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)。