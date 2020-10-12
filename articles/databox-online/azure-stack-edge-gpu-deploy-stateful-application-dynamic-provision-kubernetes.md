---
title: 使用 kubectl 透過 Azure Stack Edge Pro GPU 裝置上動態布建的共用來部署 Kubernetes 具狀態應用程式 |Microsoft Docs
description: 說明如何在 Microsoft Azure Stack Edge Pro GPU 裝置上，使用 kubectl，透過動態布建的共用來建立和管理 Kubernetes 具狀態應用程式部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: d37152f7dec78d5f5db21fdde9a8ec25c36c4e05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899464"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>使用 kubectl 在 Azure Stack Edge Pro GPU 裝置上執行具有 StorageClass 的 Kubernetes 具狀態應用程式

本文說明如何使用 StorageClass 動態布建儲存體和部署，在 Kubernetes 中部署單一實例的可設定狀態應用程式。 部署會 `kubectl` 在現有的 Kubernetes 叢集上使用命令，並部署 MySQL 應用程式。 

此程式適用于已 [在 Azure Stack Edge Pro 裝置上審核 Kubernetes 儲存體](azure-stack-edge-gpu-kubernetes-storage.md) 並熟悉 [Kubernetes 儲存體](https://kubernetes.io/docs/concepts/storage/)概念的人。


## <a name="prerequisites"></a>必要條件

部署可設定狀態的應用程式之前，請確定您已在裝置和您將用來存取裝置的用戶端上完成下列必要條件：

### <a name="for-device"></a>針對裝置

- 您有1個節點 Azure Stack Edge Pro 裝置的登入認證。
    - 裝置已啟用。 請參閱 [啟用裝置](azure-stack-edge-gpu-deploy-activate.md)。
    - 裝置具有透過 Azure 入口網站設定的計算角色，且具有 Kubernetes 叢集。 請參閱 [設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)。

### <a name="for-client-accessing-the-device"></a>適用于存取裝置的用戶端

- 您有將用來存取 Azure Stack Edge Pro 裝置的 Windows 用戶端系統。
    - 用戶端正在執行 Windows PowerShell 5.0 或更新版本。 若要下載 Windows PowerShell 的最新版本，請移至 [ [安裝 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。
    
    - 您也可以讓任何其他用戶端使用 [支援的作業系統](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 。 本文說明使用 Windows 用戶端的程式。 
    
    - 您已完成在 [Azure Stack Edge Pro 裝置上存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集所述的程式。 您已經：
      - 透過 `userns1` 命令建立命名空間 `New-HcsKubernetesNamespace` 。 
      - 透過命令建立使用者 `user1` `New-HcsKubernetesUser` 。 
      - 已透過 `user1` 命令授與存取權 `userns1` `Grant-HcsKubernetesNamespaceAccess` 。       
      - 安裝 `kubectl` 在用戶端上，並將具有使用者設定的檔案儲存 `kubeconfig` 至 C： \\ Users \\ &lt; username &gt; \\ . kube。 
    
    - 請確定 `kubectl` 用戶端版本的 Azure Stack Edge Pro 裝置上執行的 Kubernetes 主要版本，不會有多個版本的扭曲。 
        - 用 `kubectl version` 來檢查用戶端上執行的 kubectl 版本。 請記下完整版。
        - 在 Azure Stack Edge Pro 裝置的本機 UI 中，移至 **[總覽** ] 並記下 Kubernetes 軟體號碼。 
        - 確認這兩個版本是否相容于支援的 Kubernetes 版本中提供的對應<!-- insert link-->. 


您已經準備好在 Azure Stack Edge Pro 裝置上部署可設定狀態的應用程式。 


## <a name="deploy-mysql"></a>部署 MySQL

您現在將建立 Kubernetes 部署，並使用 PersistentVolumeClaim (PVC) 將它連接到內建 StorageClass，以執行具狀態應用程式。 

`kubectl`您用來建立和管理具狀態應用程式部署的所有命令都必須指定與設定相關聯的命名空間。 若要在 kubectl 命令中指定命名空間，請使用 `kubectl <command> -n <your-namespace>` 。

1. 取得在您的命名空間中的 Kubernetes 叢集上執行的 pod 清單。 Pod 是在您的 Kubernetes 叢集上執行的應用程式容器或進程。

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   以下是命令使用方式的範例：
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   輸出應該指出因為您的叢集上沒有執行任何應用程式，所以找不到 (pod) 的資源。

1. 您將使用下列 YAML 檔。 此檔案會 `mysql-deployment.yml` 描述執行 MySQL 並參考 PVC 的部署。 檔案會定義的磁片區掛接 `/var/lib/mysql` ，然後建立可尋找 20 GB 磁片區的 PVC。 已布建動態 PV，且 PVC 系結至此 PV。

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
              claimName: mysql-pv-claim-sc
    ```
    
2. 將檔案複製並儲存 `mysql-pvc.yml` 到儲存的相同資料夾中 `mysql-deployment.yml` 。 若要在連接的資料磁片上使用 Azure Stack Edge Pro 裝置的內建 StorageClass，請將 `storageClassName` PVC 物件中的欄位設定為 `ase-node-local` ，accessModes 應該是 `ReadWriteOnce` 。 

    > [!NOTE] 
    > 請確定 YAML 檔案具有正確的縮排。 您可以使用不 [起毛的 YAML](http://www.yamllint.com/) 來進行驗證，然後再儲存。
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. 部署檔案 `mysql-pvc.yaml` 。

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    以下是部署的範例輸出。

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   請注意建立的 PVC 名稱，如下所示 `mysql-pv-claim-sc` 。 您將在稍後的步驟中使用它。 

4. 部署檔案的內容 `mysql-deployment.yml` 。

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    以下是部署的範例輸出。
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. 顯示部署的相關資訊。

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. 列出部署所建立的 pod。

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    以下是範例輸出。

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. 檢查 PersistentVolumeClaim。

    `kubectl describe pvc <your-pvc-name>`

    以下是範例輸出。

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>確認 MySQL 正在執行

若要確認應用程式是否正在執行，請輸入：

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

系統提示時，請提供密碼。 密碼是在您的檔案中 `mysql-deployment` 。

以下是範例輸出。

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>後續步驟

若要瞭解如何透過 kubectl 設定網路功能，請參閱 [在 Azure Stack Edge Pro 裝置上部署無狀態應用程式](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)
