---
title: B2B 訊息的自訂追蹤架構
description: 建立自訂追蹤架構以監視 Azure Logic Apps 中的 B2B 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76903060"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>建立自訂追蹤架構，以監視 Azure 邏輯 A 中的端對端工作流程

Azure Logic Apps 內建追蹤功能，可讓您針對工作流程的部分啟用。 不過，您可以設定自訂追蹤，將事件從一開始就記錄到工作流程的結尾，例如包含邏輯應用程式、BizTalk Server、SQL Server 或任何其他圖層的工作流程。 本文提供您可以在邏輯應用程式外部層級中使用的自訂程式碼。

## <a name="custom-tracking-schema"></a>自訂追蹤結構描述

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| sourceType | 是 | String | 具有下列允許值的執行來源類型： `Microsoft.Logic/workflows` 、 `custom` |
| source | 是 | 字串或 JToken | 如果來源類型為 `Microsoft.Logic/workflows` ，則來源資訊必須遵循此架構。 如果來源類型為 `custom` ，則架構為 JToken。 |
| systemId | 是 | String | 邏輯應用程式系統識別碼 |
| runId | 是 | String | 邏輯應用程式執行識別碼 |
| operationName | 是 | String | 作業的名稱，例如動作或觸發程式 |
| repeatItemScopeName | 是 | String | 如果動作是在 `foreach` 或迴圈內，則重複專案名稱 `until` |
| repeatItemIndex | 是 | 整數 | 指出動作是在 `foreach` 或 `until` 迴圈內，而且是重複的專案索引編號。 |
| trackingId | 否 | String | 用來將訊息相互關聯的追蹤識別碼 |
| correlationId | 否 | String | 相互關聯的訊息相互關聯識別碼 |
| clientRequestId | 否 | String | 用戶端可以填入此屬性來相互關聯訊息 |
| eventLevel | 是 | String | 事件的層級 |
| eventTime | 是 | Datetime | 事件的時間（UTC 格式）： *YYYY-MM-DD DDTHH： mm： SS. 00000Z* |
| recordType | 是 | String | 具有此允許值的追蹤記錄類型： `custom` |
| 記錄 (record) | 是 | JToken | 只有 JToken 格式的自訂記錄類型 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述

如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰

* [AS2 追蹤架構](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 追蹤架構](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何 [使用 Azure 監視器記錄來監視 B2B 訊息](../logic-apps/monitor-b2b-messages-log-analytics.md)