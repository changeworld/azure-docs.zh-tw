---
title: Azure 監視器警示的一般警示架構
description: 瞭解常見的警示架構、使用它的原因，以及如何加以啟用
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: aa619976c8fa03b925d66e884ad03fc4e385693e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565597"
---
# <a name="common-alert-schema"></a>一般警示結構描述

本文說明常見的警示架構是什麼、使用它的優點，以及如何啟用它。

## <a name="what-is-the-common-alert-schema"></a>常見的警示架構為何？

一般警示結構描述會將 Azure 中警示通知的耗用量體驗標準化。 在過去，Azure 中的三個警示類型 (計量、記錄和活動記錄) 有自己的電子郵件範本、webhook 架構等。使用一般警示架構，您現在可以使用一致的架構接收警示通知。

任何警示實例都會描述 **受影響的資源** 和 **警示的原因** ，而這些實例將在下列各節的一般架構中說明：
* **Essentials** ：一組在所有警示類型中通用的 **標準化欄位** ，描述警示所在的 **資源** ，以及其他常見的警示中繼資料 (例如，嚴重性或描述) 。 
* **警示內容** ：一組描述 **警示原因** 的欄位，其中的欄位會 **根據警示類型** 而有所不同。 例如，計量警示在警示內容中會有像是計量名稱和計量值的欄位，而活動記錄警示會有產生警示的事件相關資訊。 

我們從客戶收到的一般整合案例，會根據某些 pivot (（例如資源群組) ）將警示實例路由傳送至相關小組，之後，負責的小組就會開始處理。 使用常見的警示架構，您可以利用基本欄位讓不同的警示類型具有標準化的路由邏輯，讓相關團隊的內容欄位保持不變，以進一步調查。

這表示您可能會有較少的整合，讓管理和維護 _它們的程式更簡單。_ 此外，未來的警示承載擴充 (例如，自訂、診斷擴充等，) 只會顯示在常見的架構中。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>常見的警示架構帶來了哪些增強功能？

常見的警示架構主要是在您的警示通知中資訊清單。 您會看到的增強功能如下所示：

| 動作 | 功能增強|
|:---|:---|
| sms | 所有警示類型都是一致的 SMS 範本。 |
| 電子郵件 | 一致且詳細的電子郵件範本，可讓您輕鬆地一眼就診斷問題。 入口網站上的警示實例和受影響資源的內嵌深層連結，可確保您可以迅速進入補救流程。 |
| Webhook/邏輯應用程式/Azure Function/Automation Runbook | 所有警示類型都是一致的 JSON 結構，可讓您輕鬆地在不同的警示類型之間建立整合。 |

新的架構也可讓您在未來的 Azure 入口網站和 Azure 行動應用程式之間提供更豐富的警示耗用量體驗。 

[深入瞭解 Webhook/Logic Apps/Azure Functions/Automation Runbook 的架構定義。](./alerts-common-schema-definitions.md)

> [!NOTE]
> 下列動作不支援一般警示架構： ITSM 連接器。

## <a name="how-do-i-enable-the-common-alert-schema"></a>如何? 啟用一般警示架構？

您可以透過動作群組，在入口網站上和透過 REST API，加入宣告或退出一般警示架構。 切換至新架構的切換會存在於動作層級。 例如，您必須個別加入宣告電子郵件動作和 webhook 動作。

> [!NOTE]
> 1. 下列警示類型預設支援一般架構 (沒有加入宣告必要) ：
>     * 智慧型偵測警示
> 1. 下列警示類型目前不支援一般架構：
>     * [適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)產生的警示
>     * [Azure 成本管理](../../cost-management-billing/manage/cost-management-budget-scenario.md)產生的警示

### <a name="through-the-azure-portal"></a>透過 Azure 入口網站

![一般警示架構加入宣告](media/alerts-common-schema/portal-opt-in.png)

1. 開啟動作群組中的任何現有或新的動作。 
1. 針對切換選取 [是]，以啟用一般警示架構，如下所示。

### <a name="through-the-action-groups-rest-api"></a>透過動作群組 REST API

您也可以使用 [動作群組 API](/rest/api/monitor/actiongroups) 來選擇使用一般警示架構。 在進行 [建立或更新](/rest/api/monitor/actiongroups/createorupdate) REST API 呼叫時，您可以將旗標 "useCommonAlertSchema" 設定為 ' true ' (加入宣告) 或 ' false ' (以退出宣告下列任何動作的) ：電子郵件/webhook/邏輯應用程式/Azure Function/automation runbook。

例如，下列對 [create 或 update](/rest/api/monitor/actiongroups/createorupdate) REST API 提出的要求主體會執行下列動作：

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

- [Webhook/Logic Apps/Azure Functions/Automation Runbook 的一般警示架構定義。](./alerts-common-schema-definitions.md)
- [瞭解如何建立邏輯應用程式，以利用一般警示架構來處理您所有的警示。](./alerts-common-schema-integrations.md)
