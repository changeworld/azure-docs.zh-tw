---
title: Advanced 篩選-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的 Advanced 篩選。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171766"
---
# <a name="advanced-filtering"></a>進階篩選
事件方格可讓您針對 json 承載中的任何屬性指定篩選。 這些篩選準則會模型化為一組 `AND` 條件，而每個外部條件都有選擇性的內部 `OR` 條件。 針對每個 `AND` 條件，您可以指定下列值：

* `OperatorType` -比較的類型。
* `Key` -要套用篩選之屬性的 json 路徑。
* `Value` -執行篩選時所針對的參考值 (或) `Values` -執行篩選所針對的參考值集。

## <a name="json-syntax"></a>JSON 語法

Advanced 篩選的 JSON 語法如下所示：

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

事件方格目前不支援篩選值的陣列。 如果內送事件具有適用于 advanced filter 的索引鍵的陣列值，則比對作業會失敗。 傳入事件最後不符合事件訂閱。

## <a name="and-or-not-semantics"></a>AND-OR-NOT 語義

請注意，在稍早指定的 json 範例中， `AdvancedFilters` 是陣列。 請將每個 `AdvancedFilter` 陣列元素視為 `AND` 條件。

針對支援多個值的運算子 (例如 `NumberIn` 、 `NumberNotIn` 、等等 `StringIn` ) ，會將每個值視為 `OR` 條件。 因此，會比對以 `StringBeginsWith("a", "b", "c")` 或或開頭的任何字串 `a` 值 `b` `c` 。

> [!CAUTION]
> NOT 運算子 `NumberNotIn` ，而且會 `StringNotIn` 針對欄位中提供的每個值，以 and 條件的方式運作 `Values` 。
>
> 若未這麼做，就會讓篩選準則成為 Accept-All 篩選，並使篩選的目的更好。

## <a name="floating-point-rounding-behavior"></a>浮點數舍入行為

事件方格會使用 `decimal` .net 型別來處理所有數值。 事件訂用帳戶 JSON 中指定的數值不受浮點數舍入行為的制約。

## <a name="case-sensitivity-of-string-filters"></a>字串篩選的區分大小寫

所有字串比較都不區分大小寫。 目前沒有任何方法可以變更此行為。

## <a name="allowed-advanced-filter-keys"></a>允許的 advanced 篩選鍵

`Key`屬性可以是已知的最上層屬性，也可以是具有多個點的 json 路徑，其中每個點表示逐步執行至嵌套的 json 物件。

與 JSONPath 規格不同的是，事件方格對索引鍵中的字元沒有任何特殊意義 `$` 。

### <a name="event-grid-schema"></a>事件方格架構

事件方格架構中的事件：

* 識別碼
* 主題
* 主體
* EventType
* DataVersion
* Prop1
* Data. This.prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>自訂事件架構

`Key`自訂事件架構中沒有任何限制，因為事件方格不會在承載上強制執行任何信封架構。

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

## <a name="string-range-value-filter-examples"></a>字串範圍值篩選範例

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

## <a name="boolean-single-value-filter-examples"></a>布林值單一值篩選範例

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
