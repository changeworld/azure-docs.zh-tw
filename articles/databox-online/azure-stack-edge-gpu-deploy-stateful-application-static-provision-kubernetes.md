---
title: 在 Azure Stack Edge Pro 裝置上，使用 kubectl 透過靜態布建的共用來部署 Kubernetes 具狀態應用程式 |Microsoft Docs
description: 說明如何使用 Azure Stack Edge Pro GPU 裝置上的 kubectl，透過靜態布建的共用來建立和管理 Kubernetes 具狀態應用程式部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 5704f88d8099966eedcb7143085130ad1376d742
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804890"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>使用 kubectl 搭配 Azure Stack Edge Pro 裝置上的 PersistentVolume 來執行 Kubernetes 具狀態應用程式

本文說明如何使用 PersistentVolume (PV) 和部署，在 Kubernetes 中部署單一實例的可設定狀態應用程式。 部署會 `kubectl` 在現有的 Kubernetes 叢集上使用命令，並部署 MySQL 應用程式。 

此程式適用于已 [在 Azure Stack Edge Pro 裝置上審核 Kubernetes 儲存體](azure-stack-edge-gpu-kubernetes-storage.md) 並熟悉 [Kubernetes 儲存體](https://kubernetes.io/docs/concepts/storage/)概念的人。

Azure Stack Edge Pro 也支援執行 Azure SQL Edge 容器，而這些容器的部署方式類似于 MySQL 的詳細說明。 如需詳細資訊，請參閱 [AZURE SQL Edge](../azure-sql-edge/overview.md)。


## <a name="prerequisites"></a>先決條件

在您可以部署具狀態應用程式之前，請先在您的裝置和用戶端上完成下列必要條件，以供您用來存取裝置：

### <a name="for-device"></a>針對裝置

- 您有1個節點 Azure Stack Edge Pro 裝置的登入認證。
    - 裝置已啟用。 請參閱 [啟用裝置](azure-stack-edge-gpu-deploy-activate.md)。
    - 裝置具有透過 Azure 入口網站設定的計算角色，且具有 Kubernetes 叢集。 請參閱 [設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)。

### <a name="for-client-accessing-the-device"></a>適用于存取裝置的用戶端

- 您有將用來存取 Azure Stack Edge Pro 裝置的 Windows 用戶端系統。
    - 用戶端正在執行 Windows PowerShell 5.0 或更新版本。 若要下載 Windows PowerShell 的最新版本，請移至 [ [安裝 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7&preserve-view=true)。
    
    - 您也可以讓任何其他用戶端使用 [支援的作業系統](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 。 本文說明使用 Windows 用戶端的程式。 
    
    - 您已完成在 [Azure Stack Edge Pro 裝置上存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集所述的程式。 您已經：
      - 透過 `userns1` 命令建立命名空間 `New-HcsKubernetesNamespace` 。 
      - 透過命令建立使用者 `user1` `New-HcsKubernetesUser` 。 
      - 已透過 `user1` 命令授與存取權 `userns1` `Grant-HcsKubernetesNamespaceAccess` 。       
      - 安裝 `kubectl` 在用戶端上，並將具有使用者設定的檔案儲存 `kubeconfig` 至 C： \\ Users \\ &lt; username &gt; \\ . kube。 
    
    - 請確定 `kubectl` 用戶端版本的 Azure Stack Edge Pro 裝置上執行的 Kubernetes 主要版本，不會有多個版本的扭曲。 
        - 用 `kubectl version` 來檢查用戶端上執行的 kubectl 版本。 請記下完整版。
        - 在 Azure Stack Edge Pro 裝置的本機 UI 中，移至 **[總覽** ] 並記下 Kubernetes 軟體號碼。 
        - 確認這兩個版本是否相容于支援的 Kubernetes 版本中提供的對應。<!-- insert link--> 


您已經準備好在 Azure Stack Edge Pro 裝置上部署可設定狀態的應用程式。 

## <a name="provision-a-static-pv"></a>布建靜態 PV

若要以靜態方式布建 PV，您需要在您的裝置上建立共用。 遵循這些步驟，針對您的 SMB 共用布建 PV。 

> [!NOTE]
> 本操作說明文章中使用的特定範例無法與 NFS 共用搭配使用。 一般來說，您可以使用非資料庫應用程式，將 NFS 共用布建在您的 Azure Stack Edge 裝置上。

1. 選擇您是否要建立 Edge 共用或 Edge 本機共用。 遵循 [新增共用](azure-stack-edge-manage-shares.md#add-a-share) 中的指示來建立共用。 請務必選取 [ **使用 Edge 計算的共用**] 核取方塊。

    ![PV 的 Edge 本機共用](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. 若您決定使用現有的共用，而不是建立新的共用，您將需要掛接共用。
    
        在 Azure Stack Edge 資源的 Azure 入口網站中，移至 [ **共用**]。 從現有的共用清單中，選取並按一下您想要使用的共用。

        ![選取現有的本機共用以取得 PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. 當系統提示時，選取 [ **掛接** 並確認裝載]。  

        ![裝載適用于 PV 的現有本機共用](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. 記下共用名稱。 建立此共用時，會在對應至您所建立之 SMB 共用的 Kubernetes 叢集中自動建立永久性磁片區物件。 

## <a name="deploy-mysql"></a>部署 MySQL

您現在將建立 Kubernetes 部署，並使用 PersistentVolumeClaim (PVC) ，將它連接至您在先前步驟中建立的 PV，以執行具狀態應用程式。 

`kubectl`您用來建立和管理具狀態應用程式部署的所有命令都必須指定與設定相關聯的命名空間。 若要在 kubectl 命令中指定命名空間，請使用 `kubectl <command> -n <your-namespace>` 。

1. 取得在您的命名空間中的 Kubernetes 叢集上執行的 pod 清單。 Pod 是在您的 Kubernetes 叢集上執行的應用程式容器或進程。

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   命令使用方式的範例如下：
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   輸出應該指出因為您的叢集上沒有執行任何應用程式，所以找不到 (pod) 的資源。

1. 您將使用下列 YAML 檔。 此檔案會 `mysql-deployment.yml` 描述執行 MySQL 並參考 PVC 的部署。 檔案會定義的磁片區掛接 `/var/lib/mysql` ，然後建立可尋找 20 GB 磁片區的 PVC。 

    當您在先前的步驟中建立共用時，靜態布建的任何現有 PV 都會滿足此宣告。 在您的裝置上，系統會為每個共用建立大型的 PV 32 TB。 PV 符合 PVC 所設定的需求，且 PVC 應系結至此 PV。

    將下列檔案複製並儲存 `mysql-deployment.yml` 到 Windows 用戶端上您用來存取 Azure Stack Edge Pro 裝置的資料夾中。
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. 將檔案複製並儲存 `mysql-pv.yml` 到儲存的相同資料夾中 `mysql-deployment.yml` 。 若要使用您稍早建立的 SMB 共用 `kubectl` ，請將 `volumeName` PVC 物件中的欄位設定為共用的名稱。 

    > [!NOTE] 
    > 請確定 YAML 檔案具有正確的縮排。 您可以使用不 [起毛的 YAML](http://www.yamllint.com/) 來進行驗證，然後再儲存。
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. 部署檔案 `mysql-pv.yaml` 。

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    以下是部署的範例輸出。

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   記下所建立之 PVC 的名稱。 您將在稍後的步驟中使用它。 

4. 部署檔案的內容 `mysql-deployment.yml` 。

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    以下是部署的範例輸出。
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. 顯示部署的相關資訊。

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. 列出部署所建立的 pod。

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    以下是範例輸出。

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. 檢查 PersistentVolumeClaim。

    `kubectl describe pvc <your-pvc-name>`

    以下是範例輸出。

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>確認 MySQL 正在執行


若要針對正在執行 MySQL 之 pod 中的容器執行命令，請輸入：

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

以下是範例輸出。

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>刪除部署

若要刪除部署，請依名稱刪除已部署的物件。 這些物件包括部署、服務和 PVC。
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

以下是當您刪除部署和服務時的範例輸出。

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
以下是當您刪除 PVC 時的範例輸出。

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

當 PVC 被刪除時，PV 不再系結至 PVC。 當建立共用時布建了 PV，您將需要刪除該共用。 請遵循下列步驟：

1. 卸載共用。 在 Azure 入口網站中，移至您的 **Azure Stack Edge 資源 > 共用** ，然後選取並按一下您要卸載的共用。 選取 [ **卸載** ] 並確認操作。 等候共用卸載。 取消掛接會釋放共用 (，因此會從 Kubernetes 叢集) 相關聯的 PersistentVolume。 

    ![適用于 PV 的卸載本機共用](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. 您現在可以選取 [ **刪除** ] 並確認刪除，以刪除您的共用。 這也應該刪除共用和對應的 PV。

    ![刪除本機共用以取得 PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>後續步驟

若要瞭解如何動態布建儲存體，請參閱 [在 Azure Stack Edge Pro 裝置上透過動態布建來部署具狀態應用程式](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)