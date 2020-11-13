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
ms.openlocfilehash: d94ee3145fb073dae982019fd4096cc2ceb7cd86
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578326"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>從受限的網路連接到 Synapse Studio 工作區資源

本文的目標讀者是管理公司受限網路的公司 IT 系統管理員。 IT 系統管理員即將在 Azure Synapse Studio 與此受限網路內的工作站之間啟用網路連線。

在本文中，您將瞭解如何從受限制的網路環境連接到您的 Azure Synapse 工作區。 

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂** 用帳戶：如果您沒有 azure 訂用帳戶，請在開始前建立 [免費的 azure 帳戶](https://azure.microsoft.com/free/) 。
* **Azure Synapse 工作區** ：如果您沒有 Synapse Studio，請從 Azure Synapse Analytics 建立 Synapse 工作區。 下列步驟4將需要工作區名稱。
* **受限制的網路** ：受限制的網路由公司 IT 系統管理員維護。 it 系統管理員有權設定網路原則。 下列步驟3將需要虛擬網路名稱和其子網。


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>步驟1：將網路輸出安全性規則新增至受限的網路

您必須使用四個服務標籤新增四個網路輸出安全性規則。 深入瞭解 [服務標記總覽](/azure/virtual-network/service-tags-overview) 
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

## <a name="step-3-create-private-endpoint-for-synapse-studio-gateway"></a>步驟3：建立 Synapse Studio 閘道的私人端點

若要存取 Synapse Studio 閘道，您必須從 Azure 入口網站建立私人端點。 透過 Azure 入口網站搜尋「 **Private Link** 」。 選取 [ **Private Link 中心** ] 中的 [ **建立私人端點** ]，然後填入所需的欄位並加以建立。 

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

## <a name="step-4-create-private-endpoints-for-synapse-studio-workspace-resource"></a>步驟4：建立 Synapse Studio 工作區資源的私人端點

若要存取 Synapse Studio 工作區資源內的資源，您必須建立至少一個具有「 **目標子資源** 」類型 **Dev** 的私人連結端點，以及兩個類型為「 **Sql** 」或「 **SqlOnDemand** 」的選擇性私用連結端點，取決於您想要存取的 Synapse Studio 工作區中的哪些資源。 此 Synapse studio 工作區的私人連結端點建立類似于上面建立的端點。  

在 [ **資源** ] 索引標籤中注意以下區域：
* 在 [ **資源類型** ] 中選取 [ **Synapse]/[工作區** ]。
* 選取 [ **YourWorkSpaceName** ] 至您先前建立的 [ **資源** ]。
* 選取 [ **目標子資源** ] 中的端點類型：
  * **Sql** ：適用于 sql 集區中的 SQL 查詢執行。
  * **SqlOnDemand** ：適用于 SQL 內建查詢執行。
  * **Dev** ：適用于存取 Synapse Studio 工作區中的其他專案。 您至少必須使用此類型來建立的 private link 端點。

![正在建立 Synapse studio 工作區的私人端點](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-synapse-studio-workspace-linked-storage"></a>步驟5：建立 Synapse Studio 工作區連結儲存體的私人端點

若要使用 Synapse Studio 工作區中的儲存體 explorer 來存取連結的儲存體，您必須使用上述步驟3中的相同步驟來建立一個私人端點。 

在 [ **資源** ] 索引標籤中注意以下區域：
* 在 [ **資源類型** ] 中選取 " **Synapse/storageAccounts** "。
* 選取 [ **YourWorkSpaceName** ] 至您先前建立的 [ **資源** ]。
* 選取 [ **目標子資源** ] 中的端點類型：
  * **blob** ：適用于 Azure blob 儲存體。
  * **dfs** ：適用于 Azure Data Lake Storage Gen2。

![建立 Synapse studio 工作區連結儲存體的私人端點](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

現在，您可以從 vNet 內 Synapse Studio 工作區中的儲存體 explorer 存取連結的儲存體資源。

如果您的工作區在建立工作區期間有「 **啟用受控虛擬網路** 」，如下所示。

![建立 Synapse studio 工作區連結儲存體的私人端點1](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

您希望您的筆記本能夠在特定儲存體帳戶下存取連結的儲存體資源，您需要在 Synapse Studio 下新增「 **受控私人端點** 」。 「 **儲存體帳戶名稱** 」應該是您筆記本需要存取的名稱。 瞭解如何在您的 [資料來源中建立受控私人端點](./how-to-create-managed-private-endpoints.md)的詳細步驟。

建立此端點之後，[ **核准狀態** ] 將會是 [ **擱置** 中]，您需要要求此儲存體帳戶的擁有者，才能在 Azure 入口網站的此儲存體帳戶的 [ **私人端點** 連線] 索引標籤中核准它。 核准之後，您的筆記本將能夠存取此儲存體帳戶下的連結儲存體資源。

現在已設定。 您可以存取 Synapse studio 工作區資源。

## <a name="next-steps"></a>後續步驟

深入瞭解 [受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)

深入了解[受控私人端點](./synapse-workspace-managed-private-endpoints.md)
