---
title: 合作夥伴中心內商業 marketplace 分析的常見問題和術語
description: 瞭解如何處理商業 marketplace 分析的相關常見問題。 包含適用于分析術語的資料字典。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: b7367e58de818c20723c02a6763b1bf1e3b18f24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81251821"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>商業 marketplace 分析的常見問題與術語

本文說明合作夥伴中心分析訊息的常見問題，同時也提供分析術語的字典。

## <a name="frequently-asked-questions"></a>常見問題集

本節針對合作夥伴中心內**沒有可用的分析**訊息，提供常見問題的解答。

**我無法在合作夥伴中心觀看分析資料。當我存取這些頁面時，會看到下列訊息。為什麼要這麼做？**

![您的供應專案尚未提供任何資料](./media/analytics-faq-no-data.png)

為什麼會收到此訊息：

- Marketplace 中發佈的供應專案目前沒有任何可供購買。 這可能表示您的供應專案已上線，並可在產品顯示頁面中取得客戶的觀點，但客戶尚未採取行動來購買及部署這些服務。
- 您的供應專案發佈可能正在進行中，而且尚未上線。 只有 live 供應專案可由客戶取得。 若要檢查供應專案的狀態，請參閱 [[分析] 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)中的 [總覽]。 如需詳細資訊，請參閱[商業 marketplace 分析中的摘要儀表板](./summary-dashboard.md)。
- 您的供應專案可能會列為「**聯絡我**」，也就是僅限清單的供應專案，而且無法由 marketplace 中的客戶購買。 雖然這些供應專案會產生潛在客戶並與您共用，但不會為這些供應專案建立訂單，因為他們無法購買。 若要檢查您的供應專案清單類型，請移至 [設定] 頁面。

**我知道我有分析資料，但出現下列訊息：**

![指定的日期範圍沒有任何資料](./media/analytics-faq-data-range.png)

如果您收到此訊息，表示您有分析資料，但您選取的日期範圍沒有資料。 請選取不同的日期範圍或自訂的日期範圍，以在2010之後查看任何資料。 如需詳細資訊，請參閱[商業 marketplace 分析中 [摘要] 儀表板](./summary-dashboard.md)的 [日期範圍] 區段。

## <a name="dictionary-of-data-terms"></a>資料詞彙的字典

| 屬性名稱 | 報表 | 定義|
|---|---|---|
| Azure 授權類型 | Customer、Order | 客戶用來購買 Azure 的授權合約類型。 也稱為通道 |
| Azure 授權類型：雲端解決方案提供者 | Customer、Order | 終端客戶透過其雲端解決方案提供者 (作為您的轉銷商) 取得 Azure 和您的 Marketplace 供應項目。|
| Azure 授權類型：企業 | Customer、Order | 終端客戶透過 Enterprise 合約 (直接與 Microsoft 簽署) 取得 Azure 和您的 Marketplace 供應項目。|
| Azure 授權類型：透過轉銷商的企業  | Customer、Order | 終端客戶透過協助其與 Microsoft Enterprise 合約的轉銷商，取得 Azure 和您的 Marketplace 供應專案。|  |
| Azure 授權類型：隨用隨付| Customer、Order | 終端客戶透過「隨用隨付」合約取得 Azure 和您的 Marketplace 供應專案，並直接與 Microsoft 簽署。||
| 雲端執行個體名稱| 單| 發生 VM 部署的 Microsoft Cloud。||
| 雲端實例名稱： Azure 全域| 單| 公用的全球 Microsoft 雲端。|| |
| 雲端實例名稱： Azure Government | 單| 政府專屬 Microsoft 雲端，適用于下列政府之一：中國、德國或美國地區。| |
| 客戶縣/市| 客戶| 客戶提供的城市名稱。 城市可能與客戶的 Azure 訂用帳戶中的城市不同。||
| 客戶通訊語言  | 客戶| 客戶慣用於通訊的語言。||
| 客戶公司名稱 | Customer、Order | 客戶提供的公司名稱。 名稱可能與客戶的 Azure 訂用帳戶中的城市不同。|  |
| 客戶國家/地區 | Customer、Order | 客戶提供的國家/地區名稱。 國家（地區）可能會與客戶 Azure 訂用帳戶中的國家/地區不同。|  |
| 客戶電子郵件| 客戶| 終端客戶所提供的電子郵件地址。 電子郵件可能與客戶的 Azure 訂用帳戶中的電子郵件地址不同。||
| 客戶名字| 客戶| 客戶提供的名稱。 名稱可能與客戶的 Azure 訂用帳戶中提供的名稱不同。| |
| 客戶識別碼 | Customer、Order | 指派給客戶的唯一識別碼。 客戶可能會有零或多個 Azure Marketplace 訂用帳戶。|  |
| 客戶郵遞區號  | 客戶| 客戶所提供的郵遞區號。 程式碼可能不同于客戶的 Azure 訂用帳戶中提供的郵遞區號。| |
| 客戶州| 客戶| 客戶提供的狀態（位址）。 狀態可能與客戶的 Azure 訂用帳戶中提供的狀態不同。| |
| 取得日期| 客戶| 客戶購買您所發佈之任何供應專案的第一個日期。| |
| 損失日期| 客戶| 客戶最近一次已購買的所有供應專案的最後一個日期。||
| 是新客戶  | 單| 此值將會識別一或多個新客戶第一次取得您的供應專案（或非）。 如果在同一個日曆月份內「取得日期」，值會是「是」。 如果客戶已在行事曆月份之前購買任何供應專案，值將會是「否」。 |
| 為預覽 SKU| 單| 此值可讓您知道您是否已將 SKU 標記為「預覽」。 如果 SKU 已適當標記，則值為 "Yes"，而且只有您所授權的 Azure 訂用帳戶可以部署和使用此映射。 如果 SKU 尚未識別為 "preview"，則值為 "No"。  |
| 促銷連絡人是否加入宣告| 客戶| 此值可讓您知道客戶是否主動加入宣告來自發行者的促銷連絡人。 目前，我們沒有向客戶顯示該選項，因此我們一律指出 [否]。 一旦部署了此功能，我們就會開始據以更新。|
| Marketplace 授權類型| 單| Marketplace 供應項目的計費方法。||
| Marketplace 授權類型：透過 Azure 計費| 單| Microsoft 是您對於此 Marketplace 供應項目的代理人，並且會代替您向客戶收取費用。 (PAYG 信用卡或企業發票)||
| Marketplace 授權類型：自備授權 | 單| VM 需要客戶提供的授權金鑰來進行部署。 Microsoft 不會向客戶收取透過 marketplace 以這種方式列出供應專案的費用。||
| Marketplace 授權類型：免費| 單| 供應專案已設定為可供所有使用者免費。 Microsoft 不會針對此供應專案的使用量向客戶收取費用。||
| Marketplace 授權類型：Microsoft 為轉銷商  | 單| Microsoft 是您此 Marketplace 供應項目的轉銷商。|  |
| Marketplace 訂用帳戶識別碼 | Customer、Order | 與客戶用來購買 Marketplace 供應專案的 Azure 訂用帳戶相關聯的唯一識別碼。 識別碼先前是 Azure 訂用帳戶 GUID。||
| 供應項目名稱  | 單| Marketplace 供應專案的名稱。|| |
| 供應項目類型  | 單| Microsoft Marketplace 供應專案的類型。|||
| 供應項目類型：受控應用程式  | 即可 | 當需要下列條件時，請使用 Azure 應用程式：受控應用程式供應專案類型：您會使用 VM 或整個 IaaS 架構解決方案，為您的客戶部署訂用帳戶型解決方案。 您或您的客戶要求解決方案必須由合作夥伴管理。 |
| 供應專案類型： Azure 應用程式| 即可 | 當您的解決方案需要簡單的 VM 以外的額外部署和設定自動化時，請使用 Azure 應用程式解決方案範本供應專案類型。||
| 供應專案類型：諮詢服務| 單| Azure Marketplace 中的諮詢服務可協助連結客戶與服務，以支援並擴大客戶對 Azure 的使用。| |
| 供應專案類型：容器 | 單| 當您的解決方案是佈建成 Kubernetes 型 Azure 容器服務的 Docker 容器映像時，請使用「容器」供應項目類型。||
| 供應專案類型： Dynamics 365 Business Central| 單| 當您的解決方案與 Dynamics 365 整合以進行財務和作業時，請使用此供應專案類型| |
| 供應專案類型： Dynamics 365 for Customer Engagement | 單| 當您的解決方案與 Dynamics 365 for Customer Engagement 整合時，請使用此供應專案類型。||
| 供應專案類型： IoT Edge 模組 | 單| Azure IoT Edge 模組是 IoT Edge 所管理的最小計算單位，並可包含 Microsoft 服務（例如 Azure 串流分析）、協力廠商服務，或您自己的解決方案特定程式碼。 |
| 供應專案類型： Power BI 應用程式 | 單| 當您部署與 Power BI 整合的應用程式時，請使用 Power BI 應用程式供應專案類型。|  |
| 供應專案類型： SaaS 應用程式| 單| 您可以使用「SaaS 應用程式」供應項目類型，讓客戶以訂用帳戶的形式購買您的 SaaS 型技術解決方案。||
| 供應專案類型：虛擬機器 | 單| 當您要將虛擬設備部署到與客戶相關的訂用帳戶時，請使用「虛擬機器」供應項目類型。||
| 供應專案類型： Visual Studio Marketplace 延伸模組  | 單| 供應專案類型先前可供 Azure DevOps 延伸模組開發人員使用。 未來 Azure DevOps 延伸模組開發人員可以直接將其延伸模組銷售給客戶。 擴充功能供應專案可以設定為付費或包含試用版。 |
| 訂單取消日期| 單| Marketplace 訂單取消日期。||
| 訂單識別碼| 單| Marketplace 服務客戶訂單的唯一識別碼。 以虛擬機器使用量為基礎的供應專案未與訂單相關聯。| |
| 訂單購買日期| 單| 「Marketplace 訂單」建立日期。|||
| 訂單狀態| 單| 在資料上次重新整理時，Marketplace 的狀態。|     |
| 訂單狀態：有效  | 單| 客戶已購買訂單，但尚未取消其訂單。|         |
| 訂單狀態：已取消 | 單| 客戶先前已購買訂單，並在後續取消訂單。||
| 提供者電子郵件| 客戶| 與 Microsoft 與終端客戶之間關係相關之提供者的電子郵件地址。 如果客戶是透過轉銷商的企業，這會是轉銷商。 如果涉及雲端解決方案提供者（CSP），這會是 CSP。|
| Provider Name| 客戶| 與 Microsoft 和終端客戶之間關係相關的提供者名稱。 如果客戶是透過轉銷商的企業，這會是轉銷商。 如果涉及雲端解決方案提供者（CSP），這會是 CSP。|
| SKU| 單| 發佈期間定義的 SKU 名稱。 供應項目可以有多個 SKU，但 SKU 只能與單一供應項目相關聯。||
| 試用結束日期| 單| 此訂單將結束或已結束的日期。||

## <a name="next-steps"></a>後續步驟

- 如需合作夥伴中心商業 marketplace 中可用分析報告的總覽，請參閱[合作夥伴中心的商用 Marketplace 分析](./analytics.md)。
- 如需摘要說明您供應專案 marketplace 活動之匯總資料的圖形、趨勢和值，請參閱[商業 marketplace 分析中的摘要儀表板](./summary-dashboard.md)。
- 如需以圖形化和可下載格式顯示訂單的相關資訊，請參閱[商用 marketplace 分析中的訂單儀表板](./orders-dashboard.md)。
- 若為虛擬機器（VM）提供使用量和計量付費計量，請參閱[商業 marketplace 分析中的使用量儀表板](./usage-dashboard.md)。
- 如需客戶的詳細資訊，包括成長趨勢，請參閱[商業 marketplace 分析中的客戶儀表板](./customer-dashboard.md)。
- 如需過去30天內的下載要求清單，請參閱[商用 marketplace 分析中的下載儀表板](./downloads-dashboard.md)。
- 若要查看有關 Azure Marketplace 和 AppSource 供應專案的客戶意見反應的匯總觀點，請參閱[商業 Marketplace 分析中的評等和評論儀表板](./ratings-reviews.md)。
