---
title: Azure 監視器警示的常見警示架構
description: 瞭解常見的警示架構、您應該使用它的原因，以及如何加以啟用
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710927"
---
# <a name="common-alert-schema"></a>一般警示結構描述

本文說明常見的警示架構為何、使用它的優點，以及如何加以啟用。

## <a name="what-is-the-common-alert-schema"></a>常見的警示架構為何？

一般警示結構描述會將 Azure 中警示通知的耗用量體驗標準化。 在過去，Azure 中的三個警示類型（計量、記錄和活動記錄）都有自己的電子郵件範本、webhook 架構等。使用一般警示架構，您現在可以接收具有一致架構的警示通知。

任何警示實例都會描述**受影響的資源**和**警示的原因**，而這些實例會在下列各節的一般架構中加以說明：
* **基本**概念：一組**標準化的欄位**，在所有警示類型中都是通用的，其中描述警示所在的**資源**，以及其他常見的警示中繼資料（例如，嚴重性或描述）。 
* **警示內容**：一組欄位，用來描述**警示的原因**，其中的欄位會**根據警示類型**而有所不同。 例如，計量警示在警示內容中會有類似計量名稱和計量值的欄位，而活動記錄警示則會包含產生警示之事件的相關資訊。 

我們從客戶聽到的一般整合案例牽涉到根據某個樞紐分析表（例如資源群組），將警示實例路由傳送給相關團隊，而在此之後，負責的小組會開始處理。 使用常見的警示架構，您可以利用必要欄位，讓內容欄位保持在不同的警示類型上，以供相關小組進一步調查。

這表示您可能會有較少的整合，讓管理和維護它們的程式變_得_更簡單。 此外，未來的警示承載擴充（例如，自訂、診斷擴充等）只會在一般架構中呈現。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>常見的警示架構會帶來哪些增強功能？

一般警示架構主要會在警示通知中自行資訊清單。 您會看到的增強功能如下所示：

| 動作 | 功能增強|
|:---|:---|
| SMS | 適用于所有警示類型的一致 SMS 範本。 |
| 電子郵件 | 一致且詳細的電子郵件範本，可讓您輕鬆地快速診斷問題。 在入口網站上的警示實例和受影響的資源的內嵌深層連結，可確保您可以快速跳到補救程式。 |
| Webhook/邏輯應用程式/Azure 函式/自動化 Runbook | 適用于所有警示類型的一致 JSON 結構，可讓您輕鬆地在不同的警示類型之間建立整合。 |

新的架構也可讓您在未來的 Azure 入口網站和 Azure 行動應用程式之間，提供更豐富的警示取用體驗。 

[深入瞭解 Webhook/Logic Apps/Azure Functions/自動化 Runbook 的架構定義。](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 下列動作不支援一般警示架構： ITSM 連接器。

## <a name="how-do-i-enable-the-common-alert-schema"></a>如何? 啟用一般警示架構嗎？

您可以透過入口網站上的動作群組，或透過 REST API，加入宣告或退出一般警示架構。 切換至新架構的切換會在動作層級存在。 例如，您必須另外加入宣告電子郵件動作和 webhook 動作。

> [!NOTE]
> 1. 下列警示類型預設支援一般架構（不需要選擇性）：
>     * 智慧型偵測警示
> 1. 下列警示類型目前不支援一般架構：
>     * [適用於 VM 的 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)所產生的警示
>     * [Azure 成本管理](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)所產生的警示

### <a name="through-the-azure-portal"></a>透過 Azure 入口網站

![一般警示架構加入宣告](media/alerts-common-schema/portal-opt-in.png)

1. 在動作群組中開啟任何現有或新的動作。 
1. 針對切換選取 [是] 以啟用一般警示架構，如下所示。

### <a name="through-the-action-groups-rest-api"></a>透過動作群組 REST API

您也可以使用「[動作群組 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 」來選擇一般警示架構。 建立[或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 呼叫時，您可以將旗標 "useCommonAlertSchema" 設為 ' true ' （以加入宣告）或 ' false ' （以退出宣告）下列任何動作-電子郵件/webhook/邏輯應用程式/Azure 函式/自動化 runbook。

例如，建立[或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 的下列要求主體將會執行下列動作：

* 啟用電子郵件動作「John Doe 的電子郵件」的一般警示架構
* 停用電子郵件動作「Jane Smith 的電子郵件」的一般警示架構
* 啟用 webhook 動作「範例 webhook」的一般警示架構

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>後續步驟

- [適用于 Webhook/Logic Apps/Azure Functions/自動化 Runbook 的一般警示架構定義。](https://aka.ms/commonAlertSchemaDefinitions)
- [瞭解如何建立邏輯應用程式，利用通用的警示架構來處理您的所有警示。](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



