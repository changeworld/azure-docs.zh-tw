---
title: Azure 監視器警報的常見警報架構
description: 瞭解通用警報架構、為什麼要使用它以及如何啟用它
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249044"
---
# <a name="common-alert-schema"></a>一般警示結構描述

本文介紹了公共警報架構是什麼、使用它的好處以及如何啟用它。

## <a name="what-is-the-common-alert-schema"></a>什麼是常見警報架構？

通用警報架構規範了 Azure 中警報通知的消耗體驗。 從歷史上看，Azure 中的三種警報類型（指標、日誌和活動日誌）都有自己的電子郵件範本、Webhook 架構等。使用通用警報架構，您現在可以使用一致的架構接收警報通知。

任何警報實例都描述**受影響的資源和****警報的原因**，這些實例在以下各節中的通用架構中描述：
* **要點**：一組**標準化欄位**，在所有警報類型中很常見，用於描述警報所使用**的資源**以及其他常見警報中繼資料（例如，嚴重性或描述）。 
* **警報上下文**：一組描述**警報原因的**欄位，欄位**因警報類型**而異。 例如，指標警報在警報上下文中具有指標名稱和指標值等欄位，而活動日誌警報將包含有關生成警報的事件的資訊。 

我們從客戶那裡聽到的典型集成方案涉及根據某個資料透視（例如資源組）將警報實例路由到相關團隊，之後負責團隊開始處理它。 使用通用警報架構，您可以通過利用基本欄位來跨警報類型具有標準化路由邏輯，使上下文欄位與相關團隊一樣進行進一步調查。

這意味著您可能擁有較少的集成，從而使管理和維護它們的過程_成為_一項更簡單的任務。 此外，將來的警報有效負載擴充（例如，自訂、診斷擴充等）將僅在通用架構中顯示。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>常見的警報架構帶來了哪些增強功能？

公共警報架構將主要在警報通知中顯示。 您將看到的增強功能如下所示：

| 動作 | 功能增強|
|:---|:---|
| sms | 適用于所有警報類型的一致的 SMS 範本。 |
| 電子郵件 | 一致而詳細的電子郵件範本，可讓您輕鬆快速診斷問題。 指向門戶上警報實例和受影響資源的嵌入式深層連結可確保快速跳轉到修復過程。 |
| 網路鉤/邏輯應用/Azure 功能/自動化運行簿 | 適用于所有警報類型的一致的 JSON 結構，允許您輕鬆構建跨不同警報類型的集成。 |

新的架構還將在不遠的將來跨 Azure 門戶和 Azure 移動應用實現更豐富的警報消耗體驗。 

[詳細瞭解 Webhook/邏輯應用/Azure 函數/自動化 Runbook 的架構定義。](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 以下操作不支援通用警報架構：ITSM 連接器。

## <a name="how-do-i-enable-the-common-alert-schema"></a>如何啟用通用警報架構？

您可以通過操作組、門戶和 REST API 加入宣告或退出宣告通用警報架構。 切換到新架構的切換存在於操作級別。 例如，您必須單獨選擇電子郵件操作和網路鉤子操作。

> [!NOTE]
> 1. 預設情況下，以下警報類型支援通用架構（無需加入宣告）：
>     * 智慧型偵測警示
> 1. 以下警報類型當前不支援通用架構：
>     * [Azure 監視器為 VM](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)生成的警報
>     * [Azure 成本管理](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)生成的警報

### <a name="through-the-azure-portal"></a>通過 Azure 門戶

![常見警報架構加入宣告](media/alerts-common-schema/portal-opt-in.png)

1. 打開操作組中的任何現有操作或新操作。 
1. 為切換選擇"是"，以啟用通用警報架構，如圖所示。

### <a name="through-the-action-groups-rest-api"></a>通過操作組 REST API

您還可以使用[操作組 API](https://docs.microsoft.com/rest/api/monitor/actiongroups)加入宣告公共警報架構。 在[創建或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 呼叫時，您可以將"使用公共警報架構"的標誌設置為"true"（加入宣告）或"false"（退出宣告）以執行以下任一操作 - 電子郵件/webhook/邏輯應用/Azure 功能/自動化運行簿。

例如，對[創建或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 所做的以下請求正文將執行以下操作：

* 為電子郵件操作"John Doe 的電子郵件"啟用通用警報架構
* 禁用電子郵件操作"簡·史密斯的電子郵件"的常見警報模式
* 為 Webhook 操作"示例 Webhook"啟用通用警報架構

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

- [Webhook/邏輯應用/Azure 函數/自動化 Runbook 的常見警報架構定義。](https://aka.ms/commonAlertSchemaDefinitions)
- [瞭解如何創建利用通用警報架構來處理所有警報的邏輯應用。](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



