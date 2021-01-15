---
title: 從受限制的網路連接到 Azure Synapse Analytics Studio 中的工作區資源
description: 本文將告訴您如何從受限制的網路連線到您的工作區資源
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 41403a59be0395a6d9874c7369bfe59c22f5ac17
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218360"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>從受限制的網路連接到工作區資源

假設您是 IT 系統管理員，負責管理您組織的受限網路。 您想要在 Azure Synapse Analytics Studio 和此受限網路內的工作站之間啟用網路連線。 本文將說明如何做到。

## <a name="prerequisites"></a>先決條件

* **Azure 訂** 用帳戶：如果您沒有 azure 訂用帳戶，請在開始前建立 [免費的 azure 帳戶](https://azure.microsoft.com/free/) 。
* **Azure Synapse Analytics 工作區**：您可以從 Azure Synapse Analytics 建立一個。 您需要步驟4中的工作區名稱。
* **受限制的網路**： IT 系統管理員會為組織維護受限的網路，並且擁有設定網路原則的許可權。 您需要在步驟3中的虛擬網路名稱和子網。


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>步驟1：將網路輸出安全性規則新增至受限的網路

您必須使用四個服務標籤新增四個網路輸出安全性規則。 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (這種類型的規則是選擇性的。 只有當您想要與 Microsoft 共用資料時，才將其加入。 ) 

下列螢幕擷取畫面顯示 Azure Resource Manager 輸出規則的詳細資料。

![Azure Resource Manager 服務標記詳細資料的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

當您建立其他三個規則時，請將 **目的地服務標記** 的值取代為清單中的 **AzureFrontDoor 前端**、 **AzureActiveDirectory** 或 **AzureMonitor** 。

如需詳細資訊，請參閱 [服務標記總覽](../../virtual-network/service-tags-overview.md)。

## <a name="step-2-create-private-link-hubs"></a>步驟2：建立私人連結中樞

接下來，從 Azure 入口網站建立私用連結中樞。 若要在入口網站中尋找此資訊，請搜尋 *Azure Synapse Analytics (private link 中樞)*，然後填入所需的資訊加以建立。 

![建立 Synapse private link hub 的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>步驟3：建立 Synapse Studio 的私人端點

若要存取 Azure Synapse Analytics Studio，您必須從 Azure 入口網站建立私人端點。 若要在入口網站中尋找此項，請搜尋 *Private Link*。 在 [ **Private Link 中心**] 中，選取 [ **建立私人端點**]，然後填入所需的資訊加以建立。 

> [!Note]
> 確定 **區域** 值與 Azure Synapse Analytics 工作區的值相同。

![[建立私人端點]、[基本] 索引標籤的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

在 [ **資源** ] 索引標籤上，選擇您在步驟2中建立的私人連結中樞。

![[建立私人端點，資源] 索引標籤的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

**在 [設定**] 索引標籤上： 
* 針對 [ **虛擬網路**]，選取受限制的虛擬網路名稱。
* 針對 [ **子網**]，選取受限制虛擬網路的子網。 
* 針對 [ **與私人 DNS 區域整合**]，選取 **[是]**。

![建立私人端點 [設定] 索引標籤的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

建立私人連結端點之後，您可以存取 Azure Synapse Analytics Studio web 工具的登入頁面。 不過，您還無法存取工作區中的資源。 若要這樣做，您必須完成下一個步驟。

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>步驟4：建立工作區資源的私人端點

若要存取 Azure Synapse Analytics Studio 工作區資源內的資源，您需要建立下列各項：

- 至少一個具有 **目標子資源****開發** 類型的私用連結端點。
- 具有 **Sql** 或 **SqlOnDemand** 類型的其他兩個選擇性私用連結端點，視您要存取的工作區中的資源而定。

建立這些功能類似于您在上一個步驟中建立端點的方式。  

在 [ **資源** ] 索引標籤上：

* 針對 [ **資源類型**]，選取 [ **Synapse]/[工作區**]。
* 針對 [ **資源**]，選取您先前建立的工作區名稱。
* 針對 [ **目標子資源**]，選取端點類型：
  * **Sql 適用** 于 sql 集區中的 SQL 查詢執行。
  * **SqlOnDemand** 適用于 SQL 內建查詢執行。
  * **開發** 用來存取 Azure Synapse Analytics Studio 工作區中的其他專案。 您必須建立至少一個此類型的私用連結端點。

![[建立私人端點、資源] 索引標籤、[工作區] 的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>步驟5：建立工作區連結儲存體的私人端點

若要在 Azure Synapse Analytics Studio 工作區中使用儲存體 explorer 來存取連結的儲存體，您必須建立一個私人端點。 這與步驟3的步驟類似。 

在 [ **資源** ] 索引標籤上：
* 針對 [ **資源類型**]，選取 [ **Synapse]/[storageAccounts**]。
* 針對 [ **資源**]，選取您先前建立的儲存體帳戶名稱。
* 針對 [ **目標子資源**]，選取端點類型：
  * **blob** 適用于 Azure Blob 儲存體。
  * **dfs** 適用于 Azure Data Lake Storage Gen2。

![[建立私人端點]、[資源] 索引標籤、[儲存體] 的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

現在，您可以存取連結的儲存體資源。 在您的虛擬網路中，您可以在 Azure Synapse Analytics Studio 工作區中，使用儲存體 explorer 來存取連結的儲存體資源。

您可以為您的工作區啟用受管理的虛擬網路，如下列螢幕擷取畫面所示：

![[建立 Synapse] 工作區的螢幕擷取畫面，其中已醒目提示 [啟用受控虛擬網路] 選項。](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

如果您想要讓筆記本存取特定儲存體帳戶下的連結儲存體資源，請在您的 Azure Synapse Analytics Studio 下新增受控私人端點。 儲存體帳戶名稱應該是您的筆記本需要存取的名稱。 如需詳細資訊，請參閱為 [您的資料來源建立受控私人端點](./how-to-create-managed-private-endpoints.md)。

建立此端點之後，核准狀態會顯示 [ **擱置** 中] 狀態。 在 Azure 入口網站中此儲存體帳戶的 [ **私人端點** 連線] 索引標籤中，要求核准此儲存體帳戶的擁有者。 核准之後，您的筆記本就可以存取此儲存體帳戶下的連結儲存體資源。

現在已設定。 您可以存取 Azure Synapse Analytics Studio 工作區資源。

## <a name="appendix-dns-registration-for-private-endpoint"></a>附錄：私人端點的 DNS 註冊

如果私人端點建立期間未啟用 [與私人 DNS 區域整合]，如下列螢幕擷取畫面所示，您必須為每個私人端點建立「**私人 dns 區域**」。
![建立 Synapse 私人 DNS 區域1的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

若要在入口網站中尋找 **私人 dns 區域** ，請搜尋 *私人 dns 區域*。 在 **私人 DNS 區域** 中，填入下列必要資訊來建立它。

* 針對 [ **名稱**]，輸入特定私人端點的私人 DNS 區域專用名稱，如下所示：
  * **`privatelink.azuresynapse.net`** 適用于存取 Azure Synapse Analytics Studio 閘道的私人端點。 請參閱在步驟3中建立的這種類型的私人端點。
  * **`privatelink.sql.azuresynapse.net`** 適用于 SQL 查詢在 SQL 集區和內建集區中執行的這類私用端點。 請參閱步驟4中的建立端點。
  * **`privatelink.dev.azuresynapse.net`** 適用于這種類型的私人端點，可存取 Azure Synapse Analytics Studio 工作區中的其他專案。 請參閱步驟4中的這種類型的私人端點建立。
  * **`privatelink.dfs.core.windows.net`** 適用于存取工作區連結 Azure Data Lake Storage Gen2 的私人端點。 請參閱步驟5中建立的這類私人端點。
  * **`privatelink.blob.core.windows.net`** 適用于存取工作區連結 Azure Blob 儲存體的私人端點。 請參閱步驟5中建立的這類私人端點。

![建立 Synapse 私人 DNS 區域2的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

建立 **私人 dns 區域** 之後，請輸入建立的私人 dns 區域，然後選取 **虛擬網路連結** ，將連結新增至您的虛擬網路。 

![建立 Synapse 私人 DNS 區域3的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

填寫必要欄位，如下所示：
* 在 [ **連結名稱**] 中，輸入連結名稱。
* 針對 [ **虛擬網路**]，選取您的虛擬網路。

![建立 Synapse 私人 DNS 區域4的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

新增虛擬網路連結之後，您必須在先前建立的 **私人 dns 區域** 中新增 DNS 記錄集。

* 針對 [ **名稱**]，輸入不同私人端點的專用名稱字串： 
  * **web** 適用于存取 Azure Synapse Analytics Studio 的私人端點。
  * "***YourWorkSpaceName * * _" 適用于 SQL 查詢在 sql 集區中執行的私用端點，也適用于存取 Azure Synapse Analytics Studio 工作區中其他所有專案的私用端點。 _ "*** YourWorkSpaceName *-ondemand * *" 適用于內建集區中的 SQL 查詢執行私用端點。
* 針對 [ **類型**]，選取 [僅限 DNS 記錄類型 **A** ]。 
* 針對 [ **IP 位址**]，輸入每個私人端點的對應 IP 位址。 您可以從私人端點總覽取得 **網路介面** 中的 IP 位址。

![建立 Synapse 私人 DNS 區域5的螢幕擷取畫面。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>後續步驟

深入瞭解 [受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)。

深入瞭解 [受控私人端點](./synapse-workspace-managed-private-endpoints.md)。