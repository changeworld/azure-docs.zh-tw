---
title: 從受限的網路連接到 Synapse Studio 工作區資源
description: 本文將告訴您如何從受限制的網路連線到您的 Azure Synapse Studio 工作區資源
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 5d28b8f2ff3045c9fdf5e8a866419a22bfbc6504
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321770"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>從受限的網路連接到 Synapse Studio 工作區資源

本文的目標讀者是管理公司受限網路的公司 IT 系統管理員。 IT 系統管理員即將在 Azure Synapse Studio 與此受限網路內的工作站之間啟用網路連線。

在本文中，您將瞭解如何從受限制的網路環境連接到您的 Azure Synapse 工作區。 

## <a name="prerequisites"></a>必要條件

* **Azure 訂** 用帳戶：如果您沒有 azure 訂用帳戶，請在開始前建立 [免費的 azure 帳戶](https://azure.microsoft.com/free/) 。
* **Azure Synapse 工作區** ：如果您沒有 Synapse Studio，請從 Azure Synapse Analytics 建立 Synapse 工作區。 下列步驟4將需要工作區名稱。
* **受限制的網路** ：受限制的網路由公司 IT 系統管理員維護。 it 系統管理員有權設定網路原則。 下列步驟3將需要虛擬網路名稱和其子網。



## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>步驟1：將網路輸出安全性規則新增至受限的網路

您必須使用四個服務標籤新增四個網路輸出安全性規則。 深入瞭解 [服務標記總覽](/azure/virtual-network/service-tags-overview.md) 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (選擇性。 只有當您想要將資料與 Microsoft 共用時，才新增此類型的規則。 ) 

**Azure Resource Manager** 輸出規則詳細資料，如下所示。 當您建立其他三個規則時，請從下拉式選單中選擇服務標籤名稱 " **AzureFrontDoor** "、" **AzureActiveDirectory** "、" **AzureMonitor** "，以取代 " **Destination service tag** " 的值。

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>步驟2：建立 Azure Synapse Analytics (private link 中樞) 

您必須從 Azure 入口網站建立 Azure Synapse Analytics (private link 中樞) 。 透過 Azure 入口網站搜尋「 **Azure Synapse Analytics (private link hub)** 」，然後填入所需的欄位並加以建立。 

> [!Note]
> 此區域應該與您的 Synapse 工作區所在的區域相同。

![正在建立 Synapse Analytics private link 中樞](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-link-endpoint-for-synapse-studio-gateway"></a>步驟3：建立 Synapse Studio 閘道的 private link 端點

若要存取 Synapse Studio 閘道，您必須從 Azure 入口網站建立私人連結端點。 透過 Azure 入口網站搜尋「 **Private Link** 」。 選取 [ **Private Link 中心** ] 中的 [ **建立私人端點** ]，然後填入所需的欄位並加以建立。 

> [!Note]
> 此區域應該與您的 Synapse 工作區所在的區域相同。

![建立 Synapse studio 1 的私人端點](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

在 [ **資源** ] 的下一個索引標籤中，選擇私人連結中樞（在上述步驟2中建立）。

![建立 Synapse studio 2 的私人端點](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

在 [ **設定] 的** 下一個索引標籤中， 
* 選擇您的「 **虛擬網路** 」限制的虛擬網路名稱。
* 選擇 [ **子網** ] 受限制虛擬網路的子網。 
* 選取 [ **是** ] 以「 **與私人 DNS 區域整合** 」。

![建立 Synapse studio 3 的私人端點](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

建立私人連結端點之後，您可以存取 Synapse studio web 工具的登入頁面。 不過，您無法存取 Synapse 工作區中的資源，直到您需要完成下一個步驟。

## <a name="step-4-create-private-link-endpoints-for-synapse-studio-workspace-resource"></a>步驟4：建立 Synapse Studio 工作區資源的私人連結端點

若要存取 Synapse Studio 工作區資源內的資源，您必須建立至少一個具有「 **目標子資源** 」類型 **Dev** 的私人連結端點，以及兩個類型為「 **Sql** 」或「 **SqlOnDemand** 」的選擇性私用連結端點，取決於您想要存取的 Synapse Studio 工作區中的哪些資源。 此 Synapse studio 工作區的私人連結端點建立類似于上面建立的端點。  

在 [ **資源** ] 索引標籤中注意以下區域：
* 在 [ **資源類型** ] 中選取 [ **Synapse]/[工作區** ]。
* 選取 [ **YourWorkSpaceName** ] 至您先前建立的 [ **資源** ]。
* 選取 [ **目標子資源** ] 中的端點類型：
  * **Sql** ：適用于 sql 集區中的 SQL 查詢執行。
  * **SqlOnDemand** ：適用于 SQL 內建查詢執行。
  * **Dev** ：適用于存取 Synapse Studio 工作區中的其他專案。 您至少必須使用此類型來建立的 private link 端點。

![正在建立 Synapse studio 工作區的私人端點](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)

現在已設定。 您可以存取 Synapse studio 工作區資源。

## <a name="next-steps"></a>後續步驟

深入瞭解 [受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)
