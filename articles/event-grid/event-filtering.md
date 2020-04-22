---
title: 適用於 Azure 事件格線的事件篩選
description: 描述如何在建立 Azure 事件格線訂用帳戶期間篩選事件。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: ce1bb3760ae73a9eaeee3cde957cc94841ebdf29
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731947"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>了解適用於事件格線訂用帳戶的事件篩選

本文說明篩選要傳送至端點之事件的不同方式。 建立事件訂閱時，您有三個進行篩選的選項：

* 事件類型
* 主旨開頭為或結尾為
* 進階欄位和運算子

## <a name="event-type-filtering"></a>事件類型篩選

根據預設，適用於事件來源的所有[事件類型](event-schema.md)都會被傳送至端點。 您可以決定僅將特定事件類型傳送至端點。 例如，您可以取得資源更新的通知，但不取得其他作業 (例如刪除) 的通知。 在該情況下，請依 `Microsoft.Resources.ResourceWriteSuccess` 事件類型進行篩選。 提供事件類型的陣列，或是指定 `All` 以取得適用於該事件來源的所有事件類型。

依事件類型進行篩選的 JSON 語法為：

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>主旨篩選

若要依主旨進行簡易篩選，請針對主旨指定開始或結束值。 例如，您可以指定以 `.txt` 作為結尾的主旨，以僅取得與將文字檔上傳至儲存體帳戶相關的事件。 或者，您可以篩選以 `/blobServices/default/containers/testcontainer` 為開頭的主旨，以取得儲存體帳戶中該容器的所有事件，但不會取得其他容器中的事件。

將事件發佈至自訂主題時，為您的事件建立主旨，以便訂閱者輕鬆地了解他們是否對該事件感興趣。 訂閱者使用主旨屬性以篩選和路由事件。 請考慮新增事件發生之位置的路徑，以便訂閱者可以根據該路徑的區段進行篩選。 該路徑可讓訂閱者縮小或放大篩選事件的範圍。 如果您在主旨中提供三區段路徑 (例如 `/A/B/C`)，訂閱者可根據第一個區段 `/A` 進行篩選，以取得一組廣泛的事件。 這些訂閱者可使用 `/A/B/C` 或 `/A/D/E` 之類的主旨來取得事件。 其他訂閱者可以根據 `/A/B` 篩選以取得一組範圍較小的事件。

依主題篩選的 JSON 語法是:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>進階篩選

若要依資料欄位中的值進行篩選並指定比較運算子，請使用進階篩選的選項。 在進階篩選中，您會指定：

* 運算子類型 - 比較的類型。
* 索引鍵 - 事件資料中用來進行篩選的欄位。 它可以是數字、布林值或字串。
* 值 - 要與鍵進行比較的值。

如果指定具有多個值的單個篩選器,則執行**OR**操作,因此鍵欄位的值必須是這些值之一。 範例如下：

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

如果指定多個不同的篩選器,則執行**AND**操作,因此必須滿足每個篩選器條件。 範例如下： 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operator"></a>運算子

適用於數字的可用運算子為：

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

適用於布林值的可用運算子為：BoolEquals

適用於字串的可用運算子為：

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

所有字串比較皆會區分大小寫。

### <a name="key"></a>Key

針對事件格線結構描述中的事件，請使用下列值作為索引鍵：

* ID
* 主題
* 主體
* EventType
* DataVersion
* 事件資料 (例如 Data.key1)

針對雲端事件結構描述中的事件，請使用下列值作為索引鍵：

* EventId
* 來源
* EventType
* EventTypeVersion
* 事件資料 (例如 Data.key1)

針對自訂輸入結構描述，請使用事件資料欄位 (例如 Data.key1)。

### <a name="values"></a>值

其值可能是：

* number
* 字串
* boolean
* array

### <a name="limitations"></a>限制

進階篩選有下列限制：

* 每個事件格線訂用帳戶只能有五個進階篩選
* 每個字串值只能有 512 個字元
* **in** 和 **not in** 運算子個別只能有五個值

相同的索引鍵可以用在多個篩選中。

## <a name="next-steps"></a>後續步驟

* 若要了解搭配 PowerShell 和 Azure CLI 來篩選事件，請參閱[針對事件格線篩選事件](how-to-filter-events.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
