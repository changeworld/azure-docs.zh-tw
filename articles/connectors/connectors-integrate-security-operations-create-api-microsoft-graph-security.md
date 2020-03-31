---
title: 集成和管理 Microsoft 圖形安全&安全操作
description: 使用 Microsoft 圖形安全& Azure 邏輯應用改進應用的威脅保護、檢測和回應
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598828"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>使用 Microsoft Graph 安全性和 Azure Logic Apps 整合安全性作業，改善威脅防護功能

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和 [Microsoft Graph 安全性](https://docs.microsoft.com/graph/security-concept-overview)連接器，您就可以建立整合 Microsoft 安全性產品、服務和合作夥伴的自動化工作流程，改善應用程式偵測、保護和回應威脅的方式。 例如，您可以建立 [Azure 資訊安全中心劇本](../security-center/security-center-playbooks.md)，用來監視和管理 Microsoft Graph 安全性實體 (例如警告)。 以下是 Microsoft 圖形安全連線器支援的一些方案：

* 依據查詢或警示識別碼取得警示。 例如，您可以取得包含高嚴重性警示的清單。

* 更新警示。 例如，您可以更新警示指派、新增警示的註解或標記警示。

* 建立[警示訂閱 (Webhook)](https://docs.microsoft.com/graph/api/resources/webhooks) 監視警示建立或變更的時間。

* 管理您的警示訂閱。 例如，您可以取得有效的訂閱、延長訂閱的到期時間，或刪除訂閱。

您的邏輯應用程式工作流程可使用從 Microsoft Graph 安全性連接器取得回應的動作，並讓該輸出提供給工作流程中的其他動作使用。 您也可以讓您工作流程中的其他動作使用來自 Microsoft Graph 安全性連接器動作的輸出。 例如，如果您透過 Microsoft Graph 安全性連接器取得高嚴重性警示，您可以使用 Outlook 連接器將那些警示以電子郵件訊息傳送。 

若要深入了解 Microsoft Graph 安全性，請參閱 [Microsoft Graph 安全性 API 概觀](https://aka.ms/graphsecuritydocs)。 如果您是邏輯應用的新功能，請查看[什麼是 Azure 邏輯應用？](../logic-apps/logic-apps-overview.md) 如果您正在尋找 Microsoft 流或 PowerApps，請參閱[什麼是流？](https://flow.microsoft.com/) [What is PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 若要使用 Microsoft Graph 安全性連接器，您必須擁有*明確提供*的 Azure Active Directory (AD) 租用戶系統管理員同意，這是 [Microsoft Graph 安全性驗證需求](https://aka.ms/graphsecurityauth)的一部分。 這項同意需要 Microsoft Graph 安全性連接器的應用程式識別碼和名稱，您也可以在 [Azure 入口網站](https://portal.azure.com)中找到：

  | 屬性 | 值 |
  |----------|-------|
  | **應用程式名稱** | `MicrosoftGraphSecurityConnector` |
  | **應用程式 ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  要授予連接器的同意，Azure AD 租戶管理員可以執行以下步驟：

  * [為 Azure AD 應用程式授與租用戶系統管理員同意](../active-directory/develop/v2-permissions-and-consent.md)。

  * 在您的邏輯應用程式第一次執行時，您的應用程式可透過[應用程式同意體驗](../active-directory/develop/application-consent-experience.md)要求 Azure AD 租用戶系統管理員的同意。
   
* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 邏輯應用程式即為您想要存取 Microsoft Graph 安全性實體 (例如警示) 的位置。 要使用 Microsoft 圖形安全觸發器，您需要一個空白的邏輯應用。 要使用 Microsoft 圖形安全操作，您需要一個邏輯應用，該應用以方案的適當觸發器開頭。

## <a name="connect-to-microsoft-graph-security"></a>連線至 Microsoft Graph 安全性 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com/)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 對於空白邏輯應用，在添加 Microsoft 圖形安全操作之前，添加觸發器和所需的任何其他操作。

   -或-

   對於現有邏輯應用，在要添加 Microsoft 圖形安全操作的最後一步下，選擇 **"新建步驟**"。

   -或-

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號 （+），然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入「Microsoft Graph 安全性」作為篩選條件。 從 [動作] 清單中，選取您想要的動作。

1. 使用您的 Microsoft Graph 安全性認證登入。

1. 為您選取的動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

## <a name="add-triggers"></a>添加觸發器

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次觸發觸發器時，邏輯應用引擎都會創建一個邏輯應用實例，並開始運行應用的工作流。

> [!NOTE] 
> 觸發觸發器時，觸發器將處理所有新警報。 如果未收到警報，則跳過觸發器運行。 下一次發生觸發程序輪詢的時間，會根據您在觸發程序屬性中指定的循環間隔而定。

此示例演示如何在將新警報發送到應用時啟動邏輯應用工作流。

1.  在 Azure 門戶或視覺化工作室中，創建一個空白邏輯應用，該應用將打開邏輯應用設計器。 這個範例會使用 Azure 入口網站。

1.  在設計器上，在搜索框中，輸入"Microsoft 圖形安全性"作為篩選器。 從觸發器清單中選擇此觸發器：**在所有新警報上**

1.  在觸發器中，提供有關要監視的警報的資訊。 有關更多屬性，請打開 **"添加新參數**清單"，然後選擇一個參數將該屬性添加到觸發器中。

   | 屬性 | Property (JSON) | 必要 | 類型 | 描述 |
   |----------|-----------------|----------|------|-------------|
   | **區間** | `interval` | 是 | 整數  | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p><p>- 月：1-16 個月 <br>- 天：1-500 天 <br>- 小時：1-12,000 個小時 <br>- 分鐘：1-72,000 分鐘 <br>- 秒：1-9,999,999 秒 <p>例如，如果 interval 是 6，而 frequency 是「月」，則週期為每隔 6 個月。 |
   | **頻率** | `frequency` | 是 | String | 重複的時間單位：**秒**、**分鐘**、**小時**、**天**、**週**或**月** |
   | **時區** | `timeZone` | 否 | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | `startTime` | 否 | String | 以此格式提供開始日期和時間： <p><p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>例如，如果您希望 2017 年 9 月 18 日下午 2：00，請指定"2017-09-18T14：00"並選擇太平洋標準時間等時區。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注：** 此開始時間在未來最多為 49 年，必須遵循[UTC 日期格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的[ISO 8601 日期時間規範](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但沒有[UTC 偏移](https://en.wikipedia.org/wiki/UTC_offset)量。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 [*我可以使用開始日期和時間的方式有哪些？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  完成後，在設計器工具列上，選擇 **"保存**"。

1.  現在，繼續針對您想要使用觸發程序結果來執行的工作，於邏輯應用程式中新增一或多個動作。

## <a name="add-actions"></a>新增動作

以下是有關使用 Microsoft Graph 安全性連接器提供之各種動作的具體詳細資料。

### <a name="manage-alerts"></a>管理警示

若要篩選、排序或取得最近結果，只要** 提供 [Microsoft Graph 支援的 ODATA 查詢參數](https://docs.microsoft.com/graph/query-parameters)。 「請勿指定」** 完整的基底 URL 或 HTTP 動作，例如 `https://graph.microsoft.com/v1.0/security/alerts`，或是 `GET` 或 `PATCH` 作業。 以下具體範例顯示當您要列出高嚴重性警示時，適用於**取得警示**動作的參數：

`Filter alerts value as Severity eq 'high'`

如需您可搭配此連接器使用之查詢的詳細資訊，請參閱 [Microsoft Graph 安全性警示參考文件](https://docs.microsoft.com/graph/api/alert-list) \(英文\)。 若要建置含有此連接器的增強體驗，請深入了解連接器支援的[結構描述屬性警示](https://docs.microsoft.com/graph/api/resources/alert)。

| 動作 | 描述 |
|--------|-------------|
| **取得警示** | 獲取基於一個或多個[警報屬性篩選的警報](https://docs.microsoft.com/graph/api/resources/alert)，例如 。 `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **依識別碼取得警示** | 依據警示識別碼取得特定警示。 | 
| **更新警示** | 依據警示識別碼更新特定警示。 為確保您在要求中傳遞必要和可編輯的屬性，請參閱[警示的可編輯屬性](https://docs.microsoft.com/graph/api/alert-update) \(英文\)。 例如，若要指派警示給安全性分析師以讓他們可以進行調查，您可以更新警示的「指派給」**** 屬性。 |
|||

### <a name="manage-alert-subscriptions"></a>管理警示訂閱

Microsoft Graph 支援[*訂閱*](https://docs.microsoft.com/graph/api/resources/subscription)或 [*Webhook*](https://docs.microsoft.com/graph/api/resources/webhooks)。 若要取得、更新或刪除訂閱，請將 [Microsoft Graph 支援的 ODATA 查詢參數](https://docs.microsoft.com/graph/query-parameters)提供給 Microsoft Graph 實體，並在 ODATA 查詢後包含 `security/alerts`。 「請勿包含」** 基底 URL，例如：`https://graph.microsoft.com/v1.0`。 相反地，請使用此範例的格式：

`security/alerts?$filter=status eq 'New'`

| 動作 | 描述 |
|--------|-------------|
| **建立訂用帳戶** | [建立訂閱](https://docs.microsoft.com/graph/api/subscription-post-subscriptions)可通知您相關的任何變更。 您可以針對您想要的特定警示類型篩選此訂閱。 例如，您可以建立通知您有關高嚴重性警示的訂閱。 |
| **取得有效的訂閱** | [取得未到期的訂閱](https://docs.microsoft.com/graph/api/subscription-list)。 | 
| **更新訂閱** | 提供訂閱識別碼以[更新訂閱](https://docs.microsoft.com/graph/api/subscription-update)。 例如，若要延長訂閱，您可以更新訂閱的 `expirationDateTime` 屬性。 | 
| **刪除訂用帳戶** | 提供訂閱識別碼以[刪除訂閱](https://docs.microsoft.com/graph/api/subscription-delete)。 | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>管理威脅情報指標

若要篩選、排序或取得最近結果，只要** 提供 [Microsoft Graph 支援的 ODATA 查詢參數](https://docs.microsoft.com/graph/query-parameters)。 「請勿指定」** 完整的基底 URL 或 HTTP 動作，例如 `https://graph.microsoft.com/beta/security/tiIndicators`，或是 `GET` 或 `PATCH` 作業。 下面是一個特定示例，當您希望清單具有威脅類型時，該示例顯示**獲取 tiIndicators**操作的`DDoS`參數：

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

有關可使用此連接器的查詢的詳細資訊，請參閱[Microsoft 圖形安全威脅智慧指示器參考文檔中的"可選查詢參數](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http)"。 要使用此連接器構建增強體驗，可以詳細瞭解連接器支援的[架構屬性威脅智慧指示器](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta)。

| 動作 | 描述 |
|--------|-------------|
| **獲取威脅情報指示器** | 獲取基於一個或多個[tiIndicator 屬性篩選的 tiIndicator，](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta)例如，`threatType eq 'MaliciousUrl' or 'DDoS'` |
| **按 ID 獲取威脅情報指示器** | 根據 tiIndicator ID 獲取特定的 tiIndicator。 | 
| **創建威脅情報指示器** | 通過發佈到 tiIndicators 集合創建新的 tiIndicator。 要確保傳遞請求中所需的屬性，請參閱[創建 tiIndicator 所需的屬性](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http)。 |
| **提交多個威脅情報指標** | 通過過帳 tiIndicators 集合創建多個新的 tiIndicators。 要確保在請求中傳遞所需的屬性，請參閱[提交多個 tiIndicators 所需的屬性](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http)。 |
| **更新威脅情報指示器** | 根據 tiIndicator ID 更新特定 tiIndicator。 要確保傳遞請求中所需的可編輯屬性，請參閱[tiIndicator 的可編輯屬性](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http)。 例如，要更新要應用的操作，如果指標在目標產品安全工具中匹配，則可以更新 tiIndicator**的操作**屬性。 |
| **更新多個威脅情報指標** | 更新多個 ti 指標。 要確保傳遞請求中所需的屬性，請參閱[用於更新多個 tiIndicators 所需的屬性](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http)。 |
| **按 ID 刪除威脅情報指示器** | 根據 tiIndicator ID 刪除特定的 tiIndicator。 |
| **刪除多個威脅情報指標（按 ID 進行）** | 按其指示項刪除多個 ti指標。 要確保傳遞請求中所需的屬性，請參閱[用於按 ID 刪除多個 ti指標所需的屬性](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http)。 |
| **按外部指示刪除多個威脅情報指示器** | 按外部指示號刪除多個 ti指標。 要確保傳遞請求中所需的屬性，請參閱[通過外部指示刪除多個 ti指標所需的屬性](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http)。 |
|||

## <a name="connector-reference"></a>連接器參考

如需觸發程序、動作和限制的技術詳細資訊，它們是由連接器的 OpenAPI (以前稱為 Swagger) 來描述，請檢閱連接器的[參考頁面](https://aka.ms/graphsecurityconnectorreference)。

## <a name="next-steps"></a>後續步驟

了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
