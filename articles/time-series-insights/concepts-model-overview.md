---
title: 時間序列模型-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Gen2 中的時間序列模型。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: a61dd6c17ad4d11c6dd7294c9a4f96270748c16a
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630656"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Azure 時間序列深入解析 Gen2 中的時間序列模型

本文說明時間序列模型、功能，以及如何在 Azure 時間序列深入解析 Gen2 環境中開始建立和更新您自己的模型。

> [!TIP]
>
> * 請移至 [Contoso 風伺服器陣列示範](https://insights.timeseries.azure.com/preview/samples) 環境，以取得即時時間序列模型範例。
> * 瞭解如何使用 Azure 時間序列深入解析 Explorer [來使用時間序列模型](/azure/time-series-insights/how-to-edit-your-model) 。

## <a name="summary"></a>摘要

傳統上，從 IoT 裝置收集的資料缺發關聯的資訊，因此很難快速地尋找及分析感應器。 時間序列模型的主要動機是簡化尋找及分析 IoT 或時間序列資料的動機。 它藉由啟用時間序列資料的鑒藏、維護和擴充，協助準備取用者就緒的資料集來進行分析，以達成此目標。

## <a name="scenario-contosos-new-smart-oven"></a>案例： Contoso 的新智慧型 oven

**試想 Contoso smart oven 的虛構案例。** 在此案例中，假設每個 Contoso smart oven 有五個溫度感應器，每四個最熱門的燒錄機各一個，另一個用於 oven 本身。 在最近，每個 Contoso 溫度感應器會個別傳送、儲存和視覺化其資料。 針對其廚房設備監視，Contoso 依賴基本的圖表，每個感應器各一個。

雖然 Contoso 對其初始資料和視覺效果解決方案感到滿意，但有幾項限制變得很明顯：

* 客戶想要知道在大部分的最上層燒錄機開啟時，整體 oven 會如何獲得經常性存取。 Contoso 在分析及呈現整體 oven 條件的整合答案時，比較困難。
* Contoso 工程師想要確認最常執行的燒錄機不會產生效率不佳的電源。 有一些難以交叉參照哪些溫度和電壓感應器彼此相關聯，以及如何在商店中找到它們。
* Contoso 品質保證小組想要在兩個感應器版本之間進行歷程記錄的審核和比較。 判斷哪些資料屬於哪個感應器版本時，會發生困難。

如果沒有結構、組織和定義整體智慧 oven 時間序列模型的能力，則每個溫度感應器都會維護 dislocated、隔離和資訊較少的資料點。 將這些資料點轉換成可採取動作的深入解析比較困難，因為每個資料集的存留期與其他資料集無關

這些限制揭示了 Contoso 新 oven 隨附的智慧型資料匯總和視覺化檢視的重要性：

* 當您可以建立資料的關聯並將其合併成方便的視圖時，資料視覺效果證明很有用。 顯示電壓感應器和溫度感應器的範例。
* 管理數個實體的多維度資料，以及比較、縮放和時間範圍功能，可能很難完成。

**時間序列模型** 針對此虛構範例中的許多案例提供了方便的解決方案：

[![時間序列模型智慧型 oven 圖表範例](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* 時間序列模型在查詢和導覽中扮演重要角色，因為它可讓您跨時間範圍以及在感應器和裝置類型之間繪製比較，藉此就資料。  (**) **
* 資料會進一步內容相關，因為保存在時間序列模型中的資料會將時間序列查詢計算保留為變數，並在查詢時重複使用它們。
* 時間序列模型會組織及匯總資料，以改善視覺效果和管理功能。  (**B**) 

### <a name="key-capabilities"></a>主要功能

時間序列模型可讓您輕鬆且輕鬆地管理時間序列對脈絡化，可在 Azure 時間序列深入解析 Gen2 中提供下列功能。 它可協助您：

* 撰寫和管理運用純量函數、匯總作業等的計算或公式。
* 定義父子式關聯性，以啟用導覽、搜尋和參考。
* 定義與實例相關聯的屬性（定義為 *實例欄位*），並使用它們來建立階層。

### <a name="components"></a>組件

時間序列模型有三個核心元件：

* [時間序列模型實例](#time-series-model-instances)
* [時間序列模型階層](#time-series-model-hierarchies)
* [時間序列模型類型](#time-series-model-types)

這些元件會合並以指定時間序列模型和組織您的資料。

[![時間序列模型總覽圖表](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

您可以透過 [Azure 時間序列深入解析 Explorer](/azure/time-series-insights/concepts-model-overview)來建立及管理時間序列模型。 時間序列模型設定可以透過 [模型設定 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis)來管理。

## <a name="time-series-model-instances"></a>時間序列模型執行個體

時間序列模型 *實例* 是時間序列本身的虛擬標記法。

在大多數情況下，會以 **deviceId** 或 **assetId**來唯一識別實例，這些實例會儲存為時間序列識別碼。

實例具有與其相關聯的描述性資訊，稱為 *實例屬性*，例如時間序列識別碼、類型、名稱、描述、階層和實例欄位。 執行個體屬性至少會包含階層資訊。

*實例欄位* 是描述性資訊的集合，可包含階層層級的值，以及製造商、運算子等等。

針對 Azure 時間序列深入解析 Gen2 環境設定事件來源之後，系統會自動在時間序列模型中探索和建立實例。 您可以使用時間序列模型查詢，透過 Azure 時間序列深入解析 Explorer 來建立或更新實例。

[Contoso 風伺服器陣列示範](https://insights.timeseries.azure.com/preview/samples)提供數個即時實例範例。

[![時間序列模型實例範例](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>實例屬性

實例是由 **timeSeriesId**、 **typeId**、 **name**、 **description**、 **hierarchyIds**和 **instanceFields**所定義。 每個實例只能對應到一個 *類型*，以及一個或 *多個*階層。

| 屬性 | 描述 |
| --- | ---|
| timeSeriesId | 與實例相關聯之時間序列的唯一識別碼。 在大部分情況下，實例是由 deviceId 或 assetId 之類的屬性來唯一識別。 在某些情況下，可以使用更明確的複合識別碼，最多可結合3個屬性。 |
| typeId | 與實例相關聯的時間序列模型類型之區分大小寫的唯一字串識別碼。 依預設，所有探索到的新實例都會與預設型別建立關聯。
| name | **Name**屬性是選擇性的，且區分大小寫。 如果 **名稱** 無法使用，則預設為 **timeSeriesId**。 如果有提供名稱， **timeSeriesId**仍可供[使用。](time-series-insights-update-explorer.md#4-time-series-well) |
| description | 實例的文字描述。 |
| hierarchyIds | 定義實例所屬的階層。 |
| instanceFields | 實例的屬性，以及定義實例的任何靜態資料。 它們定義階層或非階層的值，同時也支援建立索引以執行搜尋作業。 |

> [!NOTE]
> 您可以使用實例欄位來建立階層。 您可以針對進一步的實例屬性定義加入其他 **instanceFields** 。

實例具有下列 JSON 標記法：

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
> 如需 API 建立、讀取、更新和刪除 (CRUD) 支援，請參閱 [資料查詢](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) 文章和 [實例 API REST 檔](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api)。

## <a name="time-series-model-hierarchies"></a>時間序列模型階層

時間 *序列模型階層* 藉由指定屬性名稱和其關聯性來組織實例。

您可以在指定的 Azure 時間序列深入解析 Gen2 環境中設定多個階層。 時間序列模型實例可以對應到單一階層或多個階層， (多對多關聯性) 。

[Contoso 風伺服器陣列示範](https://insights.timeseries.azure.com/preview/samples)會顯示標準實例和類型階層。

[![時間序列模型階層範例](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>階層定義

階層是由階層 **識別碼**、 **名稱**和 **來源**所定義。

| 屬性 | 描述 |
| ---| ---|
| id | 階層的唯一識別碼，如您定義實例時所使用的識別碼。 |
| name | 用來提供階層名稱的字串。 |
| source | 指定組織階層或路徑，這是使用者想要建立之階層的由上而下父子式訂單。 父子式屬性會對應「執行個體欄位」。 |

階層會以 JSON 表示為：

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

在先前的 JSON 範例中：

* `Location` 定義具有父系和子系的階層 `states` `cities` 。 每一個都 `location` 可以有多個 `states` ，也可以有多個 `cities` 。
* `ManufactureDate` 定義具有父系和子系的階層 `year` `month` 。 每一個都 `ManufactureDate` 可以有多個 `years` ，也可以有多個 `months` 。

> [!TIP]
> 如需階層 API 建立、讀取、更新和刪除 (CRUD) 支援，請參閱 [資料查詢](concepts-query-overview.md#time-series-model-query-tsm-q-apis) 文章和階層 [API REST 檔](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api)。

### <a name="hierarchy-example"></a>階層範例

假設有一個範例，其中的階層 **H1** 具有 `building` 、 `floor` 和 `room` 做為其 **instanceFieldNames** 定義的一部分：

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

由於先前的定義和數個時間序列中使用的實例欄位，階層屬性和值會顯示如下表所示：

| 時間序列識別碼 | 執行個體欄位 |
| --- | --- |
| ID1 | "大樓" = "1000"，"floor" = "10"，"會議室" = "55"  |
| ID2 | "大樓" = "1000"，"會議室" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "大樓" = "1000"，"floor" = "10"  |
| ID5 | 未設定任何「建築物」、「樓層」或「房間」。 |

時間序列**ID1**和**ID4**在[Azure 時間序列深入解析 Explorer](time-series-insights-update-explorer.md)中會顯示為階層**H1**的一部分，因為它們具有完整定義和正確排序的*建築物*、*樓層*和*房間*參數。

其他則分類于 *無上層實例* 底下，因為它們不符合指定的資料階層。

## <a name="time-series-model-types"></a>時間序列模型類型

時間序列模型「類型」** 協助您定義執行計算的變數或公式。 類型會與特定的實例相關聯。

一個類型可以有一或多個變數。 例如，時間序列模型實例可能是 *溫度感應器*類型，其中包含變數 *平均溫度*、 *最小溫度*和 *最大溫度*。

[Contoso 風伺服器陣列示範](https://insights.timeseries.azure.com/preview/samples)會視覺化數個與個別實例相關聯的時間序列模型類型。

[![時間序列模型類型範例](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> 如需 API 建立、讀取、更新和刪除 (CRUD) 支援的類型，請參閱 [資料查詢](concepts-query-overview.md#time-series-model-query-tsm-q-apis) 文章和 [類型 api REST 檔](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api)。

### <a name="type-properties"></a>類型屬性

時間序列模型類型是依 **識別碼**、 **名稱**、 **描述**和 **變數**來定義。

| 屬性 | 描述 |
| ---| ---|
| id | 類型的區分大小寫唯一字串識別碼。 |
| name | 用來提供類型名稱的字串。 |
| description | 類型的字串描述。 |
| variables | 指定與類型相關聯的變數。 |

類型符合下列 JSON 範例：

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
      }
    }
  ]
}
```

時間序列模型類型可以有許多變數，以指定事件的公式和計算規則。 深入瞭解 [如何定義時間序列模型變數](./concepts-variables.md)

## <a name="next-steps"></a>後續步驟

* 如需有關如何透過 Api 編輯模型的詳細資訊，請參閱 [時間序列模型](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis) 參考檔。

* 探索您可以使用[時間序列模型變數](./concepts-variables.md)建立的公式和計算

* 瞭解如何在 Azure 時間序列深入解析 Gen2 中[查詢資料](concepts-query-overview.md)
