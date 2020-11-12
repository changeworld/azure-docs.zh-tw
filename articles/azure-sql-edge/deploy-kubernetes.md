---
title: 在 Kubernetes 中部署 Azure SQL Edge 容器 - Azure SQL Edge
description: 了解如何在 Kubernetes 中部署 Azure SQL Edge 容器
keywords: SQL Edge, 容器, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395286"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>在 Kubernetes 中部署 Azure SQL Edge 容器

您可以使用在 Kubernetes 作為 IoT Edge 模組執行的 Azure IoT Edge 或單獨的容器 Pod，在 Kubernetes 叢集上部署 Azure SQL Edge。 在本文的其餘部分，我們會將焦點放在 kubernetes 叢集上的獨立容器部署。 如需在 Kubernetes 上部署 Azure IoT Edge 的詳細資訊，請參閱 [Kubernetes 上的 Azure IoT Edge (預覽)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html)。

本教學課程示範如何在 kubernetes 叢集容器中設定高可用性 Azure SQL Edge 執行個體。 

> [!div class="checklist"]
> * 建立 SA 密碼
> * 建立儲存體
> * 建立部署
> * 使用 SQL Server Management Studio (SSMS) 連線
> * 驗證失敗和復原

Kubernetes 1.6 和更新版本支援[儲存體類別](https://kubernetes.io/docs/concepts/storage/storage-classes/)、[永續性磁碟區宣告](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)，以及 [Azure 磁碟區類型](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk)。 您可以在 Kubernetes 中以原生方式建立並管理您的 Azure SQL Edge 執行個體。 本文中的範例說明如何建立[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)，以達成類似於共用磁碟容錯移轉叢集執行個體的高可用性設定。 在此設定中，Kubernetes 扮演叢集協調器的角色。 當容器中的 Azure SQL Edge 執行個體失敗時，協調器會啟動附加至相同永續性儲存體的其他容器執行個體。

![Kubernetes 叢集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge.png)

在上圖中，`azure-sql-edge` 是 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 中的容器。 Kubernetes 會協調叢集中的資源。 [複本集](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)可確保 Pod 會在節點失敗後自動復原。 應用程式會連線至服務。 在此情況下，服務代表的負載平衡器會在 `azure-sql-edge` 失敗後維持相同 IP 位址。

在下圖中，`azure-sql-edge` 容器已失敗。 作為協調器，Kubernetes 可保證複本集中狀態良好的執行個體正確計數，並根據設定啟動新的容器。 協調器會在相同節點上啟動新的 Pod，而 `azure-sql-edge` 會重新連接到相同的永續性儲存體。 服務會連接到重新建立的 `azure-sql-edge`。

![Pod 失敗之後，Kubernetes 叢集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

在下圖中，裝載 `azure-sql-edge` 容器的節點已失敗。 協調器會在不同節點上啟動新的 Pod，而 `azure-sql-edge` 會重新連接到相同的永續性儲存體。 服務會連接到重新建立的 `azure-sql-edge`。

![節點失敗後，Kubernetes 叢集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Prerequisites

* **Kubernetes 叢集**
   - 本教學課程需要使用 Kubernetes 叢集。 這些步驟會使用 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) 來管理叢集。 

   - 基於本教學課程的目的，我們將使用 Azure Kubernetes Service 來部署 Azure SQL Edge。 請參閱[部署 Azure Kubernetes Service (AKS) 叢集](../aks/tutorial-kubernetes-deploy-cluster.md)，以使用 `kubectl` 在 AKS 中建立單一節點 Kubernetes 叢集並進行連線。 

   >[!NOTE]
   >為了防止節點失敗，Kubernetes 叢集需要一個以上的節點。

* **Azure CLI**
   - 本教學課程中的指示已經過 Azure CLI 2.10.1 驗證。

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>建立 Kubernetes 命名空間以部署 SQL Edge

在 Kubernetes 叢集中建立新的命名空間。 這個命名空間將用來部署 SQL Edge 和所有必要的成品。 如需 Kubernetes 命名空間詳細資訊，請參閱[命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)。

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>建立 SA 密碼

在 Kubernetes 叢集中建立 SA 密碼。 Kubernetes 可以管理敏感性設定資訊，例如作為 [Secret](https://kubernetes.io/docs/concepts/configuration/secret/) 的密碼。

下列命令會建立 SA 帳戶的密碼：

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   請將 `MyC0m9l&xP@ssw0rd` 取代為複雜密碼。

## <a name="create-storage"></a>建立儲存體

在 Kubernetes 叢集中，設定[永續性磁碟區](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)和[永續性磁碟區宣告](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection)。 完成下列步驟： 

1. 建立資訊清單，以定義儲存體類別和永續性磁碟區宣告。  資訊清單會指定儲存體佈建程式、參數和[回收原則](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming)。 Kubernetes 叢集會使用這個資訊清單來建立永續性儲存體。 

   下列 YAML 範例會定義儲存體類別和永續性磁碟區宣告。 儲存類別佈建程式是 `azure-disk`，因為此 Kubernetes 叢集位於 Azure 中。 儲存體帳戶類型為 `Standard_LRS`。 永續性磁碟區宣告名為 `mssql-data`。 永續性磁碟區宣告中繼資料包含可將其連接回儲存體類別的注釋。 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   儲存檔案 (例如 **pvc.yaml** )。

2. 在 Kubernetes 中建立永續性磁碟區宣告。

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` 是您儲存檔案的位置。

   系統會自動將永續性磁碟區建立為 Azure 儲存體帳戶，並繫結至永續性磁碟區宣告。 

    ![永續性磁碟區宣告命令的螢幕擷取畫面](media/deploy-kubernetes/pvc-cmd.png)

3. 驗證永續性磁碟區宣告。

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` 是永續性磁碟區宣告的名稱。

   在先前的步驟中，永續性磁碟區宣告名為 `mssql-data`。 若要查看永續性磁碟區宣告的相關中繼資料，請執行下列命令：

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   所傳回中繼資料包含名為 `Volume` 的值。 這個值會對應至 Blob 的名稱。

   ![傳回的中繼資料 (包括磁碟區) 螢幕擷取畫面](media/deploy-kubernetes/describe-volume.png)

4. 驗證永續性磁碟區。

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` 會傳回永續性磁碟區 (之前由系統自動建立並繫結至永續性磁碟區宣告) 的相關中繼資料。 

## <a name="create-the-deployment"></a>建立部署

此範例會將裝載 Azure SQL Edge 執行個體的容器描述為 Kubernetes 部署物件。 部署會建立複本集。 複本集會建立 Pod。 

在此步驟中，請建立資訊清單，以根據 Azure SQL Edge Docker 映像來描述容器。 資訊清單會參考 `mssql-data` 永續性磁碟區宣告，以及您已套用至 Kubernetes 叢集的 `mssql` Secret。 資訊清單也會描述[服務](https://kubernetes.io/docs/concepts/services-networking/service/)。 此服務是負載平衡器。 負載平衡器可確保在 Azure SQL Edge 執行個體復原之後，IP 位址會持續保留。 

1. 建立資訊清單 (YAML 檔案) 來描述部署。 下列為描述部署的範例，包括以 Azure SQL Edge 容器映像為基礎的容器。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   將上述程式碼複製到名為 `sqldeployment.yaml` 的新檔案中。 更新下列值： 

   * MSSQL_PID `value: "Developer"`：設定容器以執行 Azure SQL Edge Developer 版本。 Developer 版本未獲授權用於生產環境資料。 如果部署是供生產環境使用，請設定適當的 `Premium` 版本。 

      >[!NOTE]
      >如需詳細資訊，請參閱[如何授權 Azure SQL Edge](https://azure.microsoft.com/pricing/details/sql-edge/)。

   * `persistentVolumeClaim`:此值需要使用與永續性磁碟區宣告所用名稱對應的 `claimName:` 項目。 本教學課程使用 `mssql-data`。 

   * `name: SA_PASSWORD`:設定容器映像以設定 SA 密碼，如本節中所定義。

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     當 Kubernetes 部署容器時，它會參考名為 `mssql` 的 Secret，以取得密碼的值。 

   >[!NOTE]
   >藉由使用 `LoadBalancer` 服務類型，您可以在連接埠 1433 遠端存取 (透過網際網路) Azure SQL Edge 執行個體。

   儲存檔案 (例如 **sqledgedeploy.yaml** )。

2. 建立部署。

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` 是您儲存檔案的位置。

   ![部署命令的螢幕擷取畫面](media/deploy-kubernetes/deploy-sql-cmd.png)

   您已建立部署和服務。 Azure SQL Edge 執行個體所在的容器已連線至永續性儲存體。

   若要檢視 Pod 的狀態，請鍵入 `kubectl get pod -n <namespace name>`。

   ![get pod 命令的螢幕擷取畫面](media/deploy-kubernetes/get-sql-pod-cmd.png)

   在上圖中，Pod 的狀態為 `Running`。 此狀態表示容器已就緒。 這可能需要幾分鐘的時間。

   >[!NOTE]
   >建立部署之後，可能需要幾分鐘的時間才能顯示 Pod。 因為叢集是從 Docker Hub 提取 Azure SQL Edge 容器映像，所以會導致這個延遲。 第一次提取映像之後，如果部署抵達的節點中已經快取映像，則後續部署可能會更快速。 

3. 驗證服務是否正在執行。 執行以下命令：

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   此命令會傳回正在執行的服務，以及服務的內部和外部 IP 位址。 記下 `mssql-deployment` 服務的外部 IP 位址。 使用此 IP 位址連接到 Azure SQL Edge。 

   ![get service 命令的螢幕擷取畫面](media/deploy-kubernetes/get-service-cmd.png)

   如需 Kubernetes 叢集中的物件狀態詳細資訊，請執行：

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>連線到 Azure SQL Edge 執行個體

如果您已如所述設定容器，即可從 Azure 虛擬網路外部連線到應用程式。 使用 `sa` 帳戶和服務的外部 IP 位址。 使用您設定為 Kubernetes Secret 的密碼。 如需連線到 Azure SQL Edge 執行個體的詳細資訊，請參閱 [連線到 Azure SQL Edge](connect.md)。

## <a name="verify-failure-and-recovery"></a>驗證失敗和復原

若要驗證失敗和復原，您可以刪除 Pod。 請執行下列步驟：

1. 列出執行 Azure SQL Edge 的 Pod。

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   記下執行 Azure SQL Edge 的 Pod 名稱。

2. 刪除 Pod。

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` 即為上一個步驟傳回的 Pod 名稱值。 

Kubernetes 會自動重新建立 Pod，以復原 Azure SQL Edge 執行個體，並連接永續性儲存體。 使用 `kubectl get pods` 來驗證是否已部署新的 Pod。 使用 `kubectl get services` 來驗證新容器的 IP 位址是否相同。 

## <a name="summary"></a>摘要

在這個教學課程中，您已了解如何將 Azure SQL Edge 容器部署至 Kubernetes 叢集以確保高可用性。 

> [!div class="checklist"]
> * 建立 SA 密碼
> * 建立儲存體
> * 建立部署
> * 使用 Azure SQL Edge Management Studio (SSMS) 連線
> * 驗證失敗和復原

## <a name="next-steps"></a>後續步驟

- [Kubernetes 簡介](../aks/intro-kubernetes.md)
- [SQL Edge 中採用 ONNX 的機器學習與人工智慧](onnx-overview.md)。
- [使用 SQL Edge 和 IoT Edge 建置端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的資料串流](stream-data.md)