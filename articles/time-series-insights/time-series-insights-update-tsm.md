---
title: 時間序列模型 - Azure 時間序列見解 |微軟文檔
description: 在 Azure 時間序列見解預覽中瞭解時間序列模型。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476649"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Azure 時間序列見解預覽中的時間序列模型

本文介紹時間序列模型、功能以及如何在 Azure 時間序列預覽環境中開始構建和更新您自己的模型。

> [!TIP]
>  * 轉到 [Contoso 風電場演示](https://insights.timeseries.azure.com/preview/samples)環境，獲得即時時間序列模型示例。
> * 閱讀有關[Azure 時間序列見解預覽資源管理器](time-series-insights-update-explorer.md)，瞭解如何導航時序模型 UI。
> * 瞭解如何使用時序見解 Web 資源管理器[使用時間序列模型](time-series-insights-update-how-to-tsm.md)。

## <a name="summary"></a>總結

傳統上，從 IoT 裝置收集的資料缺發關聯的資訊，因此很難快速地尋找及分析感應器。 時間序列模型的主要動機是簡化對 IoT 或時間序列資料的查找和分析。 它啟用時間序列資料的整理、維護和豐富，以説明準備面向消費者的資料集進行分析，從而實現這一目標。

## <a name="scenario-contosos-new-smart-oven"></a>場景：康托索的新智慧烤箱

**考慮 Contoso 智慧烤箱的虛構場景。** 在這種情況下，假設每個 Contoso 智慧烤箱有五個溫度感應器，一個用於四個頂部燃燒器，一個用於烤箱本身。 直到最近，每個 Contoso 溫度感應器都單獨發送、存儲和視覺化其資料。 在廚房電器監控方面，Contoso 依靠基本圖表，每個感應器一個。

雖然 Contoso 對其初始資料和視覺化解決方案感到滿意，但有幾個限制變得明顯：

* 客戶想知道當大多數頂級燃燒器打開時，整個烤箱會有多熱。 Contoso 在分析和提出關於整個烤箱條件的統一答案方面更加困難。
* Contoso 工程師希望驗證同時運行的頂級燃燒器不會導致低效的功耗。 交叉引用哪些溫度和電壓感應器彼此關聯以及如何在存儲中定位它們，都很困難。
* Contoso 品質保證團隊希望審核和比較兩個感應器版本之間的歷史記錄。 很難確定哪些資料屬於哪個感應器版本。

由於無法構建、組織和定義總體智慧烤箱時間序列模型，每個溫度感應器都會保持資料點錯位、隔離且資訊不足。 將這些資料點轉換為可操作的見解更加困難，因為每個資料集都獨立于其他資料集。

這些限制揭示了智慧資料聚合和視覺化檢視對 Contoso 新烤箱的重要性：

* 當您能夠將資料關聯併合並到方便的視圖中時，資料視覺化證明非常有用。 一個例子是顯示電壓感應器和溫度感應器。
* 管理多個實體的多維資料以及比較、縮放和時間範圍功能可能難以完成。

時間序列模型為本虛構示例中遇到的許多方案**提供了一個方便的解決方案**：

[![時間序列 模型智慧烤箱圖表示例](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* 時序模型在查詢和導航中起著至關重要的作用，因為它允許跨時間範圍以及感應器和裝置類型之間的比較來對資料進行上下文化。 （**A**） 
* 資料進一步上下文化，因為時間序列模型中保留的資料將保留時間序列查詢計算為變數，並在查詢時重用它們。
* 時間序列模型組織和聚合資料，以提高視覺化和管理功能。 （**B**） 

### <a name="key-capabilities"></a>主要功能

為了達到簡單輕鬆就能管理時間序列關聯化的目的，時間序列模型在時間序列深入解析中提供下列功能。 它可協助您：

* 使用標量函數、聚合操作等編寫和管理計算或公式。
* 定義父子關係以啟用導航、搜索和引用。
* 定義與實例關聯的屬性，定義為*實例欄位*，並使用它們創建層次結構。

### <a name="components"></a>元件

時間序列模型有三個核心元件：

* [時間序列模型實例](#time-series-model-instances)
* [時間序列模型層次結構](#time-series-model-hierarchies)
* [時間序列模型類型](#time-series-model-types)

這些元件組合起來以指定時間序列模型並組織 Azure 時序見解資料。

[![時序模型概覽圖](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

可以通過[時序見解預覽](time-series-insights-update-how-to-tsm.md)介面創建和管理時序模型。 時間序列模型設置可以通過[模型設置 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)進行管理。

## <a name="time-series-model-instances"></a>時間序列模型執行個體

時序模型*實例*是時間序列本身的虛擬表示形式。

在大多數情況下，實例由**設備 Id**或**assetId**唯一標識，這些參數保存為時間序列 ID。

實例具有與其關聯的描述性資訊，稱為*實例屬性*，如時間序列 ID、類型、名稱、描述、層次結構和實例欄位。 執行個體屬性至少會包含階層資訊。

*實例欄位*是描述性資訊的集合，可以包括層次結構級別以及製造商、操作員等的值。

為時序見解環境配置事件源後，實例將自動在時間序列模型中發現和創建。 可以使用時序模型查詢，通過時序見解資源管理器創建或更新實例。

[Contoso 風電場演示](https://insights.timeseries.azure.com/preview/samples)提供了幾個即時實例示例。

[![時間序列模型實例示例](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>實例屬性

實例由**時間序列Id、typeId、****名稱**、**描述**、**層次結構 Id**和**實例欄位**定義。 **typeId** 每個實例只映射到一*個類型*，以及一個或多個*層次結構*。

| 屬性 | 描述 |
| --- | ---|
| 時間序列Id | 實例關聯的時間序列的 UUID。 |
| typeId | 時間序列模型的 UUID 類型實例與之關聯。 預設情況下，所有發現的新實例都與預設類型相關聯。
| NAME | **名稱**屬性是可選的，區分大小寫。 如果**名稱**不可用，則預設為**timeSeriesId**。 如果提供了名稱，**則時間序列Id**在[井](time-series-insights-update-explorer.md#4-time-series-well)中仍然可用。 |
| description | 實例的文本說明。 |
| 層次結構 Id | 定義實例所屬的層次結構。 |
| 實例欄位 | 實例的屬性和定義實例的任何靜態資料。 它們定義階層或非階層的值，同時也支援建立索引以執行搜尋作業。 |

> [!NOTE]
> 層次結構是通過使用實例欄位構建的。 可以為進一步的實例屬性定義添加其他**實例欄位**。

實例具有以下 JSON 表示形式：

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> 對於時間序列見解實例 API 以及創建、讀取、更新和刪除 （CRUD） 支援，請閱讀[資料查詢](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)文章和[實例 API REST 文檔](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)。

## <a name="time-series-model-hierarchies"></a>時間序列模型階層

時序模型*層次結構*通過指定屬性名稱及其關係來組織實例。

您可以在給定的時間序列見解環境中配置多個層次結構。 時間序列模型實例可以映射到單個層次結構或多個層次結構（多對多關係）。

[Contoso 風電場演示](https://insights.timeseries.azure.com/preview/samples)用戶端介面顯示標準實例和類型層次結構。

[![時間序列模型層次結構示例](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>層次結構定義

層次結構由層次結構**ID、****名稱**和**源**定義。

| 屬性 | 描述 |
| ---| ---|
| id | 層次結構的唯一識別碼，例如，在定義實例時使用。 |
| NAME | 用於為層次結構提供名稱的字串。 |
| source | 指定組織層次結構或路徑，這是使用者要創建的層次結構的自上而下的父子順序。 父子式屬性會對應「執行個體欄位」。 |

層次結構在 JSON 中表示為：

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

在前面的 JSON 示例中：

* `Location`定義具有父`states`項和子`cities`的層次結構。 每個`location`可以有多個`states`，而後者可以有多個`cities`。
* `ManufactureDate`定義具有父`year`項和子`month`的層次結構。 每個`ManufactureDate`可以有多個`years`，而後者可以有多個`months`。

> [!TIP]
> 有關時間序列見解實例 API 和 CRUD 支援，請閱讀[資料查詢](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)文章和[層次結構 API REST 文檔](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)。

### <a name="hierarchy-example"></a>階層範例

考慮層次結構**H1**具有`building`的示例`floor`，並作為`room`**實例 FieldNames**定義的一部分：

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

給定上一個定義和多個時間序列中使用的實例欄位，層次結構屬性和值如下所示：

| 時間序列識別碼 | 執行個體欄位 |
| --- | --- |
| ID1 | "建築" = "1000"，"地板" = "10"，"房間" = "55"  |
| ID2 | "建築" = "1000"，"房間" = "55" |
| ID3 | "地板" = "10" |
| ID4 | "建築" = "1000"，"地板" = "10"  |
| ID5 | 沒有設置"建築"、"地板"或"房間"。 |

時序**ID1**和**ID4**在[Azure 時間序列見解資源管理器](time-series-insights-update-explorer.md)中顯示為層次結構**H1**的一部分，因為它們已完全定義並正確排列*了建築物*、*樓層*和*房間*參數。

其他類實例在*非父級實例*下分類，因為它們不符合指定的資料層次結構。

## <a name="time-series-model-types"></a>時間序列模型類型

時間序列模型「類型」** 協助您定義執行計算的變數或公式。 類型與特定的時序見解實例相關聯。

一個類型可以有一或多個變數。 例如，時間序列模型實例可以是 *"溫度感應器"* 類型，該類型由*平均值*、*最小溫度*和*最大溫度*變數組成。

[Contoso 風電場演示](https://insights.timeseries.azure.com/preview/samples)視覺化了與其各自的實例關聯的多個時間序列模型類型。

[![時間序列模型類型示例](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> 有關時間序列見解實例 API 和 CRUD 支援，請閱讀[資料查詢](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)文章和[類型 API REST 文檔](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)。

### <a name="type-properties"></a>類型屬性

時間序列模型類型由**id、****名稱**、**描述**和**變數**定義。

| 屬性 | 描述 |
| ---| ---|
| id | 類型的 UUID。 |
| NAME | 用於提供類型名稱的字串。 |
| description | 類型的字串說明。 |
| variables | 指定與類型關聯的變數。 |

類型符合以下 JSON 示例：

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>變數

時間序列見解類型可能有許多變數，用於指定事件上的公式和計算規則。

每個變數可以是三*種類型*之一：*數位*、*分類*和*聚合*。

* **數值**類型使用連續值。 
* **分類**類型使用一組定義的離散值。
* **聚合**值合併單個類型的多個變數（所有數位或所有分類）。

下表顯示了與每種變數類型相關的屬性。

[![時間序列 模型變數表](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>數值變數

| 變數屬性 | 描述 |
| --- | ---|
| 變數篩選條件 | 篩選器是可選的條件子句，用於限制考慮計算的行數。 |
| 變數值 | 遙測值，用於計算來自設備或感應器或使用時序運算式轉換。 數數值型別的變數必須為 *"雙*"類型。|
| 變數插值 | 插值指定如何使用現有資料重建信號。 *步長*和*線性*插值選項可用於數值變數。 |
| 變數彙總 | 支援計算通過*平均，**最小*，*最大*，*總和*，*計數*，*第一*，*最後*和時間加權 （*平均*，*最小*，*最大*，*總和*，*左*） 運算子. |

變數符合以下 JSON 示例：

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>分類變數

| 變數屬性 | 描述 |
| --- | ---|
| 變數篩選條件 | 篩選器是可選的條件子句，用於限制考慮計算的行數。 |
| 變數值 | 用於計算來自設備或感應器的遙測值。 分類類變數必須為*長*變數或*字串*。 |
| 變數插值 | 插值指定如何使用現有資料重建信號。 "*步驟*插值"選項可用於分類變數。 |
| 變數類別 | 類別在來自設備或感應器的值與標籤之間創建映射。 |
| 可變預設類別 | 預設類別適用于未在"類別"屬性中映射的所有值。 |

變數符合以下 JSON 示例：

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>聚合變數

| 變數屬性 | 描述 |
| --- | ---|
| 變數篩選條件 | 篩選器是可選的條件子句，用於限制考慮計算的行數。 |
| 變數彙總 | 支援計算通過*平均，**最小*，*最大*，*總和*，*計數*，*第一*，*最後*. |

變數符合以下 JSON 示例：

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

變數存儲在時間序列模型的類型定義中，可以通過[查詢 API](time-series-insights-update-tsq.md)提供內聯式，以覆蓋存儲的定義。

## <a name="next-steps"></a>後續步驟

- 閱讀[Azure 時間序列見解 預覽存儲和入口](./time-series-insights-update-storage-ingress.md)。

- 在[Azure 時間序列見解預覽中瞭解資料建模中](./time-series-insights-update-how-to-tsm.md)的常見時間序列模型操作

- 閱讀新的[時序模型](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)參考文檔。
