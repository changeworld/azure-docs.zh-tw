---
title: 使用 kubectl 在 Azure Stack Edge GPU 裝置上部署 Kubernetes 無狀態應用程式 |Microsoft Docs
description: 說明如何在 Microsoft Azure Stack Edge 裝置上，使用 kubectl 建立及管理 Kubernetes 無狀態應用程式部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 27502c58481444a9dc14120bf447d4614d051ccc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268854"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-gpu-device"></a>在 Azure Stack Edge GPU 裝置上透過 kubectl 部署 Kubernetes 無狀態應用程式

本文說明如何在現有的 Kubernetes 叢集上使用 kubectl 命令部署無狀態應用程式。 本文也會逐步引導您完成在無狀態應用程式中建立及設定 pod 的流程。

## <a name="prerequisites"></a>先決條件

您必須先確定下列事項，才能建立 Kubernetes 叢集並使用 `kubectl` 命令列工具：

- 您有1個節點 Azure Stack Edge 裝置的登入認證。

- Windows PowerShell 5.0 或更新版本已安裝在 Windows 用戶端系統上，以存取 Azure Stack Edge 裝置。 您也可以讓任何其他用戶端使用支援的作業系統。 本文說明使用 Windows 用戶端的程式。 若要下載 Windows PowerShell 的最新版本，請移至 [安裝 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。

- Azure Stack Edge 裝置上會啟用計算。 若要啟用計算，請移至裝置本機 UI 中的 [ **計算** ] 頁面。 然後選取您想要為計算啟用的網路介面。 選取 [啟用]。 在該網路介面上的裝置上建立虛擬交換器時，啟用計算結果。 如需詳細資訊，請參閱在 [您的 Azure Stack Edge 上啟用計算網路](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

- 您的 Azure Stack Edge 裝置有一台執行的 Kubernetes 叢集伺服器，其版本為1.9 或更新版本。 如需詳細資訊，請參閱 [在 Microsoft Azure Stack Edge 裝置上建立和管理 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集。

- 您已安裝 `kubectl` 。

## <a name="deploy-a-stateless-application"></a>部署無狀態應用程式

開始之前，您應該先：

1. 建立 Kubernetes 叢集。
2. 設定命名空間。
3. 將使用者與命名空間相關聯。
4. 將使用者設定儲存至 `C:\Users\<username>\.kube` 。
5. 已安裝 `kubectl` 。

現在您可以開始在 Azure Stack Edge 裝置上執行及管理無狀態應用程式部署。 在您開始使用之前 `kubectl` ，您必須先確認您有正確的版本 `kubectl` 。

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>確認您擁有正確的 kubectl 版本並設定設定

若要檢查的版本 `kubectl` ：

1. 確認的版本 `kubectl` 大於或等於1.9：

   ```powershell
   kubectl version
   ```
    
   以下是輸出的範例：
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   在此情況下，kubectl 的用戶端版本為 v 1.15.2 且相容于繼續。

2. 取得 Kubernetes 叢集中執行的 pod 清單。 Pod 是在您的 Kubernetes 叢集上執行的應用程式容器或進程。

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   以下是命令使用方式的範例：
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   輸出應該指出因為您的叢集上沒有執行任何應用程式，所以找不到 (pod) 的資源。

   此命令會填入 \\ &lt; &gt; \\ 具有設定檔的 "C:\Users username. kube" 目錄結構 \" 。 Kubectl 命令列工具會使用這些檔案來建立和管理 Kubernetes 叢集上的無狀態應用程式。

3. 請手動檢查 "C:\Users username. kube" 的目錄結構 \\ &lt; &gt; \\ \" ，以確認*kubectl*已填入下列子資料夾：

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> 若要查看所有 kubectl 命令的清單，請輸入 `kubectl --help` 。

### <a name="create-a-stateless-application-using-a-deployment"></a>使用部署建立無狀態應用程式

現在您已驗證 kubectl 命令列版本是否正確，並擁有必要的設定檔案，您可以建立無狀態的應用程式部署。

Pod 是 Kubernetes 應用程式的基本執行單位，這是您建立或部署之 Kubernetes 物件模型中最小且最簡單的單位。 Pod 也會封裝儲存體資源、唯一的網路 IP，以及管理容器 () 應該如何執行的選項。

您建立的無狀態應用程式類型是 nginx web 伺服器部署。

您用來建立及管理無狀態應用程式部署的所有 kubectl 命令都必須指定與設定相關聯的命名空間。 當您在 [Microsoft Azure Stack Edge 裝置上的 [建立和管理 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md) 叢集] 教學課程中，連線到 Azure Stack Edge 裝置上的叢集時，您已建立命名空間 `New-HcsKubernetesNamespace` 。

若要在 kubectl 命令中指定命名空間，請使用 `kubectl <command> -n <namespace-string>` 。

遵循下列步驟來建立 nginx 部署：

1. 藉由建立 Kubernetes 部署物件來套用無狀態應用程式：

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   在此範例中，應用程式 YAML 檔的路徑是外部來源。

   以下是命令和輸出的範例使用方式：

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   或者，您可以將下列 markdown 儲存至本機電腦，並取代 *-f* 參數中的路徑和檔案名。 例如，"C:\Kubernetes\deployment.yaml"。 以下是應用程式部署的設定：

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   此命令會建立具有兩個 pod 以執行應用程式的預設 nginx 部署。

2. 取得您所建立之 Kubernetes nginx 部署的描述：

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   以下是命令和輸出的範例使用方式：
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   如果您仔細查看 [ *複本* ] 設定，您將會看到：
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   [ *複本* ] 設定表示您的部署規格需要兩個 pod，也就是建立和更新的 pod，以及它們已準備好供您使用。

   > [!NOTE]
   > 複本集會取代因為任何原因而刪除或終止的 pod，例如裝置節點失敗或干擾裝置升級的情況。 基於這個理由，即使您的應用程式只需要單一 pod，仍建議您使用複本集。

3. 若要列出您部署中的 pod：

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   以下是命令和輸出的範例使用方式：
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   輸出會確認我們有兩個 pod 具有唯一名稱，我們可以使用 kubectl 來參考這些 pod。

4. 若要在您的部署中查看個別 pod 的資訊：

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

   以下是命令和輸出的範例使用方式：

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>藉由增加複本計數來調整應用程式部署的寬度

每個 pod 都是為了執行特定應用程式的單一實例。 如果您想要以水準方式調整您的應用程式以執行多個實例，您可以增加 pod 數目，每個實例各一個。 在 Kubernetes 中，這稱為「複寫」。
您可以套用新的 YAML 檔案，以增加應用程式部署中的 pod 數目。 YAML 檔案會將複本設定變更為4，這會將部署中的 pod 數目增加至四個 pod。 將 pod 數目從2增加到4：

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

或者，您可以將下列 markdown 儲存在本機電腦上，並以的 *-f* 參數的路徑和檔案名取代 `kubectl apply` 。 例如，"C:\Kubernetes\deployment-scale.yaml"。 以下是應用程式部署規模的設定：

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

若要確認部署有四個 pod：

```powershell
kubectl get pods -l app=nginx
```

以下是從二到四個 pod 進行重新調整部署的範例輸出：

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

您可以從輸出中看到，現在您的部署中有四個可執行應用程式的 pod。

### <a name="delete-a-deployment"></a>刪除部署

若要刪除部署（包括所有 pod），您必須執行 `kubectl delete deployment` 指定部署 *nginx* 和命名空間名稱的名稱。 若要刪除部署：

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

以下是命令使用方式和輸出的範例：

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>後續步驟

[Kubernetes 總覽](azure-stack-edge-gpu-kubernetes-overview.md)
