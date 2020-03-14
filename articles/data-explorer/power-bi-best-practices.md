---
title: 使用 Power BI 來查詢 Azure 資料總管資料並將其視覺化的最佳做法
description: 在本文中，您將瞭解使用 Power BI 來查詢 Azure 資料總管資料並將其視覺化的最佳做法。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251735"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>使用 Power BI 來查詢 Azure 資料總管資料並將其視覺化的最佳做法

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 [Power BI](https://docs.microsoft.com/power-bi/)是一種商務分析解決方案，可讓您將資料視覺化，並在整個組織中共用結果。 Azure 資料總管提供三個選項來連接 Power BI 中的資料。 使用[內建的連接器](power-bi-connector.md)，將[查詢從 Azure 資料總管匯入 Power BI](power-bi-imported-query.md)，或使用[SQL 查詢](power-bi-sql-query.md)。 本文提供使用 Power BI 查詢和視覺化 Azure 資料總管資料的秘訣。 

## <a name="best-practices-for-using-power-bi"></a>使用 Power BI 的最佳做法 

使用數 tb 的最新原始資料時，請遵循下列指導方針，讓 Power BI 的儀表板和報表保持 snappy 和更新：

* **旅遊燈**-只將您的報表所需的資料帶到 Power BI。 如需深入的互動分析，請使用已針對使用 Kusto 查詢語言進行臨機操作探索優化的[Azure 資料總管 WEB UI](web-query-data.md) 。

* **複合模型**-使用[複合模型](https://docs.microsoft.com/power-bi/desktop-composite-models)，將最上層儀表板的匯總資料與已篩選的操作原始資料結合。 您可以清楚定義何時使用原始資料，以及何時使用匯總視圖。 

* 匯**入模式與 DirectQuery 模式**-使用匯**入**模式來互動較小的資料集。 針對大型且經常更新的資料集，請使用**DirectQuery**模式。 例如，您可以使用 [匯**入**] 模式建立維度資料表，因為它們很小，而且不常變更。 根據預期的資料更新速率，設定重新整理間隔。 使用**DirectQuery**模式建立事實資料表，因為這些資料表很大，而且包含原始資料。 使用這些資料表，利用 Power BI 的[鑽取](https://docs.microsoft.com/power-bi/desktop-drillthrough)來呈現已篩選的資料。

* **平行**處理原則– Azure 資料總管是可線性調整的資料平臺，因此，您可以藉由增加端對端流程的平行處理原則，改善儀表板轉譯的效能，如下所示：

   * 在 Power BI 中增加[DirectQuery 的並行連接](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)數目。

   * 使用[弱式一致性來改善平行](/azure/kusto/concepts/queryconsistency)處理原則。 這可能會影響資料的時效性。

* **有效**的交叉分析篩選器–使用[同步](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages)交叉分析篩選器，在您準備好之前，讓報表無法載入資料。 在您建立資料集的結構之後，請放置所有視覺效果，並標示所有交叉分析篩選器，您可以選取 [同步處理交叉分析篩選器]，只載入所需的資料。

* **使用篩選**-盡可能使用多個 Power BI 篩選準則，將 Azure 資料總管搜尋重點放在相關的資料分區上。

* **有效率的視覺效果**–為您的資料選取最具效能的視覺效果。

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>使用 Azure 資料總管 connector 進行 Power BI 以查詢資料的秘訣

下一節包含使用 Kusto 查詢語言搭配 Power BI 的秘訣和訣竅。 使用[適用于 Power BI 的 Azure 資料總管連接器](power-bi-connector.md)來將資料視覺化

### <a name="complex-queries-in-power-bi"></a>Power BI 中的複雜查詢

複雜的查詢在 Kusto 中的表示方式比 Power Query 更輕鬆。 它們應該實作為[Kusto](/azure/kusto/query/functions)函式，並在 Power BI 中叫用。 在 Kusto 查詢中使用**DirectQuery**搭配 `let` 語句時，這是必要的方法。 因為 Power BI 聯結兩個查詢，而且 `let` 語句不能與 `join` 運算子一起使用，所以可能會發生語法錯誤。 因此，請將聯結的每個部分儲存為 Kusto 函式，並允許 Power BI 同時聯結這兩個函數。

### <a name="how-to-simulate-a-relative-date-time-operator"></a>如何模擬相對的日期時間運算子

Power BI 不包含*相對*的日期時間運算子，例如 `ago()`。
若要模擬 `ago()`，請使用 `DateTime.FixedLocalNow()` 和 `#duration` Power BI 函數的組合。

而不是使用 `ago()` 運算子的此查詢：

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

使用下列對等的查詢：

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>達到 Kusto 查詢限制 

根據預設，Kusto 查詢會傳回最多500000個數據列或 64 MB，如[查詢限制](/azure/kusto/concepts/querylimits)中所述。 您可以使用 [ **Azure 資料總管（Kusto）** 連接] 視窗中的 [ **Advanced options** ] 來覆寫這些預設值：

![advanced 選項](media/power-bi-best-practices/advanced-options.png)

這些選項會使用您的查詢發出[set 語句](/azure/kusto/query/setstatement)，以變更預設查詢限制：

  * **限制查詢結果記錄號碼**會產生 `set truncationmaxrecords`
  * **限制查詢結果資料大小（以位元組為單位）** 會產生 `set truncationmaxsize`
  * **停用結果集截斷**會產生 `set notruncation`

### <a name="using-query-parameters"></a>使用查詢參數

您可以使用[查詢參數](/azure/kusto/query/queryparametersstatement)來動態修改查詢。 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>使用連接詳細資料中的查詢參數

使用查詢參數來篩選查詢中的資訊，並將查詢效能優化。
 
在 [**編輯查詢**] 視窗中， **Home** > **進階編輯器**

1. 尋找查詢的下列區段：

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   例如：

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. 以您的參數取代查詢的相關部分。 將查詢分割為多個部分，並使用連字號（&）和參數將其串連回來。

   例如，在上述查詢中，我們會採用 `State == 'ALABAMA'` 部分，並將其分割為： `State == '` 和 `'`，而我們會在兩者之間放置 `State` 參數：
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. 如果您的查詢包含引號，請正確編碼。 例如，下列查詢： 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   會出現在**進階編輯器**中，並以兩個引號括住：

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   應該使用下列查詢取代為三個引號：

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>在查詢步驟中使用查詢參數

您可以在任何支援的查詢步驟中使用查詢參數。 例如，根據參數的值來篩選結果。

![使用參數篩選結果](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>請勿使用 Power BI 資料重新整理排程器將控制命令發行至 Kusto

Power BI 包括可定期針對資料來源發出查詢的資料重新整理排程器。 此機制不應用來將控制命令排程為 Kusto，因為 Power BI 假設所有查詢都是唯讀的。

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI 只能將簡短（&lt;2000 個字元）查詢傳送至 Kusto

如果在 Power BI 中執行查詢，會產生下列錯誤：「_資料來源錯誤： Web。內容無法從 ... 取得內容_...」查詢長度可能超過2000個字元。 Power BI 使用**PowerQuery**來查詢 Kusto，方法是發出 HTTP GET 要求，將查詢編碼為所抓取 URI 的一部分。 因此，Power BI 所發出的 Kusto 查詢會限制為要求 URI 的最大長度（2000個字元，減去小位移）。 因應措施是，您可以在 Kusto 中定義[預存](/azure/kusto/query/schema-entities/stored-functions)函式，並讓 Power BI 在查詢中使用該函數。

## <a name="next-steps"></a>後續步驟

[使用適用于 Power BI 的 Azure 資料總管連接器將資料視覺化](power-bi-connector.md)




