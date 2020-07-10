---
title: Advanced 篩選-Azure Event Grid IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的 Advanced 篩選。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171766"
---
# <a name="advanced-filtering"></a>進階篩選
事件方格允許在 json 承載中的任何屬性上指定篩選。 這些篩選器會模型化為 `AND` 條件集，每個外部條件都具有選擇性的內部 `OR` 條件。 針對每個 `AND` 條件，您可以指定下列值：

* `OperatorType`-比較的類型。
* `Key`-要套用篩選之屬性的 json 路徑。
* `Value`-用來執行篩選的參考值 (或) `Values` -執行篩選準則的一組參考值。

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

請注意，在先前指定的 json 範例中， `AdvancedFilters` 是一個陣列。 將每個 `AdvancedFilter` 陣列元素視為 `AND` 條件。

針對支援多個值 (的運算子（例如 `NumberIn` 、 `NumberNotIn` 、 `StringIn` 等） ) ，會將每個值視為 `OR` 條件。 因此， `StringBeginsWith("a", "b", "c")` 會符合以或或開頭的任何字串值 `a` `b` `c` 。

> [!CAUTION]
> NOT 運算子- `NumberNotIn` 和 `StringNotIn` 的行為是在欄位中指定的每個值上的和條件 `Values` 。
>
> 不這麼做會將篩選準則設為 [接受全部] 篩選準則，並破壞篩選的目的。

## <a name="floating-point-rounding-behavior"></a>浮點舍入行為

事件方格會使用 `decimal` .net 類型來處理所有數值。 事件訂用帳戶 JSON 中指定的數位值不受浮點舍入行為的制約。

## <a name="case-sensitivity-of-string-filters"></a>字串篩選準則的區分大小寫

所有字串比較都不區分大小寫。 目前沒有任何方法可以變更這種行為。

## <a name="allowed-advanced-filter-keys"></a>允許的 advanced filter 金鑰

`Key`屬性可以是知名的最上層屬性，或為具有多個點的 json 路徑，其中每個點表示逐步執行為嵌套 json 物件。

不同于 JSONPath 規格，事件方格對索引鍵中的字元沒有任何特殊意義 `$` 。

### <a name="event-grid-schema"></a>事件方格架構

事件方格架構中的事件：

* 識別碼
* 主題
* 主旨
* EventType
* DataVersion
* Data. Prop1
* Data. This.prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>自訂事件架構

`Key`自訂事件架構中沒有的限制，因為事件方格不會在裝載上強制執行任何信封架構。

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
