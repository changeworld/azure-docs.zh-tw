---
title: 連接到 Azure Kubernetes Service-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何使用適用於 PostgreSQL 的 Azure 資料庫單一伺服器連接 Azure Kubernetes Service （AKS）
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74769875"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>連接 Azure Kubernetes Service 和適用於 PostgreSQL 的 Azure 資料庫-單一伺服器

Azure Kubernetes Service (AKS) 提供的受控 Kubernetes 叢集可讓您用於 Azure 中。 搭配使用 AKS 和適用於 PostgreSQL 的 Azure 資料庫來建立應用程式時，請考慮下列這些選項。


## <a name="accelerated-networking"></a>加速網路
在 AKS 叢集中使用啟用了加速網路功能的基礎 VM。 在 VM 上啟用加速網路時，可降低延遲、抖動和 VM 上的 CPU 使用率。 深入了解加速網路的運作方式、支援的 OS 版本，以及支援的 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) VM 執行個體。

自 2018 年 11 月起，AKS 即支援在這些支援 VM 執行個體上使用加速網路。 根據預設，使用這些 VM 的新 AKS 叢集會啟用加速網路。

您可以透過下列方式，確認 AKS 叢集是否具有加速網路：
1. 移至 Azure 入口網站，並選取您的 AKS 叢集。
2. 選取 [屬性] 索引標籤。
3. 複製**基礎結構資源群組**的名稱。
4. 使用入口網站搜尋列，以找出並開啟基礎結構資源群組。
5. 選取該資源群組中的 VM。
6. 移至 VM 的 [網路]**** 索引標籤。
7. 確認 [加速網路]**** 是否為「啟用」。

或者，透過 Azure CLI 使用下列兩個命令：
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
輸出將是 AKS 所建立的資源群組，其中包含網路介面。 請取得 "nodeResourceGroup" 名稱，並在下一個命令中加以使用。 **EnableAcceleratedNetworking** 將是 true 或 false：
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA)，可讓您直接從 Kubernetes 或 Cloud Foundry 佈建 Azure 服務。 它是 Azure 的一項 [Open Service Broker API](https://www.openservicebrokerapi.org/) 實作。

使用 OSBA 時，您可以建立適用於 PostgreSQL 的 Azure 資料庫伺服器，並使用 Kubernetes 的原生語言將它繫結至您的 AKS 叢集。 瞭解如何在[OSBA GitHub 頁面](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md)上同時使用 OSBA 和適用於 PostgreSQL 的 Azure 資料庫。 


## <a name="connection-pooling"></a>連線共用
連接共用器可將建立與關閉資料庫新連線的相關時間與成本降至最低。 集區是可重複使用的連接集合。 

有數種連接共用器可以搭配 PostgreSQL 使用。 其中一個是 [PgBouncer](https://pgbouncer.github.io/)。 在 Microsoft Container Registry 中，我們提供輕量級容器化的 PgBouncer，其可用於側車以共用從 AKS 到適用於 PostgreSQL 的 Azure 資料庫連接。 請瀏覽 [docker hub 頁面](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/)，以了解如何存取和使用此映像。 


## <a name="next-steps"></a>後續步驟
-  [建立 Azure Kubernetes Service 叢集](../aks/kubernetes-walkthrough.md)
