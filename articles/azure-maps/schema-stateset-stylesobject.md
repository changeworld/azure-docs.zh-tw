---
title: 動態 Azure 地圖服務的 StylesObject
description: 用於在動態 Azure 地圖服務中建立之 StylesObject 的 JSON 架構和語法參考指南。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8eb4e49e6c0e3f011015d40b8eca036d5218674c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891694"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>動態地圖的 StylesObject 架構參考指南

本文是 JSON 架構和語法的參考指南 `StylesObject` 。 `StylesObject`是 `StyleObject` 代表 stateset 樣式的陣列。 使用 Azure 地圖服務 Creator [功能狀態服務](/rest/api/maps/featurestate) ，將您的 stateset 樣式套用至室內地圖資料功能。 一旦您建立了 stateset 樣式，並將其與室內地圖功能相關聯，您就可以使用它們來建立動態室內地圖。 如需有關建立動態室內地圖的詳細資訊，請參閱 [為 Creator 室內地圖實行動態樣式](indoor-map-dynamic-styling.md)。

## <a name="styleobject"></a>StyleObject

`StyleObject`可以是 [`BooleanTypeStyleRule`](#booleantypestylerule) 或 [`NumericTypeStyleRule`](#numerictypestylerule) 。

以下 JSON 顯示名為 `BooleanTypeStyleRule` 的 `occupied` 和名為的 `NumericTypeStyleRule` `temperature` 。

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 `NumericTypeStyleRule`是 [`StyleObject`](#styleobject) ，其中包含下列屬性：

| 屬性 | 類型 | 描述 | 必要 |
|-----------|----------|-------------|-------------|
| `keyName` | 字串 | *狀態* 或動態屬性名稱。 在 `keyName` 陣列內應該是唯一的 `StyleObject` 。| 是 |
| `type` | 字串 | 值為「數值」。 | 是 |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| 具有相關聯色彩的數值樣式範圍陣列。 每個範圍都會定義 *狀態* 值滿足範圍時要使用的色彩。| 是 |

### <a name="numberruleobject"></a>NumberRuleObject

是 `NumberRuleObject` 由 [`RangeObject`](#rangeobject) 和屬性所組成 `color` 。 如果 *狀態值* 落在範圍內，則其顯示色彩將會是屬性中所指定的色彩 `color` 。

如果您定義多個重迭的範圍，選擇的色彩將會是所滿足的第一個範圍中所定義的色彩。

在下列 JSON 範例中，當 *狀態值* 介於50-60 之間時，這兩個範圍都會保留 true。 不過，將使用的色彩是 `#343deb` 因為它是清單中已滿足的第一個範圍。

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| 屬性 | 類型 | 描述 | 必要 |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject)會定義一組邏輯範圍條件，如果將 `true` *狀態* 的顯示色彩變更為屬性中所指定的色彩，則為 `color` 。 如果未 `range` 指定，則一律會使用屬性中定義的色彩 `color` 。   | 否 |
| `color` | 字串 | 當狀態值落在範圍內時，所要使用的色彩。 `color`屬性是下列任何一種格式的 JSON 字串： <ul><li> HTML 樣式的十六進位值 </li><li> RGB ( "#ff0"、"#ffff00"、"rgb (255、255、0) " ) </li><li> RGBA ( "rgba (255、255、0、1) " ) </li><li> HSL ( "hsl (100，50%，50% ) " ) </li><li> HSLA ( "HSLA (100，50%，50%，1) " ) </li><li> 預先定義的 HTML 色彩名稱，例如黃色和藍色。</li></ul> | 是 |

### <a name="rangeobject"></a>RangeObject

會 `RangeObject` 定義的數值範圍值 [`NumberRuleObject`](#numberruleobject) 。 針對要落在範圍內的 *狀態值* ，所有定義的條件都必須保留 true。 

| 屬性 | 類型 | 描述 | 必要 |
|-----------|----------|-------------|-------------|
| `minimum` | double | X ≥的所有數位 x `minimum` 。| 否 |
| `maximum` | double | X ≤的所有數位 x `maximum` 。 | 否 |
| `exclusiveMinimum` | double | X > 的數位 x `exclusiveMinimum` 。| 否 |
| `exclusiveMaximum` | double | X < 的數位 x `exclusiveMaximum` 。| 否 |

### <a name="example-of-numerictypestylerule"></a>NumericTypeStyleRule 範例

下列 JSON 說明 `NumericTypeStyleRule` *state* 名為的狀態 `temperature` 。 在此範例中， [`NumberRuleObject`](#numberruleobject) 包含兩個已定義的溫度範圍與其相關聯的色彩樣式。 如果溫度範圍是50-69，則顯示應該使用色彩 `#343deb` 。  如果溫度範圍是31-70，則顯示應該使用色彩 `#eba834` 。

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

`BooleanTypeStyleRule`是 [`StyleObject`](#styleobject) ，其中包含下列屬性：

| 屬性 | 類型 | 描述 | 必要 |
|-----------|----------|-------------|-------------|
| `keyName` | 字串 |  *狀態* 或動態屬性名稱。  在 `keyName` 樣式陣列中應該是唯一的。| 是 |
| `type` | 字串 |值為「布林值」。 | 是 |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)單| 具有和狀態值色彩的布林 `true` `false` *state* 值組。| 是 |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject`定義 `true` 和值的色彩 `false` 。

| 屬性 | 類型 | 描述 | 必要 |
|-----------|----------|-------------|-------------|
| `true` | 字串 | *狀態* 值為時要使用的色彩 `true` 。 `color`屬性是下列任何一種格式的 JSON 字串： <ul><li> HTML 樣式的十六進位值 </li><li> RGB ( "#ff0"、"#ffff00"、"rgb (255、255、0) " ) </li><li> RGBA ( "rgba (255、255、0、1) " ) </li><li> HSL ( "hsl (100，50%，50% ) " ) </li><li> HSLA ( "HSLA (100，50%，50%，1) " ) </li><li> 預先定義的 HTML 色彩名稱，例如黃色和藍色。</li></ul>| 是 |
| `false` | 字串 | *狀態* 值為時要使用的色彩 `false` 。 | 是 |

### <a name="example-of-booleantypestylerule"></a>BooleanTypeStyleRule 範例

下列 JSON 說明 `BooleanTypeStyleRule` *state* 名為的狀態 `occupied` 。 會 [`BooleanRuleObject`](#booleanruleobject) 定義 `true` 和值的色彩 `false` 。

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```