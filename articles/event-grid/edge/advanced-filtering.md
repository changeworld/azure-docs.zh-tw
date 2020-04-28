---
title: Advanced 篩選-Azure Event Grid IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的 Advanced 篩選。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "72992557"
---
# <a name="advanced-filtering"></a>進階篩選
事件方格允許在 json 承載中的任何屬性上指定篩選。 這些篩選器會模型化為`AND`條件集，每個外部條件都具有選擇性`OR`的內部條件。 針對每`AND`個條件，您可以指定下列值：

* `OperatorType`-比較的類型。
* `Key`-要套用篩選之屬性的 json 路徑。
* `Value`-執行篩選準則時所針對的參考值（或） `Values` -用來執行篩選的一組參考值。

## <a name="json-syntax"></a>JSON 語法

Advanced filter 的 JSON 語法如下所示：

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

事件方格目前不支援對值陣列進行篩選。 如果內送事件具有 advanced filter 的索引鍵的陣列值，則比對作業會失敗。 內送事件最後與事件訂用帳戶不相符。

## <a name="and-or-not-semantics"></a>AND-OR-NOT 語義

請注意，在先前指定的 json 範例`AdvancedFilters`中，是一個陣列。 將`AND`每個`AdvancedFilter`陣列元素視為條件。

針對支援多個值的運算子（ `NumberIn`例如、 `NumberNotIn`、 `StringIn`等），會將每個值視為`OR`條件。 因此， `StringBeginsWith("a", "b", "c")`會符合以`a`或`b`或`c`開頭的任何字串值。

> [!CAUTION]
> NOT 運算子- `NumberNotIn`和`StringNotIn`的`Values`行為是在欄位中指定的每個值上的和條件。
>
> 不這麼做會將篩選準則設為 [接受全部] 篩選準則，並破壞篩選的目的。

## <a name="floating-point-rounding-behavior"></a>浮點舍入行為

事件方格會使用`decimal` .net 類型來處理所有數值。 事件訂用帳戶 JSON 中指定的數位值不受浮點舍入行為的制約。

## <a name="case-sensitivity-of-string-filters"></a>字串篩選準則的區分大小寫

所有字串比較都不區分大小寫。 目前沒有任何方法可以變更這種行為。

## <a name="allowed-advanced-filter-keys"></a>允許的 advanced filter 金鑰

`Key`屬性可以是知名的最上層屬性，或為具有多個點的 json 路徑，其中每個點表示逐步執行為嵌套 json 物件。

不同于 JSONPath 規格，事件方格對索引`$`鍵中的字元沒有任何特殊意義。

### <a name="event-grid-schema"></a>事件方格架構

事件方格架構中的事件：

* 識別碼
* 主題
* 主體
* EventType
* DataVersion
* Data. Prop1
* Data. This.prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>自訂事件架構

自訂事件架構中沒有`Key`的限制，因為事件方格不會在裝載上強制執行任何信封架構。

## <a name="numeric-single-value-filter-examples"></a>數值單一值篩選範例

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

## <a name="numeric-range-value-filter-examples"></a>數值範圍值篩選範例

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

## <a name="string-range-value-filter-examples"></a>字串範圍-值篩選範例

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

## <a name="boolean-single-value-filter-examples"></a>布林單一值篩選範例

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
