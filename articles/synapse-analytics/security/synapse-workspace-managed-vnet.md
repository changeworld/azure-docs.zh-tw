---
title: Azure Synapse Analytics 中的受控虛擬網路
description: 說明 Azure Synapse Analytics 中受控虛擬網路的文章
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 623b6325b88f42f0076c84a221864762cd3918f9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83645224"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics 受控虛擬網路 (預覽)

本文會說明 Azure Synapse Analytics 中的受控虛擬網路。

## <a name="managed-workspace-vnet"></a>受控工作區 VNet

建立您的 Azure Synapse 工作區時，可以選擇將其與 VNet 產生關聯。 與您的工作區相關聯的 VNet 是由 Azure Synapse 所管理。 此 VNet 稱為「受控工作區 VNet」。

受控工作區 VNet 可以四種方式提供價值：

- 透過受控工作區 VNet，您可以將管理 VNet 的負擔轉至 Azure Synapse。
- 您不需要在自己的 VNet 上設定輸入 NSG 規則，以允許 Azure Synapse 管理進入您 VNet 的流量。 這些 NSG 規則的設定錯誤會導致客戶的服務中斷。
- 您不需要根據尖峰負載來建立 Spark 叢集的子網路。
- 受控工作區 VNet 以及受控私人端點可防止資料外洩。 您只能在具有相關聯受控工作區 VNet 的工作區中建立受控私人端點。

使用與其相關聯的受控工作區 VNet 來建立工作區，可確保您的工作區與其他工作區之間的網路隔離。 Azure Synapse 會在工作區中提供各種分析功能：資料整合、Apache Spark、SQL 集區和 SQL 隨選。

如果您的工作區具有受控工作區 VNet，則系統會在其中部署資料整合和 Spark 資源。 受控工作區 VNet 也會提供 Spark 活動的使用者層級隔離，因為每個 Spark 叢集都在自己的子網路中。

SQL 集區和 SQL 隨選均為多租用戶功能，因此位於受控工作區 VNet 之外。 SQL 集區與 SQL 隨選工作區內部通訊會使用 Azure 私人連結。 建立具有相關聯受控工作區 VNet 的工作區時，系統會自動為您建立這些私人連結。

>[!IMPORTANT]
>建立工作區之後，您就無法變更此工作區組態。 例如，您無法重新設定沒有相關聯受控工作區 VNet 的工作區，並將 VNet 與其建立關聯。 同樣地，您無法重新設定具有相關聯受控工作區 VNet 的工作區，並將 VNet 與其取消關聯。

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>建立具有受控工作區 VNet 的 Azure Synapse 工作區

如果您尚未這麼做，請註冊網路資源提供者。 註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 當您[註冊](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)時，請從資源提供者清單中選擇 [Microsoft.Network]。

若要建立具有相關聯受控工作區 VNet 的 Azure Synapse 工作區，請選取 Azure 入口網站中的 [安全性 + 網路] 索引標籤，然後勾選 [啟用受控虛擬網路] 核取方塊。

如果未勾選此核取方塊，則您的工作區將不會有與其相關聯的 VNet。

>[!IMPORTANT]
>您只能在具有受控工作區 VNet 的工作區中使用私人連結。

![啟用受控工作區 VNet](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>來自受控工作區 VNet 的所有輸出流量 (除了透過受控的私人端點以外) 未來都會遭到封鎖。 建議您建立受控私人端點，以連線到工作區外部的所有 Azure 資料來源。 

您可以從 Azure 入口網站選取**概觀**，以檢查您的 Azure Synapse 工作區是否與受控工作區 VNet 相關聯。

![Azure 入口網站中的 工作區概觀](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>後續步驟

建立 [Azure Synapse 工作區](../quickstart-create-workspace.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)

[建立資料來源的受控私人端點](./how-to-create-managed-private-endpoints.md)
