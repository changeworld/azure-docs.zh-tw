---
title: Service Fabric 受控叢集 (預覽)
description: Service Fabric 受控叢集是 Azure Service Fabric 叢集資源模型的演進，可簡化部署和叢集管理。
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410374"
---
# <a name="service-fabric-managed-clusters-preview"></a>Service Fabric 受控叢集 (預覽)

Service Fabric 受控叢集是 Azure Service Fabric 叢集資源模型的演進，可簡化您的部署和叢集管理體驗。

傳統 Service Fabric 叢集的 Azure 資源模型 (ARM) 範本會要求您將叢集資源定義在數個支援資源的後面，所有這些都必須正確地「連接」(在部署時及整個叢集生命週期中)，叢集和您的服務才能正常運作。 相反地，Service Fabric 受控叢集的封裝模型包含單一、「Service Fabric 受控叢集」資源。 叢集的所有基礎資源都是由 Azure 代表您進行擷取及管理。

**Service Fabric 傳統叢集模型**
![Service Fabric 傳統叢集模型][sf-composition]

**Service Fabric 受控叢集模型**
![Service Fabric 封裝叢集模型][sf-encapsulation]

就大小和複雜度而言，Service Fabric 受控叢集的 ARM 範本大約是 100 行的 JSON，對應於定義一般 Service Fabric 叢集所需的 1000 行：

| Service Fabric 資源 | Service Fabric 受控叢集資源 |
|----------|-----------|
| Service Fabric 叢集 | Service Fabric 受控叢集 |
| 虛擬機器擴展集 | |
| 負載平衡器 | |
| 公用 IP 位址 | |
| 儲存體帳戶 | |
| 虛擬網路 | |

Service Fabric 受控叢集在傳統叢集上提供了許多優點：

**簡化的叢集部署和管理**
- 部署及管理單一 Azure 資源
- 憑證管理及自動輪替
- 簡化的調整作業

**防止作業錯誤**
- 避免與基礎資源的組態不符
- 封鎖不安全的作業 (例如刪除種子節點)

**預設的最佳做法**
- 簡化的可靠性和持久性設定

除了叢集所需的基礎資源成本之外，Service Fabric 受控叢集並不會產生額外的成本。

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric 受控叢集 SKU

「基本」和「標準」SKU 都提供 Service Fabric 受控叢集。

| 特徵 | 基本 | 標準 |
| ------- | ----- | -------- |
| 網路資源 (適用於 [Load Balancer](../load-balancer/skus.md) 的 SKU，[公用 IP](../virtual-network/public-ip-addresses.md)) | 基本 | 標準 |
| 最小節點 (VM 執行個體) 計數 | 3 | 5 |
| 每個節點類型的節點計數上限 | 100 | 100 |
| 節點類型計數上限 | 1 | 20 |
| 新增/移除節點類型 | 否 | 是 |
| 區域備援 | 否 | 是 |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Service Fabric 受控叢集功能藍圖
因為這是 Service Fabric 受控叢集的早期預覽版本，所以有一些差距需要注意。 這些功能將在未來的版本中推出。 

* 直接從 Visual Studio 將應用程式發佈至叢集
* 受控識別 
* ARM 應用程式部署 
* 可用性區域 
* 反向 Proxy 
* 自動調整 
* 正在更新 NSG 規則 
* 自動 OS 升級

## <a name="next-steps"></a>後續步驟

若要開始使用 Service Fabric 受控叢集，請嘗試快速入門：

> [!div class="nextstepaction"]
> [建立 Service Fabric 受控叢集 (預覽)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png