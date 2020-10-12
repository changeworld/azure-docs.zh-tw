---
title: 刪除已啟用 Azure Arc 的 SQL 受控執行個體
description: 刪除已啟用 Azure Arc 的 SQL 受控執行個體
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936831"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>刪除已啟用 Azure Arc 的 SQL 受控執行個體
本文說明如何刪除已啟用 Azure Arc 的 SQL 受控執行個體。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>查看現有已啟用 Azure Arc 的 SQL 受控實例
若要查看 SQL 受控實例，請執行下列命令：

```console
azdata arc sql mi list
```

輸出看起來應該像這樣：

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>刪除已啟用 Azure Arc 的 SQL 受控執行個體
若要刪除 SQL 受控執行個體，請執行下列命令：

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

輸出看起來應該像這樣：

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>將 Kubernetes 的永久性磁片區宣告回收 (Pvc) 

刪除 SQL 受控執行個體並不會移除其相關聯的 [pvc](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。 這是原廠設定。 其目的是要在意外刪除執行個體時，協助使用者存取資料庫檔案。 刪除 PVC 並不是必要的。 但是建議您這麼做。 如果您未回收這些 Pvc，最後最後會發生錯誤，因為您的 Kubernetes 叢集將會用盡磁碟空間。 若要回收 PVC，請採取下列步驟：

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 列出您所刪除之伺服器群組的 Pvc
若要列出 Pvc，請執行下列命令：
```console
kubectl get pvc
```

在下列範例中，請注意您已刪除之 SQL 受控實例的 Pvc。
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. 刪除每個 Pvc
針對您所刪除的每個 SQL 受控實例，刪除資料和記錄 Pvc。
此命令的一般格式為： 
```console
kubectl delete pvc <name of pvc>
```

例如：
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

這些 kubectl 命令都會確認成功刪除 PVC。 例如：
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> 如所述，不刪除 Pvc 最後可能會在發生錯誤的情況下取得您的 Kubernetes 叢集。 其中某些錯誤可能包括無法使用 azdata 登入您的 Kubernetes 叢集，因為此儲存問題 (一般 Kubernetes 行為) 。
>
> 例如，您可能會在記錄檔中看到類似以下的訊息：  
> - 批註： microsoft.com/ignore-pod-health： true  
> - 狀態：失敗  
> - 原因：已收回  
> - 訊息：節點資源不足：暫時儲存體。 容器控制器使用的16372Ki 超過其要求0。

## <a name="next-steps"></a>後續步驟

深入了解[啟用 Azure Arc 的 SQL 受控執行個體特性和功能](managed-instance-features.md)

[從建立資料控制器開始](create-data-controller.md)

已經建立資料控制器了嗎？ [建立啟用 Azure Arc 的 SQL 受控執行個體](create-sql-managed-instance.md)