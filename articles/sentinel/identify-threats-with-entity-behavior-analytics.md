---
title: 在 Azure Sentinel 中找出使用者和實體行為分析 (UEBA) 的 advanced 威脅 |Microsoft Docs
description: " (使用者、主機名稱、IP 位址) 建立實體的行為基準，並使用它們來偵測異常行為，並識別零天的 advanced 永久性威脅 (APT) 。"
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2020
ms.author: yelevin
ms.openlocfilehash: 657221a2acbf592a56cb4659ced2199023bc5e5b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658786"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中找出使用者和實體行為分析 (UEBA) 的 advanced 威脅

> [!IMPORTANT]
>
> - UEBA 和實體頁面功能現在已在下列 Azure Sentinel 地理位置和區域 **正式推出** ：
>    - 美國地理位置
>    - 歐洲西部區域
>    - 澳大利亞地理位置
>
> - 在所有其他地理位置和區域中，這些功能仍可供 **預覽**。 請參閱 [**Microsoft Azure 預覽的補充使用條款**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>什麼是使用者和實體行為分析 (UEBA) ？

### <a name="the-concept"></a>概念

找出組織內部的威脅及其潛在的影響-無論是遭入侵的實體或惡意的內部人員，都一定會耗費大量時間和人力。 篩選警示、連接點和主動式搜尋全都增加了大量的時間和精力，並以最少的方式來增加，而且複雜的威脅可能只是規避探索。 尤其是難以捉摸的威脅（例如零天、目標和先進的持續性威脅）對您的組織而言可能是最危險的，讓他們的偵測更為重要。

Azure Sentinel 中的 UEBA 功能可消除分析師工作負載的 drudgery，以及工作負載的不確定性，並提供高精確度、可採取行動的智慧，讓他們可以專注于調查和補救。

當 Azure Sentinel 從其所有連接的資料來源收集記錄和警示時，就會加以分析，並為組織的實體建立基準行為設定檔， (使用者、主機、IP 位址、應用程式等，在時間和對等群組範圍內 ) 。 Sentinel 可以使用各種不同的技術和機器學習功能，找出異常活動，協助您判斷資產是否遭到入侵。 不僅如此，還可以找出特定資產的相對敏感度、識別資產的對等群組，並評估任何給定遭盜用資產的潛在影響 (其「爆炸半徑」 ) 。 有了這種資訊，您就可以有效地設定調查和事件處理的優先順序。 

### <a name="architecture-overview"></a>架構概觀

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="實體行為分析架構":::

### <a name="security-driven-analytics"></a>安全性導向分析

根據 Gartner 的 UEBA 解決方案的典範，Azure Sentinel 根據三個參考框架提供「外接」方法：

- **使用案例：** 藉由根據安全性研究來排列相關的攻擊媒介和案例的優先順序，並根據 MITRE ATT&CK 的策略、技術和子技術，將各種實體放在終止鏈中的受害者、perpetrators 或資料點;Azure Sentinel 特別著重于每個資料來源可提供的最有價值記錄檔。

- **資料來源：** 雖然第一次且最重要的是支援 Azure 資料來源，但是 Azure Sentinel thoughtfully 會選取協力廠商資料來源，以提供符合我們威脅案例的資料。

- **分析：** 使用各種機器學習服務 (ML) 演算法，Azure Sentinel 識別異常的活動，並以內容擴充的形式清楚且簡潔地呈現辨識項，如下所示的一些範例。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="行為分析以外的方法":::

Azure Sentinel 提供成品，可協助您的安全性分析師清楚瞭解內容中的異常活動，以及與使用者的基準設定檔相比較。 使用者 (或主機或位址) 所執行的動作會評估內容，其中「true」結果表示已識別異常：
- 跨地理位置、裝置和環境。
- 相較于使用者自己的歷程記錄) ，跨時間和頻率的 horizons (。
- 相較于對等的行為。
- 相較于組織的行為。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="實體內容":::


### <a name="scoring"></a>計分

每個活動都會以「調查優先順序分數」進行評分，以根據使用者及其對等的行為學習，判斷特定使用者執行特定活動的機率。 已識別為最高的活動會以 0-10) 的規模 (獲得最高分的最高分。

請參閱如何在 [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) 中使用行為分析，以瞭解其運作方式的範例。

## <a name="entities-in-azure-sentinel"></a>Azure Sentinel 中的實體

### <a name="entity-identifiers"></a>實體識別碼

當警示傳送至 Azure Sentinel 時，它們會包含 Azure Sentinel 識別和分類為實體的資料元素，例如使用者帳戶、主機、IP 位址和其他專案。 有時，如果警示未包含足夠的實體相關資訊，則此識別可能會是一項挑戰。

例如，您可以使用一種以上的方式來識別使用者帳戶：使用 Azure AD 帳戶的數值識別碼 (GUID) ，或其使用者主體名稱 (UPN) 值，或使用其使用者名稱及其 NT 功能變數名稱的組合。 不同的資料來源可以用不同的方式來識別相同的使用者。 因此，在可能的情況下，Azure Sentinel 會將這些識別碼合併成單一實體，以便能夠正確識別。

不過，您的其中一個資源提供者會建立警示，其中的實體無法充分識別，例如，沒有功能變數名稱內容的使用者名稱。 在這種情況下，使用者實體無法與相同使用者帳戶的其他實例合併，這會識別為個別的實體，而這兩個實體會維持不變而不是統一。

為了將這種情況發生的風險降至最低，您應該確認所有警示提供者都能正確地識別其產生的警示中的實體。 此外，使用 Azure Active Directory 同步處理使用者帳戶實體，可能會建立統一的目錄，讓您能夠合併使用者帳戶實體。

以下是目前在 Azure Sentinel 中識別的實體類型：

- 使用者帳戶 (帳戶) 
- 主機
- IP 位址 (IP) 
- 惡意程式碼
- 檔案
- Process
- 雲端應用程式 (CloudApplication) 
- DNS)  (的功能變數名稱
- Azure 資源
- File (Get-filehash) 
- 登錄機碼
- 登錄值
- 安全性群組
- URL
- IoT 裝置
- Mailbox
- 郵件叢集
- 郵件訊息
- 提交郵件

### <a name="entity-pages"></a>實體頁面

當您在搜尋、警示或調查中遇到目前限制為使用者和主機) 的任何實體 (時，您可以選取實體，然後將其移至 **實體頁面**，這是與該實體相關的實用資訊資料表。 您將在此頁面上找到的資訊類型包括關於實體的基本事實、與此實體相關的值得注意事件時間軸，以及實體行為的見解。
 
實體頁面包含三個部分：
- 左邊的面板包含實體的識別資訊，從資料來源（例如 Azure Active Directory、Azure 監視器、Azure 資訊安全中心和 Microsoft Defender）收集。

- 中央面板會顯示與實體相關之明顯事件的圖形化和文字時間軸，例如警示、書簽和活動。 活動是來自 Log Analytics 的值得注意事件匯總。 偵測這些活動的查詢是由 Microsoft 安全性研究團隊所開發。

- 右側面板會顯示實體的行為見解。 這些深入解析有助於快速找出異常和安全性威脅。 深入解析是由 Microsoft 安全性研究團隊所開發，並且以異常偵測模型為基礎。

### <a name="the-timeline"></a>時間軸

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="實體頁面時間軸":::

時間軸是實體頁面在 Azure Sentinel 中的行為分析貢獻的主要部分。 它提供有關實體相關事件的故事，協助您瞭解實體在特定時間範圍內的活動。

您可以從數個預設選項中選擇 **時間範圍** (例如 [ *過去24小時* ]) ，或將它設定為任何自訂的時間範圍。 此外，您可以設定篩選器，將時間軸中的資訊限制為特定類型的事件或警示。

時間軸包含下列類型的專案：

- 警示-實體定義為 **對應實體** 的任何警示。 請注意，如果您的組織已 [流量分析規則建立自訂警示](./tutorial-detect-threats-custom.md)，您應該確定規則的實體對應已正確完成。

- 書簽-包含頁面上顯示之特定實體的任何書簽。

- 活動-與實體相關的值得注意事件匯總。 
 
### <a name="entity-insights"></a>實體見解
 
Entity insights 是 Microsoft 安全性研究人員所定義的查詢，可協助您的分析師更有效率且更有效地進行調查。 深入解析會顯示為實體頁面的一部分，並以表格式資料和圖表的形式提供主機和使用者的重要安全性資訊。 在這裡提供資訊表示您不需要繞道至 Log Analytics。 深入解析包含有關登入、群組新增、異常事件等的資料，並包含先進的 ML 演算法來偵測異常行為。 深入解析是根據下列資料類型：
- syslog
- SecurityEvent
- 稽核記錄
- 登入記錄
- Office 活動
- BehaviorAnalytics (UEBA)  
 
### <a name="how-to-use-entity-pages"></a>如何使用實體頁面

實體頁面是設計為多個使用案例的一部分，而且可以從 [事件管理]、[調查圖表]、[書簽]，或是直接從 [Azure Sentinel 主功能表的 [ **實體行為分析** ] 下的實體搜尋頁面存取。

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="實體頁面使用案例":::


## <a name="data-schema"></a>資料結構描述

### <a name="behavior-analytics-table"></a>行為分析資料表

| 欄位                     | 說明                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | 租使用者的唯一識別碼                                      |
| SourceRecordId            | EBA 事件的唯一識別碼                                   |
| TimeGenerated             | 活動發生的時間戳記                              |
| TimeProcessed             | 由 EBA 引擎處理的啟用時間戳            |
| ActivityType              | 活動的高層級類別                                 |
| ActionType                | 活動的標準化名稱                                     |
| UserName                  | 起始活動之使用者的使用者名稱                    |
| UserPrincipalName         | 起始活動之使用者的完整使用者名稱               |
| EventSource               | 提供原始事件的資料來源                        |
| SourceIPAddress           | 起始活動的 IP 位址                        |
| SourceIPLocation          | 起始活動的國家/地區，從 IP 位址擴充 |
| SourceDevice              | 起始活動之裝置的主機名稱                  |
| DestinationIPAddress      | 活動目標的 IP 位址                            |
| DestinationIPLocation     | 活動目標的國家/地區，從 IP 位址擴充     |
| DestinationDevice         | 目標裝置的名稱                                           |
| **UsersInsights**         | 相關使用者的內容擴充                            |
| **DevicesInsights**       | 相關裝置的內容擴充                          |
| **ActivityInsights**      | 以我們的分析為基礎的活動相關內容分析              |
| **InvestigationPriority** | 異常分數，介於 0-10 (0 = 良性，10 = 高度異常)          |
|

### <a name="querying-behavior-analytics-data"></a>查詢行為分析資料

使用 [KQL](/azure/data-explorer/kusto/query/)，我們可以查詢行為分析資料表。

例如，如果我們想要尋找使用者無法登入 Azure 資源的所有案例，在此情況下，使用者第一次嘗試從指定的國家/地區進行連線，而該國家/地區的連線即使是使用者的對等，也是不常見的，我們可以使用下列查詢：

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>使用者對等中繼資料-資料表和筆記本

使用者對等的中繼資料提供威脅偵測的重要內容、調查事件，以及搜尋潛在的威脅。 安全性分析師可以觀察使用者對等的一般活動，以判斷使用者的活動是否與他或她的對等活動不尋常。

Azure Sentinel 會根據使用者的 Azure AD 安全性群組成員資格、郵寄清單等來計算和排名使用者的對等，並將對等排名1-20 儲存在 **UserPeerAnalytics** 資料表中。 下列螢幕擷取畫面顯示 UserPeerAnalytics 資料表的架構，並顯示使用者肯德爾 Collins 的前八個排名對等。 Azure Sentinel 使用「 *頻率-反向檔頻率* 」 (TF-IDF) 演算法來正規化計算排名的加權：較小的群組，權數愈高。 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="使用者對等中繼資料資料表的螢幕擷取畫面":::

您可以使用 Azure Sentinel GitHub 存放庫中提供的 [Jupyter 筆記本](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) ，將使用者對等中繼資料視覺化。 如需有關如何使用筆記本的詳細指示，請參閱 [引導式分析-使用者安全性中繼資料](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) 筆記本。

### <a name="permission-analytics---table-and-notebook"></a>許可權分析-資料表和筆記本

許可權分析有助於判斷攻擊者危害組織資產的潛在影響。 這項影響也稱為資產的「群發半徑」。 安全性分析師可以使用此資訊來排列調查和事件處理的優先順序。

Azure Sentinel 藉由評估使用者可以直接存取的 Azure 訂用帳戶，或透過群組或服務主體，來判斷指定使用者對 Azure 資源所持有的直接和可轉移存取權。 這項資訊以及使用者 Azure AD 安全性群組成員資格的完整清單會儲存在 **UserAccessAnalytics** 資料表中。 下列螢幕擷取畫面顯示 [使用者 Alex] Johnson 的 UserAccessAnalytics 資料表中的範例資料列。 **來源實體** 是使用者或服務主體帳戶，而 **目標實體** 是來源實體可存取的資源。 **存取層級** 和 **存取類型** 的值取決於目標實體的存取控制模型。 您可以看到 Alex 具有 Azure 訂用帳戶 *Contoso 旅館租* 使用者的參與者存取權。 訂用帳戶的存取控制模型是 RBAC。   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="使用者存取分析資料表的螢幕擷取畫面":::

您可以使用 [Jupyter 筆記本](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (上述) 從 Azure Sentinel GitHub 存放庫中所述的相同筆記本，將許可權分析資料視覺化。 如需有關如何使用筆記本的詳細指示，請參閱 [引導式分析-使用者安全性中繼資料](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) 筆記本。

### <a name="hunting-queries-and-exploration-queries"></a>搜尋查詢和探索查詢

Azure Sentinel 根據 BehaviorAnalytics 資料表，提供現成可用的一組搜尋查詢、探索查詢和活頁簿。 這些工具會呈現擴充的資料，著重于指出異常行為的特定使用案例。 

深入瞭解 Azure Sentinel 中 [的搜尋和調查圖表](./hunting.md) 。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解 Azure Sentinel 的實體行為分析功能。 如需有關實施的實用指導方針，以及使用您所獲得的見解，請參閱下列文章：

- 在 Azure Sentinel 中[啟用實體行為分析](./enable-entity-behavior-analytics.md)。
- [搜尋安全性威脅](./hunting.md)。