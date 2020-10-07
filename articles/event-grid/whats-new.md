---
title: 新功能 版本資訊 - Azure 事件方格
description: 了解 Azure 事件方格的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 1edfa3e2bc4c8adae113b2215b7fb0483fba4c02
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87172689"
---
# <a name="whats-new-in-azure-event-grid"></a>Azure 事件方格的新功能

>藉由將此 URL：`https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` 複製並貼到您的 ![RSS 摘要讀取程式圖示](./media/whats-new/feed-icon-16x16.png)摘要讀取程式，即可獲知何時要重新造訪此頁面來進行更新。

Azure 事件方格會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- 新增對於新正式推出 (GA) 服務 API 版本 2020-06-01 的支援。
- 正式推出的新功能：
    - [輸入對應](input-mappings.md)
    - [自訂輸入結構描述](input-mappings.md)
    - [雲端事件 V10 結構描述](cloud-event-schema.md)
    - [將服務匯流排主題作為目的地](handler-service-bus.md)
    - [將 Azure Function 作為目的地](handler-functions.md)
    - [Webhook 批次處理](./edge/delivery-output-batching.md)
    - [安全 Webhook (Azure Active Directory 支援)](secure-webhook-delivery.md)
    - [IP 篩選](configure-firewall.md)
    - [Private Link 服務支援](configure-private-endpoints.md)
    - [事件傳遞結構描述](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- 此版本包含其他可強化品質的錯誤修正。
- 就版本 5.3.1-preview 而言，此版本對應至 2020-04-01-Preview API 版本，其中包含下列新功能： 
    - [支援在將事件發佈至網域和主題時進行 IP 篩選](configure-firewall.md)
    - [合作夥伴主題](partner-topics-overview.md)
    - [系統主題在 Azure 入口網站中成為已追蹤的資源](system-topics.md)
    - [使用受管理的服務識別進行事件傳遞](managed-service-identity.md) 
    - [Private Link 服務支援](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- 此版本包含各種可強化品質的錯誤修正。
- 就版本 5.3.0-preview 而言，此版本對應至 2020-04-01-Preview API 版本，其中包含下列新功能： 
    - [支援在將事件發佈至網域和主題時進行 IP 篩選](configure-firewall.md)
    - [合作夥伴主題](partner-topics-overview.md)
    - [系統主題在 Azure 入口網站中成為已追蹤的資源](system-topics.md)
    - [使用受管理的服務識別進行事件傳遞](managed-service-identity.md) 
    - [Private Link 服務支援](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- 我們在版本 5.2.0-preview 已新增的功能之上引進了新功能。 
- 就版本 5.2.0-preview 而言，此版本對應至 2020-04-01-Preview API 版本。
- 其新增了對於下列新功能的支援： 
    - [支援在將事件發佈至網域和主題時進行 IP 篩選](configure-firewall.md)
    - [合作夥伴主題](partner-topics-overview.md)
    - [系統主題在 Azure 入口網站中成為已追蹤的資源](system-topics.md)
    - [使用受管理的服務識別進行事件傳遞](managed-service-identity.md) 
    - [Private Link 服務支援](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- 此版本對應至 2020-04-01-Preview API 版本。
- 其新增了對於下列新功能的支援：
    - [支援在將事件發佈至網域和主題時進行 IP 篩選](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- 此版本對應至 `2019-06-01` API 版本。
- 其新增了對於下列新功能的支援：
    * [網域](event-domains.md)
    * 適用於資源列出作業的分頁和搜尋篩選。 如需範例，請參閱[主題 - 依訂用帳戶列出](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)。
    * [將服務匯流排佇列作為目的地](handler-service-bus.md)
    * [進階篩選](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- 此版本對應至 2019-02-01-preview API 版本。
- 其新增了對於下列新功能的支援：
    * 適用於資源列出作業的分頁和搜尋篩選。 如需範例，請參閱[主題 - 依訂用帳戶列出](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)。
    * [手動建立/刪除網域主題](how-to-event-domains.md)
    * [將服務匯流排佇列作為目的地](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- 此版本對應至 `2019-01-01` 穩定 API 版本。
- 其針對與事件訂閱相關的下列功能支援正式推出 (GA)：
    * [無效信件目的地](manage-event-delivery.md)
    * [將 Azure 儲存體佇列作為目的地](handler-storage-queues.md)
    * [Azure 轉送 - 將混合式連線作為目的地](handler-relay-hybrid-connections.md)
    * [手動交握驗證](webhook-event-delivery.md)
    * [支援重試原則](delivery-and-retry.md)
- 仍處於預覽狀態的功能 (例如[事件方格網域](event-domains.md)或[進階篩選支援](event-filtering.md#advanced-filtering) 仍可使用 3.0.1-preview 版本的 SDK 來存取。」

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- 相依於 [10.0.3 版本的 Newtonsoft NuGet 套件](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)。

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- 此版本是 2018-09-15-preview API 版本中所引進新功能的預覽 SDK。 - 此版本包含下列功能的支援：
    - [網域和網域主題](event-domains.md)
    - 引進[事件訂閱的到期日](concepts.md#event-subscription-expiration)
    - 引進事件訂閱的[進階篩選](event-filtering.md#advanced-filtering)
    - 以 `2018-01-01` API 版本作為目標的穩定版 SDK 會繼續以 1.3.0 版的形式存在

## <a name="next-steps"></a>後續步驟
