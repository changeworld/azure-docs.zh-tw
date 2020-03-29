---
title: B2B 消息的自訂跟蹤架構
description: 創建自訂跟蹤架構以監視 Azure 邏輯應用中的 B2B 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903060"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>創建自訂跟蹤架構，監視 Azure 邏輯 A 中的端到端工作流

Azure 邏輯應用具有內置跟蹤功能，您可以為工作流的某些部分啟用該跟蹤。 但是，您可以設置自訂跟蹤，從工作流的開頭到末尾記錄事件，例如，包括邏輯應用、BizTalk Server、SQL Server 或任何其他層的工作流。 本文提供您可以在邏輯應用程式外部層級中使用的自訂程式碼。

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
| sourceType | 是 | String | 具有這些允許值的運行源的類型： `Microsoft.Logic/workflows``custom` |
| source | 是 | 字串或 JToken | 如果源類型為`Microsoft.Logic/workflows`，則源資訊需要遵循此架構。 如果源類型為`custom`，則架構是 JToken。 |
| systemId | 是 | String | 邏輯應用系統 ID |
| runId | 是 | String | 邏輯應用運行 ID |
| operationName | 是 | String | 操作的名稱，例如操作或觸發器 |
| repeatItemScopeName | 是 | String | 如果操作在 或`foreach``until`迴圈內，請重複專案名稱 |
| repeatItemIndex | 是 | 整數  | 指示操作在`foreach`或`until`迴圈中，是重複的物料索引編號。 |
| trackingId | 否 | String | 跟蹤 ID 以關聯郵件 |
| correlationId | 否 | String | 關聯 ID 以關聯消息 |
| clientRequestId | 否 | String | 用戶端可以填充此屬性以關聯消息 |
| eventLevel | 是 | String | 事件級別 |
| eventTime | 是 | Datetime | UTC 格式的事件時間 *：YYYY-MM-DDTHH：MM：SS.00000Z* |
| recordType | 是 | String | 僅具有此允許值的追蹤記錄類型：`custom` |
| 記錄 (record) | 是 | JToken | 僅具有 JToken 格式的自訂記錄類型 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述

如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰

* [AS2 跟蹤架構](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟蹤架構](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* 瞭解有關使用[Azure 監視器日誌監視 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)的資訊