---
title: Azure 事件格線訂用帳戶的結構描述
description: 本文說明使用 Azure 事件方格來訂閱事件的屬性。 事件方格訂用帳戶架構。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720753"
---
# <a name="event-grid-subscription-schema"></a>事件格線訂用帳戶的結構描述

若要建立事件格線訂用帳戶，請將要求傳送至建立事件訂用帳戶作業。 請使用下列格式：

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

例如，若要在名為 `examplestorage` 的資源群組中建立命名為 `examplegroup` 的儲存體帳戶之事件訂用帳戶，請使用下列格式：

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

「事件訂用帳戶」名稱必須長度為 3-64 個字元，且只能包含 a-z、A-Z、0-9 及 "-"。 本文描述要求主體的屬性和結構描述。
 
## <a name="event-subscription-properties"></a>事件訂用帳戶屬性

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 目的地 | 物件 (object) | 定義端點的物件。 |
| filter | 物件 (object) | 篩選事件類型的選擇性欄位。 |

### <a name="destination-object"></a>目的地物件

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| endpointType | 字串 | 訂用帳戶的端點類型 (webhook/HTTP、事件中樞或佇列)。 | 
| endpointUrl | 字串 | 此事件訂用帳戶中事件的目的地 URL。 | 

### <a name="filter-object"></a>篩選物件

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| includedEventTypes | array | 事件訊息中的事件類型完全符合這些事件類型名稱其中之一時，即會符合。 事件名稱不符合事件來源的已註冊事件類型名稱時，會引發錯誤。 預設會符合所有事件類型。 |
| subjectBeginsWith | 字串 | 事件訊息中對主體欄位的前置詞符合篩選。 預設值或空字串會符合全部。 | 
| subjectEndsWith | 字串 | 事件訊息中對主體欄位的後置詞符合篩選。 預設值或空字串會符合全部。 |
| isSubjectCaseSensitive | 字串 | 控制篩選的區分大小寫比對。 |


## <a name="example-subscription-schema"></a>範例訂用帳戶的結構描述

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

* 若要初步了解事件格線，請參閱[什麼是事件格線？](overview.md)
