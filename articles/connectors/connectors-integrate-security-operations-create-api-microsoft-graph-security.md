---
title: 整合及管理安全性作業 & Microsoft Graph 安全性
description: 使用 Microsoft Graph 安全性 & 來改善應用程式的威脅防護、偵測和回應 Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: ea861dce23e50bc5fcae50116a3e71793d402f8f
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873313"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>使用 Microsoft Graph 安全性和 Azure Logic Apps 整合安全性作業，改善威脅防護功能

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和 [Microsoft Graph 安全性](/graph/security-concept-overview)連接器，您就可以建立整合 Microsoft 安全性產品、服務和合作夥伴的自動化工作流程，改善應用程式偵測、保護和回應威脅的方式。 例如，您可以建立 [Azure 資訊安全中心劇本](../security-center/workflow-automation.md)，用來監視和管理 Microsoft Graph 安全性實體 (例如警告)。 以下是 Microsoft Graph Security connector 支援的一些案例：

* 依據查詢或警示識別碼取得警示。 例如，您可以取得包含高嚴重性警示的清單。

* 更新警示。 例如，您可以更新警示指派、新增警示的註解或標記警示。

* 建立[警示訂閱 (Webhook)](/graph/api/resources/webhooks) 監視警示建立或變更的時間。

* 管理您的警示訂閱。 例如，您可以取得有效的訂閱、延長訂閱的到期時間，或刪除訂閱。

您的邏輯應用程式工作流程可使用從 Microsoft Graph 安全性連接器取得回應的動作，並讓該輸出提供給工作流程中的其他動作使用。 您也可以讓您工作流程中的其他動作使用來自 Microsoft Graph 安全性連接器動作的輸出。 例如，如果您透過 Microsoft Graph 安全性連接器取得高嚴重性警示，您可以使用 Outlook 連接器將那些警示以電子郵件訊息傳送。 

若要深入了解 Microsoft Graph 安全性，請參閱 [Microsoft Graph 安全性 API 概觀](/graph/security-concept-overview)。 如果您不熟悉邏輯應用程式，請參閱 [什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)。 如果您要尋找 Power Automate 或 PowerApps，請參閱 [什麼是 Power Automate？](https://flow.microsoft.com/) 或 [什麼是 powerapps？](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 若要使用 Microsoft Graph 安全性連接器，您必須擁有 *明確提供* 的 Azure Active Directory (AD) 租用戶系統管理員同意，這是 [Microsoft Graph 安全性驗證需求](/graph/security-authorization)的一部分。 這項同意需要 Microsoft Graph 安全性連接器的應用程式識別碼和名稱，您也可以在 [Azure 入口網站](https://portal.azure.com)中找到：

  | 屬性 | 值 |
  |----------|-------|
  | **應用程式名稱** | `MicrosoftGraphSecurityConnector` |
  | **應用程式識別碼** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  若要授與連接器同意，您的 Azure AD 租使用者系統管理員可以遵循下列其中一項步驟：

  * [為 Azure AD 應用程式授與租用戶系統管理員同意](../active-directory/develop/v2-permissions-and-consent.md)。

  * 在您的邏輯應用程式第一次執行時，您的應用程式可透過[應用程式同意體驗](../active-directory/develop/application-consent-experience.md)要求 Azure AD 租用戶系統管理員的同意。
   
* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 邏輯應用程式即為您想要存取 Microsoft Graph 安全性實體 (例如警示) 的位置。 若要使用 Microsoft Graph 安全性觸發程式，您需要空白的邏輯應用程式。 若要使用 Microsoft Graph 的安全性動作，您需要一個邏輯應用程式，該應用程式會以適用于您案例的適當觸發程式為開頭。

## <a name="connect-to-microsoft-graph-security"></a>連線至 Microsoft Graph 安全性 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com/)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，新增觸發程式和您想要的任何其他動作，然後再新增 Microsoft Graph 的安全性動作。

   -或-

   針對現有的邏輯應用程式，請在您要新增 Microsoft Graph 安全性動作的最後一個步驟底下，選取 [ **新增步驟**]。

   -或-

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選取出現的加號 (+) ，然後選取 [ **新增動作**]。

1. 在搜尋方塊中，輸入「Microsoft Graph 安全性」作為篩選條件。 從 [動作] 清單中，選取您想要的動作。

1. 使用您的 Microsoft Graph 安全性認證登入。

1. 為您選取的動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

## <a name="add-triggers"></a>新增觸發程式

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程式時，Logic Apps 引擎都會建立邏輯應用程式實例，並開始執行您應用程式的工作流程。

> [!NOTE] 
> 觸發程式引發時，觸發程式會處理所有新的警示。 如果未收到任何警示，則會略過觸發程式執行。 下一次發生觸發程序輪詢的時間，會根據您在觸發程序屬性中指定的循環間隔而定。

此範例說明如何在新的警示傳送至您的應用程式時，啟動邏輯應用程式工作流程。

1.  在 Azure 入口網站或 Visual Studio 中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。 這個範例會使用 Azure 入口網站。

1.  在設計工具的搜尋方塊中，輸入「microsoft graph 安全性」作為篩選準則。 從觸發程式清單中選取此觸發程式： **在所有新的警示上**

1.  在觸發程式中，提供您想要監視之警示的相關資訊。 如需其他屬性，請開啟 [ **加入新的參數** ] 清單，然後選取參數將該屬性加入至觸發程式。

   | 屬性 | 屬性 (JSON) | 必要 | 類型 | 說明 |
   |----------|-----------------|----------|------|-------------|
   | **間隔** | `interval` | 是 | 整數 | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p><p>- 月：1-16 個月 <br>- 天：1-500 天 <br>- 小時：1-12,000 個小時 <br>- 分鐘：1-72,000 分鐘 <br>- 秒：1-9,999,999 秒 <p>例如，如果 interval 是 6，而 frequency 是「月」，則週期為每隔 6 個月。 |
   | **頻率** | `frequency` | 是 | String | 重複的時間單位：**秒**、**分鐘**、**小時**、**天**、**週** 或 **月** |
   | **時區** | `timeZone` | 否 | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | `startTime` | 否 | String | 以下列格式提供開始日期和時間： <p><p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>例如，如果您想要在2017年9月18日下午2:00，則請指定 "2017-09-18T14：00： 00"，然後選取時區，例如太平洋標準時間。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注意：** 此開始時間在未來的時間最多為49年，且必須遵循 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) （ [utc 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)），但不含 [utc 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 [*我可以使用開始日期和時間的方式有哪些？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  完成時，請在設計工具的工具列上，選取 [儲存]。

1.  現在，繼續針對您想要使用觸發程序結果來執行的工作，於邏輯應用程式中新增一或多個動作。

## <a name="add-actions"></a>新增動作

以下是有關使用 Microsoft Graph 安全性連接器提供之各種動作的具體詳細資料。

### <a name="manage-alerts"></a>管理警示

若要篩選、排序或取得最近結果，只要提供 [Microsoft Graph 支援的 ODATA 查詢參數](/graph/query-parameters)。 「請勿指定」完整的基底 URL 或 HTTP 動作，例如 `https://graph.microsoft.com/v1.0/security/alerts`，或是 `GET` 或 `PATCH` 作業。 以下具體範例顯示當您要列出高嚴重性警示時，適用於 **取得警示** 動作的參數：

`Filter alerts value as Severity eq 'high'`

如需您可搭配此連接器使用之查詢的詳細資訊，請參閱 [Microsoft Graph 安全性警示參考文件](/graph/api/alert-list) \(英文\)。 若要建置含有此連接器的增強體驗，請深入了解連接器支援的[結構描述屬性警示](/graph/api/resources/alert)。

| 動作 | 描述 |
|--------|-------------|
| **取得警示** | 取得根據一或多個 [警示屬性](/graph/api/resources/alert)篩選的警示，例如 `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` 。 | 
| **依識別碼取得警示** | 依據警示識別碼取得特定警示。 | 
| **更新警示** | 依據警示識別碼更新特定警示。 為確保您在要求中傳遞必要和可編輯的屬性，請參閱[警示的可編輯屬性](/graph/api/alert-update) \(英文\)。 例如，若要指派警示給安全性分析師以讓他們可以進行調查，您可以更新警示的「指派給」屬性。 |
|||

### <a name="manage-alert-subscriptions"></a>管理警示訂閱

Microsoft Graph 支援 [*訂閱*](/graph/api/resources/subscription)或 [*Webhook*](/graph/api/resources/webhooks)。 若要取得、更新或刪除訂閱，請將 [Microsoft Graph 支援的 ODATA 查詢參數](/graph/query-parameters)提供給 Microsoft Graph 實體，並在 ODATA 查詢後包含 `security/alerts`。 「請勿包含」基底 URL，例如：`https://graph.microsoft.com/v1.0`。 相反地，請使用此範例的格式：

`security/alerts?$filter=status eq 'NewAlert'`

| 動作 | 描述 |
|--------|-------------|
| **建立訂用帳戶** | [建立訂閱](/graph/api/subscription-post-subscriptions)可通知您相關的任何變更。 您可以針對您想要的特定警示類型篩選此訂閱。 例如，您可以建立通知您有關高嚴重性警示的訂閱。 |
| **取得有效的訂閱** | [取得未到期的訂閱](/graph/api/subscription-list)。 | 
| **更新訂閱** | 提供訂閱識別碼以[更新訂閱](/graph/api/subscription-update)。 例如，若要延長訂閱，您可以更新訂閱的 `expirationDateTime` 屬性。 | 
| **刪除訂用帳戶** | 提供訂閱識別碼以[刪除訂閱](/graph/api/subscription-delete)。 | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>管理威脅情報指標

若要篩選、排序或取得最近結果，只要提供 [Microsoft Graph 支援的 ODATA 查詢參數](/graph/query-parameters)。 「請勿指定」完整的基底 URL 或 HTTP 動作，例如 `https://graph.microsoft.com/beta/security/tiIndicators`，或是 `GET` 或 `PATCH` 作業。 以下是當您想要具有威脅類型的清單時，會顯示 **Get tiIndicators** 動作之參數的特定範例 `DDoS` ：

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

如需有關可搭配此連接器使用之查詢的詳細資訊，請參閱 [Microsoft Graph 安全性威脅情報指標參考檔中的「選擇性查詢參數](/graph/api/tiindicators-list)」。 若要使用此連接器建立增強的體驗，請深入瞭解連接器所支援的 [架構屬性威脅情報指標](/graph/api/resources/tiindicator) 。

| 動作 | 描述 |
|--------|-------------|
| **取得威脅情報指標** | 取得根據一或多個 [tiIndicator 屬性](/graph/api/resources/tiindicator)篩選的 tiIndicators，例如 `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **依識別碼取得威脅情報指標** | 取得以 tiIndicator 識別碼為基礎的特定 tiIndicator。 | 
| **建立威脅情報指標** | 藉由張貼至 tiIndicators 集合來建立新的 tiIndicator。 若要確定您在要求中傳遞必要的屬性，請參閱 [建立 tiIndicator 所需的屬性](/graph/api/tiindicators-post)。 |
| **提交多個威脅情報指標** | 藉由張貼 tiIndicators 集合來建立多個新的 tiIndicators。 若要確定您在要求中傳遞必要的屬性，請參閱 [提交多個 tiIndicators 的必要屬性](/graph/api/tiindicator-submittiindicators)。 |
| **更新威脅情報指標** | 根據 tiIndicator 識別碼更新特定 tiIndicator。 為了確保您在要求中傳遞必要和可編輯的屬性，請參閱 [tiIndicator 的可編輯屬性](/graph/api/tiindicator-update)。 例如，若要更新要套用的動作（如果指標在 targetProduct 安全性工具內相符），您可以更新 tiIndicator 的 **action** 屬性。 |
| **更新多個威脅情報指標** | 更新多個 tiIndicators。 為了確保您在要求中傳遞必要的屬性，請參閱 [更新多個 tiIndicators 的必要屬性](/graph/api/tiindicator-updatetiindicators)。 |
| **依識別碼刪除威脅情報指標** | 刪除以 tiIndicator 識別碼為基礎的特定 tiIndicator。 |
| **依識別碼刪除多個威脅情報指標** | 依識別碼刪除多個 tiIndicators。 若要確定您在要求中傳遞所需的屬性，請參閱 [依識別碼刪除多個 tiIndicators 的必要屬性](/graph/api/tiindicator-deletetiindicators)。 |
| **依外部識別碼刪除多個威脅情報指標** | 依外部識別碼刪除多個 tiIndicators。 若要確定您在要求中傳遞所需的屬性，請參閱使用 [外部識別碼刪除多個 tiIndicators 的必要屬性](/graph/api/tiindicator-deletetiindicatorsbyexternalid)。 |
|||

## <a name="connector-reference"></a>連接器參考

如需觸發程序、動作和限制的技術詳細資訊，它們是由連接器的 OpenAPI (以前稱為 Swagger) 來描述，請檢閱連接器的[參考頁面](/connectors/microsoftgraphsecurity/)。

## <a name="next-steps"></a>後續步驟

了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
