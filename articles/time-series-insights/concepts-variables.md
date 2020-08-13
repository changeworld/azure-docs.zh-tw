---
title: 模型變數-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 模型變數
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: d0a5b48e93e839b0a0adaf185700d7f60fec7948
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140678"
---
# <a name="time-series-model-variables"></a>時間序列模型變數

本文描述在事件上指定公式和計算規則的時間序列模型變數。

每個變數都可以是下列三種類型之一：*數值*、*類別*和*匯總*。

* **數值**類型可搭配連續數值使用。
* **類別**類型會使用一組已定義的離散值。
* **匯總**種類結合了單一類型的多個變數 (所有數值或所有類別) 。

下表顯示每個變數種類的相關屬性。

[![時間序列模型變數資料表](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>數值變數

| Variable 屬性 | 說明 |
| --- | ---|
| 變數篩選條件 | 篩選是選擇性的條件子句，可限制要考慮計算的資料列數目。 |
| 變數值 | 用於計算來自裝置或感應器，或使用時間序列運算式轉換的遙測值。 數數值型別變數必須是*Double*類型。|
| 變數插補 | 插補會指定如何使用現有的資料來重建信號。 [*步驟*] 和 [*線性*插補] 選項適用于數值變數。 |
| 變數彙總 | 透過[數值變數種類的支援彙總函式來](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind)執行計算。 |

變數符合下列 JSON 範例：

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>類別變數

| Variable 屬性 | 說明 |
| --- | ---|
| 變數篩選條件 | 篩選是選擇性的條件子句，可限制要考慮計算的資料列數目。 |
| 變數值 | 用於計算來自裝置或感應器的遙測值。 類別類型變數必須是*Long*或*String*。 |
| 變數插補 | 插補會指定如何使用現有的資料來重建信號。 [*步驟*內插補點] 選項適用于類別變數。 |
| 變數類別 | 類別會在來自裝置或感應器的值與標籤之間建立對應。 |
| 變數預設分類 | 預設類別是針對未在 [分類] 屬性中對應的所有值。 |

變數符合下列 JSON 範例：

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>匯總變數

| Variable 屬性 | 說明 |
| --- | ---|
| 變數篩選條件 | 篩選是選擇性的條件子句，可限制要考慮計算的資料列數目。 |
| 變數彙總 | 透過[匯總變數種類支援的彙總函式來](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind)執行計算。 |

變數符合下列 JSON 範例：

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

變數會儲存在時間序列模型的類型定義中，而且可以透過 Api 以內嵌方式提供，以覆寫或補充儲存的定義。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[時間序列模型](./concepts-model-overview.md)。

* 深入瞭解如何使用[查詢 api](./concepts-query-overview.md)，以內嵌方式定義變數。
