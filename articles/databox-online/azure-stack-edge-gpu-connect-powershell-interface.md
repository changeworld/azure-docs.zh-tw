---
title: 透過 Windows PowerShell 介面，連接到 Microsoft Azure Stack Edge 裝置並加以管理 |Microsoft Docs
description: 說明如何透過 Windows PowerShell 介面連接到 Azure Stack Edge，然後再加以管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 85e95dc4138fd638c8db9f5c98a7064153c7ef17
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181641"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>透過 Windows PowerShell 管理 Azure Stack Edge GPU 裝置

Azure Stack Edge 解決方案可讓您處理資料，並透過網路將資料傳送至 Azure。 本文說明 Azure Stack Edge 裝置的一些設定和管理工作。 您可以使用 Azure 入口網站、本機 web UI 或 Windows PowerShell 介面來管理您的裝置。

本文著重于如何連接至裝置的 PowerShell 介面，以及您可以使用此介面進行的工作。 


## <a name="connect-to-the-powershell-interface"></a>連接到 PowerShell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

您也可以上傳 IoT Edge 憑證，以在您的 IoT Edge 裝置與其可能連線的下游裝置之間啟用安全連線。 您需要安裝三個 IoT Edge 憑證 (*pem* 格式) ：

- 根 CA 憑證或擁有者 CA
- 裝置 CA 憑證
- 裝置金鑰憑證

下列範例顯示如何使用此 Cmdlet 來安裝 IoT Edge 憑證：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
當您執行這個 Cmdlet 時，系統會提示您提供網路共用的密碼。

如需憑證的詳細資訊，請移至 [Azure IoT Edge 憑證](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) 或 [在閘道上安裝憑證](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)。

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

Nvidia Gpu 上的多進程服務 (MP) 提供一種機制，可讓多個作業共用 Gpu，其中每個作業都會配置一些 GPU 資源的百分比。 若要在您的 Azure Stack Edge 裝置上啟用 MP，請遵循下列步驟：

1. 開始之前，請確定： 

    1. 您已使用 Azure 中的 Azure Stack Edge/資料箱閘道資源設定並 [啟用您的 Azure Stack Edge 裝置](azure-stack-edge-gpu-deploy-activate.md) 。
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



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>與 IoT Edge 相關的 Debug Kubernetes 問題

建立 Kubernetes 叢集時， `aseuser` 也會建立與系統命名空間相關聯的預設使用者 `iotedge` 。 若要對 IoT Edge 的任何相關問題進行偵測，您可以使用此使用者和系統命名空間。  

### <a name="create-config-file-for-system-namespace"></a>建立 system 命名空間的設定檔

若要進行疑難排解，請先建立 `config` `iotedge` 與命名空間對應的檔案 `aseuser` 。

執行 `Get-HcsKubernetesUserConfig -AseUser` 命令，並將輸出儲存為檔案 `config` (沒有副檔名) 。 將檔案儲存在 `.kube` 本機電腦上的使用者設定檔資料夾中。

以下是命令的範例輸出 `Get-HcsKubernetesUserConfig` 。

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

在已設定計算角色的 Azure Stack Edge 裝置上，所有 `kubectl` 命令都可以用來監視或疑難排解模組。 若要查看可用的命令清單，請 `kubectl --help` 從命令視窗執行。

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


### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>取得 Kubernetes 叢集外部公開的服務或模組 IP

若要取得負載平衡服務的 IP 或 Kubernetes 外部公開的模組，請執行下列命令：

`kubectl get svc -n iotedge`

以下是在 Kubernetes 叢集外部公開的所有服務或模組的輸出範例。 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
[外部 IP] 資料行中的 IP 位址會對應至服務或模組的外部端點。 您也可以 [在 Kubernetes 儀表板中取得外部 IP](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)。


### <a name="to-check-if-module-deployed-successfully"></a>若要檢查是否已成功部署模組

計算模組是已實行商務邏輯的容器。 Kubernetes pod 可以有多個執行中的容器。 若要檢查是否已成功部署計算模組，請執行 `get pods` 命令，並檢查對應到計算模組) 的容器 (是否正在執行。

若要取得在特定命名空間中執行的所有 pod 清單，請執行下列命令：

`get pods -n <namespace>`

以下是在命名空間中執行的所有 pod 的範例輸出 `iotedge` 。

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

狀態 **狀態** 指出命名空間中的所有 pod 都在執行中，且 [ **就緒** ] 表示 pod 中部署的容器數目。 在上述範例中，所有 pod 都會執行，而且每個 pod 中部署的所有模組都會執行。 

如需指定命名空間的特定 pod 輸出更詳細的輸出，您可以執行下列命令：

`kubectl describe pod <pod name> -n <namespace>` 

範例輸出如下所示。

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
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


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>取得容器記錄

若要取得模組的記錄檔，請執行下列命令：

`kubectl logs <pod_name> -n <namespace> --all-containers` 

因為 `all-containers` 旗標會傾印所有容器的所有記錄，所以查看最近錯誤的好方法是使用選項 `--tail 10` 。

以下是範例輸出。 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
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

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>結束遠端會話

若要結束遠端 PowerShell 會話，請關閉 PowerShell 視窗。

## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署 [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) 。
