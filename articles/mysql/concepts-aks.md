---
title: 連接到 Azure Kubernetes Service-適用於 MySQL 的 Azure 資料庫
description: 了解如何連接 Azure Kubernetes Service 與適用於 MySQL 的 Azure 資料庫
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537273"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>連接 Azure Kubernetes Service 與適用於 MySQL 的 Azure 資料庫

Azure Kubernetes Service (AKS) 提供的受控 Kubernetes 叢集可讓您用於 Azure 中。 搭配使用 AKS 和適用於 MySQL 的 Azure 資料庫來建立應用程式時，請考慮下列這些選項。


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

使用 OSBA 時，您可以建立適用於 MySQL 的 Azure 資料庫伺服器，並使用 Kubernetes 的原生語言將它繫結至您的 AKS 叢集。 請參閱 [OSBA GitHub 頁面](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md)，以深入了解如何搭配使用 OSBA 與適用於 MySQL 的 Azure 資料庫。 



## <a name="next-steps"></a>後續步驟
- [建立 Azure Kubernetes Service 叢集](../aks/kubernetes-walkthrough.md)
- 了解如何[使用適用於 MySQL 的 OSBA 與 Azure 資料庫從 Helm 圖表安裝 WordPress](../aks/integrate-azure.md)
