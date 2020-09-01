---
title: 在 Azure Stack Edge 裝置上的啟用 Arc 的 Kubernetes 上部署 PHP 意見簿應用程式 |Microsoft Docs
description: 描述如何在 Azure Stack Edge 裝置的啟用 Arc 的 Kubernetes 叢集上，使用 Gitops) 將在已啟用 Arc 的叢集上部署具有 Redis 的 PHP 意見簿無狀態應用程式。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 46cef291a447a7c243ee9ef66ee64e9c6264ad23
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083464"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge"></a>在 Azure Stack Edge 上已啟用 Arc 的 Kubernetes 叢集上部署 PHP 意見簿無狀態應用程式（含 Redis）

本文說明如何使用 Kubernetes 和 Azure Arc 來建立和部署簡單的多層式 web 應用程式。此範例包含下列元件：

- 用來儲存意見簿專案的單一實例 Redis 主機
- 多個複寫的 Redis 實例服務讀取
- 多個 web 前端實例

部署是使用 Gitops) 將，在您 Azure Stack Edge 裝置上啟用 Arc 的 Kubernetes 叢集上完成。 

此程式適用于已 [在 Azure Stack Edge 裝置上審核 Kubernetes 工作負載](azure-stack-edge-gpu-kubernetes-workload-management.md) 的人員，並熟悉 [Azure Arc 啟用的功能 Kubernetes (預覽) ](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)的概念。


## <a name="prerequisites"></a>必要條件

在您可以部署無狀態應用程式之前，請確定您已在裝置上完成下列必要條件，以及用來存取裝置的用戶端：

### <a name="for-device"></a>針對裝置

1. 您有1個節點 Azure Stack Edge 裝置的登入認證。
    1. 裝置已啟用。 請參閱 [啟用裝置](azure-stack-edge-gpu-deploy-activate.md)。
    1. 裝置具有透過 Azure 入口網站設定的計算角色，且具有 Kubernetes 叢集。 請參閱 [設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)。

1. 您已在裝置上的現有 Kubernetes 叢集上啟用 Azure Arc，且 Azure 入口網站中有對應的 Azure Arc 資源。 如需詳細步驟，請參閱 [Azure Stack Edge 裝置上的啟用 Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。

### <a name="for-client-accessing-the-device"></a>適用于存取裝置的用戶端

1. 您有將用來存取 Azure Stack Edge 裝置的 Windows 用戶端系統。
  
    - 用戶端正在執行 Windows PowerShell 5.0 或更新版本。 若要下載 Windows PowerShell 的最新版本，請移至 [ [安裝 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。
    
    - 您也可以讓任何其他用戶端使用 [支援的作業系統](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 。 本文說明使用 Windows 用戶端的程式。 
    
1. 您已完成在 [Azure Stack Edge 裝置上存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集所述的程式。 您已經：
    
    - 安裝 `kubectl` 在用戶端上  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - 請確定 `kubectl` 用戶端版本的 Azure Stack Edge 裝置上執行的 Kubernetes 主要版本不會有多個版本的扭曲。 
      - 用 `kubectl version` 來檢查用戶端上執行的 kubectl 版本。 請記下完整版。
      - 在 Azure Stack Edge 裝置的本機 UI 中，移至 **[總覽** ] 並記下 Kubernetes 軟體號碼。 
      - 確認這兩個版本是否相容于支援的 Kubernetes 版本中提供的對應 <!--insert link-->.

1. 您有 [可用來執行 Azure Arc 部署的 gitops) 將](https://github.com/kagoyal/dbehaikudemo)設定。 您將使用下列檔案 `yaml` ，在 Azure Stack Edge 裝置上部署。

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>部署組態

遵循下列步驟來設定 Azure Arc 資源，以透過 Azure 入口網站部署 Gitops) 將設定： 

1. 在您的 Azure 入口網站中，移至您在裝置上的 Kubernetes 叢集上啟用 Azure Arc 時所建立的 Azure Arc 資源。 

    ![移至 Azure Arc 資源](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. 移至 [設定] **，然後選取** [ **+ 新增**設定]。

    ![移至設定](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. 在 [ **新增**設定] 中，為欄位輸入適當的值， **然後選取 [** 套用]。

    |參數  |描述 |
    |---------|---------|
    |組態名稱     | 設定資源的名稱。        |
    |運算子實例名稱     |用來識別特定設定之操作員的實例名稱。 Name 是最多253個字元的字串，必須是小寫、英數位元、連字號和句點。         |
    |Operator 命名空間     | 設定為 **demotestguestbook** ，因為這符合部署中指定的命名空間 `yaml` 。 <br> 欄位會定義操作員安裝所在的命名空間。 Name 是最多253個字元的字串，必須是小寫、英數位元、連字號和句點。         |
    |存放庫 URL     |<br>Git 存放庫的路徑， `http://github.com/username/repo` 或 `git://github.com/username/repo` 格式設定 gitops) 將設定所在的位置。         |
    |運算子範圍     | 選取 [ **命名空間**]。 <br>這會定義操作員的安裝範圍。 選取此名稱做為命名空間。 您的操作員將安裝在部署 yaml 檔所指定的命名空間中。       |
    |運算子類型     | 保留預設值。 <br>這會指定運算子的類型（依預設，設定為 flux）。        |
    |Operator 參數     | 將此留白。 <br>此欄位包含要傳遞給 flux 運算子的參數。        |
    |Helm     | 將此設定為 **停用**。 <br>如果您要進行以圖表為基礎的部署，請啟用此選項。        |


    ![新增設定](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. 設定部署會開始，且 **操作員狀態** 會顯示為 [ **擱置**中]。 

    ![移至設定](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. 部署需要幾分鐘的時間。 當部署完成時， **操作員狀態** 會顯示為 **已安裝**。

    ![移至設定](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)


## <a name="verify-deployment"></a>驗證部署

透過 Gitops) 將設定的部署會建立 `demotestguestbook` 命名空間，如 git 存放庫中的部署檔案所指定 `yaml` 。

1. 套用 Gitops) 將設定之後，請 [連接到裝置的 PowerShell 介面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
1. 執行下列命令，以列出在 `demotestguestbook` 對應于部署的命名空間中執行的 pod。

    `kubectl get pods -n <your-namespace>`
  
    以下是範例輸出。
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. 在此範例中，前端服務已部署為類型： LoadBalancer。 您將需要尋找此服務的 IP 位址，才能查看意見簿。 執行下列命令。

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. 的前端服務 `type:LoadBalancer` 具有外部 IP 位址。 此 IP 來自您在裝置上設定計算網路設定時，為外部服務指定的 IP 位址範圍。 使用此 IP 位址可在 URL 上查看意見簿： `https://<external-IP-address>` 。

    ![查看留言簿](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>刪除部署

若要刪除部署，您可以從 Azure 入口網站中刪除設定。 這會刪除所建立的物件，包括部署和服務。

1. 在 Azure 入口網站中，移至 Azure Arc 資源 > 設定。 
1. 找出您想要刪除的設定。 選取 .。。以叫用操作功能表，然後選取 [ **刪除**]。
    ![刪除設定](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

刪除設定可能需要幾分鐘的時間。
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>後續步驟

瞭解如何 [使用 Kubernetes 儀表板來監視 Azure Stack Edge 裝置上的部署](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)
