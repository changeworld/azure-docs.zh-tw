---
title: 映射資料流程腳本
description: 資料工廠資料流程腳本代碼支援語言概述
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 92421125ecb5f4336922c6e6b4508fcdaf92be6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246393"
---
# <a name="data-flow-script-dfs"></a>資料流程腳本 （DFS）

資料流程腳本 （DFS） 是基礎中繼資料，類似于編碼語言，用於執行映射資料流程中包含的轉換。 每個轉換都由一系列屬性工作表示，這些屬性提供了正確運行作業的必要資訊。 通過按一下瀏覽器 UI 頂部功能區上的"腳本"按鈕，該腳本可以從 ADF 可見和編輯。

![腳本按鈕](media/data-flow/scriptbutton.png "腳本按鈕")

例如，`allowSchemaDrift: true,`在源轉換中，告訴服務將來自源資料集的所有列包括在資料流程中，即使它們未包含在架構投影中也是如此。

## <a name="use-cases"></a>使用案例
DFS 由使用者介面自動生成。 您可以按一下"腳本"按鈕來查看和自訂腳本。 您還可以在 ADF UI 之外生成腳本，然後將腳本傳遞到 PowerShell Cmdlet 中。 調試複雜資料流程時，您可能會發現掃描腳本代碼後面而不是掃描流的 UI 圖形表示形式更容易。

下面是幾個示例用例：
- 以程式設計方式生成許多相當相似的資料流程，即"衝壓"資料流程。
- 在 UI 中難以管理或導致驗證問題的複雜運算式。
- 調試並更好地瞭解執行期間返回的各種錯誤。

構建要與 PowerShell 或 API 一起使用的資料流程腳本時，必須將格式化的文本折疊成一行。 您可以將選項卡和分行符號保留為逸出字元。 但是，文本的格式必須適合 JSON 屬性。 腳本編輯器 UI 底部有一個按鈕，該按鈕將腳本格式化為單行。

![[複製] 按鈕](media/data-flow/copybutton.png "[複製] 按鈕")

## <a name="how-to-add-transforms"></a>如何添加轉換
添加轉換需要三個基本步驟：添加核心轉換資料、重新路由輸入流，然後重新路由輸出流。 這在示例中是最容易的。
假設我們從一個簡單的源開始，以像以下內容一樣來存儲資料流程：

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

如果我們決定添加派生變換，首先我們需要創建核心轉換文本，該文本具有一個簡單的運算式來添加新大寫列，稱為`upperCaseTitle`：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

然後，我們採用現有的 DFS 並添加轉換：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

現在，我們通過確定我們希望新轉換之後的轉換（在本例中`source1`）並將流的名稱複製到新轉換來重新路由傳入流：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

最後，我們確定要在此新轉換之後進行轉換，並將其輸入流（本例中`sink1`）替換為新轉換的輸出流名稱：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS 基礎知識
DFS 由一系列連接的轉換組成，包括源、接收器和其他各種轉換，這些轉換可以添加新列、篩選資料、聯接資料等。 通常，腳本以一個或多個源開頭，然後是許多轉換，最後以一個或多個接收器結束。

源都具有相同的基本構造：
```
source(
  source properties
) ~> source_name
```

例如，包含三列（電影 Id、標題、流派）的簡單源是：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

源以外的所有轉換都具有相同的基本構造：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，採用列（標題）並用大寫版本覆蓋它的簡單派生轉換如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

沒有架構的接收器只是：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>腳本程式碼片段

### <a name="aggregated-summary-stats"></a>匯總匯總統計資訊
向資料流程添加名為"SummaryStats"的聚合轉換，然後粘貼到下面的代碼中，用於腳本中的彙總函式，替換現有的 SummaryStats。 這將為數據設定檔摘要統計資訊提供通用模式。

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
您還可以使用以下示例來計算資料中唯一行數和不同行數。 下面的示例可以粘貼到具有稱為 ValueDistAgg 的聚合轉換的資料流程中。 此示例使用名為"標題"的列。 請確保將"標題"替換為要用於獲取值計數的資料中的字串列。

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>將所有列包含在聚合中
這是一種通用聚合模式，演示如何在生成聚合時將剩餘列保留在輸出中繼資料中。 在這種情況下，我們使用 函數```first()```選擇名稱不是"movie"的每個列中的第一個值。 要使用此，請創建名為"明顯行"的聚合轉換，然後將該轉換粘貼到現有"明顯行"聚合腳本的頂部。

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>創建行雜湊指紋 
在資料流程腳本中使用此代碼創建一個稱為生成```DWhash```三列```sha1```雜湊的新派生列。

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>後續步驟

從[資料流程概述文章](concepts-data-flow-overview.md)開始探索資料流程
