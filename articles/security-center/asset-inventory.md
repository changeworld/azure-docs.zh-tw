---
title: Azure 資訊安全中心的資產清查
description: 瞭解 Azure 資訊安全中心的資產管理體驗，以提供所有資訊安全中心受監視資源的完整可見度。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/22/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: d15d73b0f2b87b8e6f66c7bd4e7fb34f6b06e1a0
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341918"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>使用資產清查和管理工具探索及管理資源

Azure 資訊安全中心的資產清查頁面會提供單一頁面，讓您檢視已連線至資訊安全中心的資源有何安全性狀態。 

資訊安全中心會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 然後為您提供如何補救這些弱點的建議。

只要任何資源有未處理的建議，這些建議就會出現在清查中。

您可以使用此視圖及其篩選來解決下列問題：

- 哪些 Azure Defender 啟用的訂用帳戶有未處理的建議？
- 具有「生產」標籤的電腦缺少 Log Analytics 代理程式？
- 使用特定標記標記的電腦有多少個有未處理的建議？
- 特定資源群組中有多少資源有弱點評估服務的安全性結果？

此工具的資產管理可能性很大，而且會持續成長。 

> [!TIP]
> [資產清查] 頁面上的安全性建議與 [ **建議** ] 頁面上的建議相同，但在這裡會根據受影響的資源來顯示。 如需有關如何解決建議的詳細資訊，請參閱 [Azure 資訊安全中心中的執行安全性建議](security-center-recommendations.md)。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|免費|
|必要的角色和權限：|所有使用者|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>資產清查的主要功能有哪些？

[清查] 頁面提供下列工具：

- **摘要** -在您定義任何篩選器之前，清查視圖頂端有一個明顯的值帶，顯示：

    - **總資源**數：連線至「安全性中心」的資源總數。
    - **狀況不良的資源**：具有有效安全性建議的資源。 [深入瞭解安全性建議](security-center-recommendations.md)。
    - 未受**監視的資源**：有代理程式監視問題的資源-已部署 Log Analytics 代理程式，但代理程式未傳送資料或有其他健康情況問題。

- **篩選** -頁面頂端的多個篩選器可讓您根據您嘗試回答的問題，快速地調整資源清單。 例如，如果您想要回答 *具有「生產」標籤之電腦的問題，Log Analytics 代理程式會遺失嗎？* 您可以將 **代理程式監視** 篩選器與 **標記** 篩選器結合，如下列剪輯所示：

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="篩選未受監視的生產資源":::

    套用篩選之後，就會更新摘要值以與查詢結果相關聯。 

- **匯出選項** -清查提供將您所選篩選選項的結果匯出至 CSV 檔案的選項。 此外，您可以將查詢本身匯出到 Azure Resource Graph Explorer，以進一步精簡、儲存或修改 KQL 查詢。

    ![清查的匯出選項](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > KQL 檔提供具有一些範例資料的資料庫，以及一些簡單的查詢，以取得語言的「操作」。 [在本 KQL 教學課程中深入瞭解](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。

- **資產管理選項** -清查可讓您執行複雜的探索查詢。 當您找到符合查詢的資源時，清查會提供作業的快捷方式，例如：

    - 將標籤指派給篩選過的資源-選取您想要標記的資源旁的核取方塊。
    - 將新伺服器上架到安全性中心-使用 [ **新增非 Azure 伺服器** ] 工具列按鈕。
    - 使用 Azure Logic Apps 將工作負載自動化-使用 **觸發程式邏輯應用程式** 按鈕在一或多個資源上執行邏輯應用程式。 您的邏輯應用程式必須事先準備，並 (HTTP 要求) 接受相關的觸發程式類型。 [深入瞭解邏輯應用程式](../logic-apps/logic-apps-overview.md)。


## <a name="how-does-asset-inventory-work"></a>資產清查的運作方式為何？

資產清查利用 [Azure Resource Graph (ARG) ](../governance/resource-graph/index.yml)，這是一項 Azure 服務，可讓您跨多個訂用帳戶查詢安全中心的安全性狀態資料。

ARG 是設計用來提供有效率的資源探索，並能夠大規模查詢。

使用 [Kusto 查詢語言 (KQL) ](/azure/data-explorer/kusto/query/)，資產清查可以透過與其他資源屬性交互參考 ASC 資料，快速產生深入的見解。


## <a name="how-to-use-asset-inventory"></a>如何使用資產清查

1. 從「安全性中心」的側邊欄，選取 [ **清查**]。

1. 使用 [ **依名稱篩選** ] 方塊來顯示特定的資源，或使用如下所述的篩選。

1. 在篩選中選取相關選項，以建立您想要執行的特定查詢。

    :::image type="content" source="./media/asset-inventory/inventory-filters.png" alt-text="篩選未受監視的生產資源" lightbox="./media/asset-inventory/inventory-filters.png":::

    依預設，資源會依使用中的安全性建議數目來排序。

    > [!IMPORTANT]
    > 每個篩選器中的選項都是目前所選訂用帳戶中的資源 **，以及** 您在其他篩選準則中所做選擇的特定選項。
    >
    > 例如，如果您只選取一個訂用帳戶，而且訂用帳戶沒有任何資源具有未處理的安全性建議，可補救 (0 狀況不良的資源) ， **建議** 篩選準則將不會有任何選項。 

1. 若要使用 [ **安全性結果] 包含** 篩選器，請輸入名稱的任意文字、安全性檢查或弱點的 CVE 名稱，以篩選出受影響的資源：

    ![「安全性結果包含」篩選](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **安全性結果包含**和**標記**篩選器只接受單一值。 若要依一個以上篩選，請使用 [ **加入篩選**]。

1. 若要使用 **Azure Defender** 篩選器，請選取 [關閉]、[開啟] 或 [部分])  (的一或多個選項：

    - **Off** -不受 Azure Defender 方案保護的資源。 您可以用滑鼠右鍵按一下這些專案，然後升級這些專案：

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="篩選未受監視的生產資源" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - 受 Azure Defender 方案**保護的資源**
    - **部分** -這 **適用于已** 停用部分但並非所有 Azure Defender 方案的訂用帳戶。 例如，下列訂用帳戶已停用五個 Azure Defender 方案。 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="篩選未受監視的生產資源":::

1. 若要進一步檢查查詢結果，請選取您感興趣的資源。

1. 若要在 Resource Graph Explorer 中以查詢形式來查看目前選取的篩選選項，請 **在 [Resource Graph Explorer] 中選取 [view]**。

    ![ARG 中的清查查詢](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 執行先前定義的邏輯應用程式與 

1. 如果您已定義一些篩選，並將頁面保持開啟狀態，則「安全性中心」不會自動更新結果。 除非您手動重載頁面 **或選取 [** 重新整理]，否則對資源所做的任何變更都不會影響顯示的結果。


## <a name="faq---inventory"></a>常見問題-清查

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>為什麼我的訂用帳戶、電腦、儲存體帳戶等都不會顯示？

清查視圖會從雲端安全性狀態管理中列出您的安全中心連線資源 (CSPM) 的觀點。 篩選不會傳回您環境中的每個資源;只有具有未完成 (或 ' active ' 的專案 ) 建議。 

例如，下列螢幕擷取畫面顯示具有38訂閱存取權的使用者，但目前只有10個建議。 因此，當使用者依 **資源類型 =** 訂用帳戶篩選時，清查中只會顯示具有作用中建議的10個訂用帳戶：

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="篩選未受監視的生產資源":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>為什麼某些資源在 Azure Defender 或代理程式監視資料行中顯示空白值？

並非所有安全中心受監視的資源都有代理程式。 例如，Azure 儲存體帳戶或 PaaS 資源，例如磁片、Logic Apps、Data Lake 分析和事件中樞。

當定價或代理程式監視與資源無關時，這些資料行中將不會顯示任何資料行。

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="篩選未受監視的生產資源":::

## <a name="next-steps"></a>後續步驟

本文描述 Azure 資訊安全中心的 [資產清查] 頁面。

如需相關工具的詳細資訊，請參閱下列頁面：

- [Azure Resource Graph (ARG) ](../governance/resource-graph/index.yml)
- [Kusto 查詢語言 (KQL)](/azure/data-explorer/kusto/query/)