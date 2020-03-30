---
title: 高級篩選 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: IoT 邊緣事件網格中的高級篩選。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992557"
---
# <a name="advanced-filtering"></a>進階篩選
事件網格允許在 json 負載中的任何屬性上指定篩選器。 這些篩選器建模為條件集`AND`，每個外部條件具有可選的內部`OR`條件。 對於每個`AND`條件，指定以下值：

* `OperatorType`- 比較的類型。
* `Key`- 要應用篩選器的屬性的 json 路徑。
* `Value`- 運行篩選器的引用值（或） `Values` - 運行篩選器的引用值集。

## <a name="json-syntax"></a>JSON 語法

高級篩選器的 JSON 語法如下所示：

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>篩選陣列值

事件網格今天不支援對值陣列進行篩選。 如果傳入事件具有高級篩選器鍵的陣列值，則匹配操作將失敗。 傳入事件最終與事件訂閱不匹配。

## <a name="and-or-not-semantics"></a>AND-OR-NOT 語義

請注意，在前面給出的 json 示例中`AdvancedFilters`，是一個陣列。 將每個`AdvancedFilter`陣列元素視為條件`AND`。

對於支援`NumberIn`多個值（如 、`NumberNotIn`等`StringIn`）的運算子，每個值都被視為條件。 `OR` 因此，將`StringBeginsWith("a", "b", "c")`匹配以 或`a`或`b``c`開頭的任何字串值。

> [!CAUTION]
> NOT 運算子`NumberNotIn`-`StringNotIn`並在現場給出`Values`的每個值上作為 AND 條件執行。
>
> 不這樣做將使篩選器成為"接受-所有"篩選器，並破壞篩選的目的。

## <a name="floating-point-rounding-behavior"></a>浮點舍入行為

事件網格使用`decimal`.NET 類型來處理所有數值。 事件訂閱 JSON 中指定的編號值不受浮點舍入行為的約束。

## <a name="case-sensitivity-of-string-filters"></a>字串篩選器的區分大小寫

所有字串比較都是不區分大小寫的。 今天沒有辦法改變這種行為。

## <a name="allowed-advanced-filter-keys"></a>允許的高級篩選器鍵

該`Key`屬性可以是已知的頂級屬性，也可以是具有多個點的 json 路徑，其中每個點表示踏入嵌套的 json 物件。

事件網格對金鑰中的`$`字元沒有任何特殊含義，與 JSONPath 規範不同。

### <a name="event-grid-schema"></a>事件網格架構

對於事件網格架構中的事件：

* ID
* 主題
* 主體
* EventType
* DataVersion
* 資料.Prop1
* 資料.Prop_Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>自訂事件架構

自訂事件架構沒有限制，`Key`因為事件網格不會在負載上強制實施任何包絡架構。

## <a name="numeric-single-value-filter-examples"></a>數位單值篩選器示例

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>數值範圍值篩選器示例

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>字串範圍值篩選器示例

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>布林單值篩選器示例

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
