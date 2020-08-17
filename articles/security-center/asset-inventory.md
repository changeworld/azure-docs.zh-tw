---
title: Azure 資訊安全中心的資產清查
description: 瞭解 Azure 資訊安全中心的資產管理體驗，以提供所有資訊安全中心受監視資源的完整可見度。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 7fab15ae4e9e58af58a4490beb2d512379976d1c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263902"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>使用資產清查和管理工具探索及管理您的資源

Azure 資訊安全中心的 [資產清查] 頁面提供單一頁面，可供您用來查看您已連線至「安全性中心」之資源的安全性狀態。 

安全中心會定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，它會提供有關如何補救這些弱點的建議。

當任何資源有未處理的建議時，它們會出現在清查中。

您可以使用此視圖及其篩選來解決下列問題：

- 哪些標準層訂用帳戶有未處理的建議？
- 具有「生產」標籤的電腦缺少 Log Analytics 代理程式？
- 有多少部電腦以特定標記標記有未處理的建議？
- 特定資源群組中有多少資源有弱點評估服務的安全性結果？

此工具的資產管理可能性很大，而且會持續成長。 


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|預覽|
|定價：|免費|
|必要的角色和許可權：|所有使用者|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>資產清查的主要功能有哪些？

[清查] 頁面提供下列工具：

- **摘要** -在您定義任何篩選器之前，清查視圖頂端有一個明顯的值帶，顯示：

    - **總資源**數：連線至「安全性中心」的資源總數。
    - **狀況不良的資源**：具有有效安全性建議的資源。 [深入瞭解安全性建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。
    - 未受**監視的資源**：有代理程式監視問題的資源-已部署 Log Analytics 代理程式，但代理程式未傳送資料或有其他健康情況問題。

- **篩選** -頁面頂端的多個篩選器可讓您根據您嘗試回答的問題，快速地調整資源清單。 例如，如果您想要回答 *具有「生產」標籤之電腦的問題，Log Analytics 代理程式會遺失嗎？* 您可以將 **代理程式監視** 篩選器與 **標記** 篩選器結合，如下列剪輯所示：

    ![篩選未受監視的生產資源](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    套用篩選之後，就會更新摘要值以與查詢結果相關聯。 

- **匯出選項** -清查提供將您所選篩選選項的結果匯出至 CSV 檔案的選項。 此外，您可以將查詢本身匯出到 Azure Resource Graph Explorer，以進一步精簡、儲存或修改 KQL 查詢。

    ![清查的匯出選項](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > KQL 檔提供具有一些範例資料的資料庫，以及一些簡單的查詢，以取得語言的「操作」。 [在本 KQL 教學課程中深入瞭解](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。

- **資產管理選項** -清查可讓您執行複雜的探索查詢。 當您找到符合查詢的資源時，清查會提供作業的快捷方式，例如：

    - 將標籤指派給篩選過的資源-選取核取方塊與您想要標記的資源
    - 將新伺服器上架到安全性中心-使用 [ **新增非 Azure 伺服器** ] 工具列按鈕


## <a name="how-does-asset-inventory-work"></a>資產清查的運作方式為何？

資產清查利用 [Azure Resource Graph (ARG) ](https://docs.microsoft.com/azure/governance/resource-graph/)，這是一項 Azure 服務，可讓您跨多個訂用帳戶查詢安全中心的安全性狀態資料。

ARG 是設計用來提供有效率的資源探索，並能夠大規模查詢。

使用 [Kusto 查詢語言 (KQL) ](https://docs.microsoft.com/azure/data-explorer/kusto/query/)，資產清查可以透過與其他資源屬性交互參考 ASC 資料，快速產生深入的見解。


## <a name="how-to-use-asset-inventory"></a>如何使用資產清查

1. 從「安全性中心」的側邊欄，選取 [ **清查**]。

1. （選擇性）若要顯示特定資源，請在 [ **依名稱篩選** ] 方塊中輸入名稱。

1. 在篩選中選取相關選項，以建立您想要執行的特定查詢。

    ![清查的篩選](./media/asset-inventory/inventory-filters.png)

    依預設，資源會依使用中的安全性建議數目來排序。

    > [!IMPORTANT]
    > 每個篩選器中的選項都是目前所選訂用帳戶中的資源 **，以及** 您在其他篩選準則中所做選擇的特定選項。
    >
    > 例如，如果您只選取一個訂用帳戶，而且訂用帳戶沒有任何資源具有未處理的安全性建議，可補救 (0 狀況不良的資源) ， **建議** 篩選準則將不會有任何選項。 

1. 若要使用 [ **安全性結果] 包含** 篩選器，請輸入名稱的任意文字、安全性檢查或弱點的 CVE 名稱，以篩選出受影響的資源：

    ![「安全性結果包含」篩選](./media/asset-inventory/security-findings-contain-elements.png)

1. 若要使用 **定價層** 篩選，請選取一或多個選項， (免費、部分或標準) ：

    - **免費** -免費定價層上的資源
    - **標準** -標準定價層上的資源
    - **部分** -這適用于標準定價層的訂用帳戶，但已停用一些選用的安全性方案。 例如，下列訂用帳戶位於標準層，但已停用標準層的五個元素。 

        ![標準 (部分) 定價層的訂用帳戶](./media/asset-inventory/pricing-tier-partial.png)

1. 若要進一步檢查查詢結果，請選取您感興趣的資源。

1. （選擇性）在 [ **resource graph explorer]** 中選取 [View]，以在 Resource Graph explorer 中開啟查詢。

    ![ARG 中的清查查詢](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 如果您已定義一些篩選，並將頁面保持開啟狀態，則「安全性中心」不會自動更新結果。 除非您手動重載頁面 **或選取 [** 重新整理]，否則對資源所做的任何變更都不會影響顯示的結果。


## <a name="faq---inventory"></a>常見問題-清查

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>為什麼我的訂用帳戶、電腦、儲存體帳戶等都不會顯示？

清查視圖會列出雲端安全性狀態管理 (CSPM) 觀點的資源。 篩選不會傳回您環境中的每個資源;只有具有未完成 (或 ' active ' 的專案 ) 建議。 

例如，如果您有九個訂用帳戶，但目前只有八個建議，則當您依 **資源類型 =** 訂用帳戶進行篩選時，您只會看到具有有效建議的八個訂用帳戶：

![未在沒有作用中建議時傳回所有的子活動](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>為什麼我的某些資源會在 [定價] 或 [代理程式監視] 資料行中顯示空白值？

並非所有安全中心受監視的資源都有代理程式。 例如，Azure 儲存體帳戶或 PaaS 資源，例如磁片、Logic Apps、Data Lake 分析和事件中樞。

當定價或代理程式監視與資源無關時，這些資料行中將不會顯示任何資料行。

![某些資源會在代理程式監視或定價資料行中顯示空白資訊](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>後續步驟

本文描述 Azure 資訊安全中心的 [資產清查] 頁面。

如需相關工具的詳細資訊，請參閱下列頁面：

- [Azure Resource Graph (ARG) ](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Kusto 查詢語言 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)