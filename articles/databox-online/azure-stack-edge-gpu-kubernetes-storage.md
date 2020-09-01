---
title: 瞭解 Azure Stack Edge 裝置上的 Kubernetes 儲存體管理 |Microsoft Docs
description: 說明如何在 Azure Stack Edge 裝置上進行 Kubernetes 存放裝置管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 57574b66ddb20e592a5979a4b827347f7c8e09af
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268086"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 裝置上的 Kubernetes 儲存體管理

在您的 Azure Stack Edge 裝置上，當您設定計算角色時，會建立 Kubernetes 叢集。 建立 Kubernetes 叢集之後，就可以將容器化應用程式部署到 pod 中的 Kubernetes 叢集。 有不同的方式可提供儲存體給 Kubernetes 叢集中的 pod。 

本文說明一般（尤其是在 Azure Stack Edge 裝置的內容中）布建儲存在 Kubernetes 叢集的方法。 

## <a name="storage-requirements-for-kubernetes-pods"></a>Kubernetes pod 的儲存體需求

Kubernetes pod 是無狀態的，但它們執行的應用程式通常是具狀態的。 因為 pod 可能很短，而且它們會在 Kubernetes 節點之間重新開機、容錯移轉或移動，所以與 pod 相關聯的儲存體必須符合下列需求。 

儲存體必須：

- 存留于 pod 之外。
- 獨立于 pod 生命週期。
- 可從所有 Kubernetes 節點進行存取。

若要瞭解如何管理儲存體以進行 Kubernetes，有一項需要瞭解兩個 API 資源： 

- **PersistentVolume (PV) **：這是 Kubernetes 叢集中的一部分儲存體。 Kubernetes 儲存體可靜態布建為 `PersistentVolume` 。 也可以動態布建為  `StorageClass` 。

- **PersistentVolumeClaim (PVC) **：這是使用者對儲存體的要求。 Pvc 使用 PV 資源。 Pvc 可以要求特定的大小和存取模式。 

    由於使用者 `PersistentVolumes` 針對不同的問題需要具有不同的屬性，因此叢集管理員必須能夠提供不同于 `PersistentVolumes` 大小和存取模式的不同方式。 針對這些需求，您需要 `StorageClass` 資源。

儲存體布建可以是靜態或動態的。 下列各節將討論每個布建類型。

## <a name="staticprovisioning"></a>靜態布建

Kubernetes cluster admins 可以靜態布建儲存體。 若要這樣做，他們可以使用以 SMB/NFS 檔案系統為基礎的儲存體後端，或使用在內部部署環境中透過網路連接到本機的 iSCSI 磁片，或甚至使用雲端中的 Azure 檔案儲存體或 Azure 磁片。 此類型的儲存體預設不會布建，而且叢集管理員必須規劃和管理此布建。 
 
以下圖表說明如何在 Kubernetes 中使用靜態布建的儲存體： 

![透過 PersistentVolumes 提供靜態布建](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

會進行下列步驟： 

1. 布建**存放裝置**：叢集系統管理員會布建儲存體。 在此範例中，叢集系統管理員會建立一或多個 SMB 共用，以在對應至這些共用的 Kubernetes 叢集中自動建立永久性磁片區物件。 

1. 宣告**儲存體**：您提交要求儲存體的 PVC 部署。 此儲存體宣告是 (PVC) 的 PersistentVolumeClaim。 如果 PV 的大小和存取模式符合 PVC 的大小和存取模式，則 PVC 會系結至 PV。 PVC 和 PV map 一對一。

1. 將**Pvc 掛接到容器**：當 PVC 系結至 PV 之後，您就可以將此 pvc 掛接到容器中的路徑。 當容器中的應用程式邏輯讀取/寫入此路徑時，會將資料寫入 SMB 儲存體。
 

## <a name="dynamicprovisioning"></a>動態佈建

以下圖表說明如何在 Kubernetes 中使用靜態布建的儲存體： 

![透過 Storageclass 提供動態布建](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

會進行下列步驟： 

1. **定義存放裝置類別**：叢集系統管理員會根據您 Kubernetes 叢集的作業環境定義儲存類別。 叢集系統管理員也會部署布建程式，也就是部署在 Kubernetes 叢集上的另一個 pod 或應用程式。 布建程式具有可動態布建共用的所有詳細資料。  

1. 宣告**儲存體**：您提交會宣告儲存體的應用程式。 使用這個儲存類別參考建立 PVC 之後，便會叫用布建程式。 

1. **動態**布建儲存體：布建程式會動態建立與本機磁片儲存體相關聯的共用。 建立共用之後，它也會自動建立與此共用對應的 PV 物件。

1. 將**Pvc 掛接到容器**：當 PVC 系結至 PV 之後，您可以使用與靜態布建和讀取或寫入共用的相同方式，將 pvc 掛接到容器上的路徑。


## <a name="storage-provisioning-on-azure-stack-edge"></a>Azure Stack Edge 上的儲存體布建

在 Azure Stack Edge 裝置上， `PersistentVolumes` 會使用裝置的儲存體功能來建立靜態布建。 當您布建共用，並啟用 **[使用 Edge 計算的共用** ] 選項時，此動作會在 Kubernetes 叢集中自動建立 PV 資源。

![在 Azure 入口網站中建立本機共用以進行靜態布建](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

若要使用雲端階層處理，您可以在啟用 [使用共用與 Edge 計算] 選項的情況下建立 Edge 雲端共用。 此共用會自動建立一個 PV。 您寫入 Edge 共用的任何應用程式資料都會分層到雲端。 

![在 Azure 入口網站中建立雲端共用以進行靜態布建](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

您可以建立 SMB 和 NFS 共用，以在 Azure Stack Edge 裝置上以靜態方式布建 PVs。 布建 PV 之後，您會提交 PVC 來宣告此儲存體。 以下是用來 `yaml` 宣告儲存體並使用您所布建之共用的 PVC 部署範例。


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

如需詳細資訊，請參閱透過 [kubectl 在您的 Azure Stack Edge 上透過靜態布建部署具狀態應用程式](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。

Azure Stack Edge 也有一個名為的內建 `StorageClass` `ase-node-local` ，其使用連接至 Kubernetes 節點的資料磁片儲存體。 這 `StorageClass` 支援動態布建。 您可以 `StorageClass` 在 pod 應用程式中進行參考，並自動為您建立一個 PV。 如需詳細資訊，請參閱 [Kubernetes 儀表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) 以查詢 `ase-node-local StorageClass` 。

![Kubernetes 儀表板中的內建儲存類別](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

如需詳細資訊，請參閱透過 [kuebctl 透過動態布建在您的 Azure Stack Edge 上部署具狀態應用程式](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)。

## <a name="choose-storage-type"></a>選擇儲存體類型

視您要部署的工作負載而定，您可能需要選擇您的儲存體類型。 

- 如果您想要讓磁片區以 `ReadWriteMany` `PersistentVolumes` 讀寫方式裝載在許多部署節點的情況下存取模式，請使用 SMB/NFS 共用的靜態布建。

- 如果您要部署的應用程式具有 POSIX 合規性需求（例如 MongoDB、于 postgresql、MySQL 或 Prometheus 等應用程式），請使用內建 StorageClass。 存取模式為， `ReadWriteOnce` 或磁片區由單一節點裝載為讀寫。 


如需存取模式的詳細資訊，請參閱 [Kubernetes 磁片區存取模式](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)。


## <a name="next-steps"></a>後續步驟

若要瞭解您可以如何靜態布建 `PersistentVolume` ，請參閱：

- 透過[kubectl 在您的 Azure Stack Edge 上透過靜態布建來部署具狀態應用程式](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。

若要瞭解如何以動態方式布建 `StorageClass` ，請參閱：

- 透過[kuebctl 在您的 Azure Stack Edge 上透過動態布建來部署具狀態應用程式](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)。
