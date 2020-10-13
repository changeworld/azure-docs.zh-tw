---
title: 刪除已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組
description: 刪除已啟用 Azure Arc 的 Postgres 超大規模伺服器群組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dcabe4b1520c66b8d5bfa398dc1248972587cd32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936807"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>刪除已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組

本檔說明從 Azure Arc 安裝程式刪除伺服器群組的步驟。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>刪除伺服器群組

例如，讓我們考慮要從下列設定中刪除 _postgres01_ 實例：

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Delete 命令的一般格式為：
```console
azdata arc postgres server delete -n <server group name>
```
如需刪除命令的詳細資訊，請執行：
```console
azdata arc postgres server delete --help
```

### <a name="lets-delete-the-server-group-used-in-this-example"></a>讓我們刪除此範例中使用的伺服器群組：
```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>將 Kubernetes 的永久性磁片區宣告回收 (Pvc) 

刪除伺服器群組並不會移除其相關聯的 [pvc](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。 這是原廠設定。 其目的是要在意外刪除執行個體時，協助使用者存取資料庫檔案。 刪除 PVC 並不是必要的。 但是建議您這麼做。 如果您不回收這些 PVC，最後將會發生錯誤，因為您的 Kubernetes 叢集會認為磁碟空間已用盡。 若要回收 PVC，請採取下列步驟：

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. 列出您所刪除之伺服器群組的 Pvc
若要列出 Pvc，請執行下列命令：
```console
kubectl get pvc [-n <namespace name>]
```

它會傳回 PVSs 清單，特別是您所刪除之伺服器群組的 Pvc。 例如：
```console
kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
此伺服器群組有8個 Pvc。

### <a name="2-delete-each-of-the-pvcs"></a>2. 刪除每個 Pvc
針對您所刪除之伺服器群組 (協調器和背景工作) ，刪除每個于 postgresql 超大規模節點的資料和記錄 Pvc。
此命令的一般格式為： 
```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

例如：
```console
kubectl delete pvc data-postgres01-0
kubectl delete pvc data-postgres01-1 
kubectl delete pvc data-postgres01-2
kubectl delete pvc data-postgres01-3
kubectl delete pvc logs-postgres01-0
kubectl delete pvc logs-postgres01-1
kubectl delete pvc logs-postgres01-2
kubectl delete pvc logs-postgres01-3
```

這些 kubectl 命令都會確認成功刪除 PVC。 例如：
```console
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>**注意** 如所述，不刪除 Pvc 最後可能會在發生錯誤的情況下取得您的 Kubernetes 叢集。 其中某些錯誤可能包括無法使用 azdata 登入您的 Kubernetes 叢集，因為此儲存問題 (一般 Kubernetes 行為) 。
>
> 例如，您可能會在記錄檔中看到類似以下的訊息：  
    > 批註： microsoft.com/ignore-pod-health： true  
    > 狀態：失敗  
    > 原因：已收回  
    > 訊息：節點資源不足：暫時儲存體。 容器控制器使用的16372Ki 超過其要求0。
    
## <a name="next-step"></a>後續步驟
建立 [Azure Arc 啟用的于 postgresql 超大規模](create-postgresql-hyperscale-server-group.md)
