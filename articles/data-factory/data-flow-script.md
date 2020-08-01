---
title: 對應資料流程腳本
description: Data Factory 的資料流程腳本程式碼後置語言總覽
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2020
ms.openlocfilehash: d28cd7a7edd5d6405761bf21ee87ec39dc9ec9cb
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448546"
---
# <a name="data-flow-script-dfs"></a>資料流程腳本（DFS）

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

資料流程腳本（DFS）是基礎中繼資料，類似于編碼語言，用來執行對應資料流程中包含的轉換。 每個轉換都會以一系列的屬性來表示，以提供必要的資訊來正確執行工作。 您可以透過按一下瀏覽器 UI 頂端功能區上的 [腳本] 按鈕，從 ADF 看到腳本並加以編輯。

![腳本按鈕](media/data-flow/scriptbutton.png "腳本按鈕")

例如， `allowSchemaDrift: true,` 在來源轉換中，會告知服務在資料流程中包含來自源資料集的所有資料行，即使它們並未包含在架構投影中也一樣。

## <a name="use-cases"></a>使用案例
DFS 會由使用者介面自動產生。 您可以按一下 [腳本] 按鈕來查看和自訂腳本。 您也可以在 ADF UI 外產生腳本，然後將其傳遞至 PowerShell Cmdlet。 在進行複雜資料流程的調試時，您可能會發現，掃描腳本的程式碼後置會比較容易，而不是掃描您流程的 UI 圖形標記法。

以下是幾個範例使用案例：
- 以程式設計方式產生許多非常類似的資料流程，也就是「戳記」的資料流程。
- 難以在 UI 中管理或導致驗證問題的複雜運算式。
- 對執行期間傳回的各種錯誤進行調試和更進一步的瞭解。

當您建立要搭配 PowerShell 或 API 使用的資料流程腳本時，您必須將格式化的文字折迭成一行。 您可以將定位字元和分行符號保留為 escape 字元。 但必須將文字格式化以符合 JSON 屬性。 底部的 [腳本編輯器] UI 上有一個按鈕，可讓您將腳本格式化為單一行。

![[複製] 按鈕](media/data-flow/copybutton.png "[複製] 按鈕")

## <a name="how-to-add-transforms"></a>如何新增轉換
加入轉換需要三個基本步驟：新增核心轉換資料、將輸入資料流程重新路由，然後將輸出資料流程重設路徑。 在範例中，這會很容易看到。
假設我們從一個簡單的來源接收資料流程程，如下所示：

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

如果我們決定新增「衍生」轉換，首先我們需要建立「核心」轉換文字，它有一個簡單的運算式可新增名為的新大寫資料行 `upperCaseTitle` ：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

然後，我們會採用現有的 DFS 並新增轉換：
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

現在我們會藉由識別要讓新轉換在之後（在此案例中為）的轉換 `source1` ，並將資料流程的名稱複製到新的轉換，來重新路由傳入資料流程：
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

最後，我們會識別我們想要在這個新轉換之後的轉換，並將其輸入資料流程（在此案例中為 `sink1` ）取代為新轉換的輸出資料流程名稱：
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

## <a name="dfs-fundamentals"></a>DFS 基本概念
DFS 是由一系列的已連線轉換組成，包括來源、接收和各種其他專案，可以加入新的資料行、篩選資料、聯結資料，還有更多功能。 腳本通常會以一或多個來源開頭，後面接著多個轉換，並以一或多個接收結束。

來源全都具有相同的基本結構：
```
source(
  source properties
) ~> source_name
```

例如，具有三個數據行（movieId、title、內容內容）的簡單來源會是：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

來源以外的所有轉換都具有相同的基本結構：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，採用資料行（標題）並以大寫版本覆寫它的簡單衍生轉換會如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

而沒有架構的接收則是：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>腳本程式碼片段

腳本程式碼片段是可共用的資料流程腳本程式碼，您可以用來在整個資料流程之間共用。 下面這段影片將討論如何使用腳本片段，並利用資料流程腳本來複製和貼上資料流程圖形背後的部分腳本：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>匯總的摘要統計資料
將匯總轉換新增至名為 "SummaryStats" 的資料流程，然後將下列程式碼貼到腳本中的彙總函式，取代現有的 SummaryStats。 這將提供資料設定檔摘要統計資料的一般模式。

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
您也可以使用下列範例來計算您的資料中，唯一的數目和相異資料列的數目。 下列範例可以貼入具有「匯總」轉換的資料流程，稱為 ValueDistAgg。 這個範例會使用名為 "title" 的資料行。 請務必以您想要用來取得數值計數的資料中的 string 資料行來取代 "title"。

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>將所有資料行包含在匯總中
這是一般匯總模式，示範如何在建立匯總時，保留輸出中繼資料中其餘的資料行。 在此情況下，我們 ```first()``` 會使用函數來選擇名稱不是 "movie" 的每個資料行中的第一個值。 若要使用此功能，請建立名為 DistinctRows 的匯總轉換，然後在您的腳本中，將其貼到現有 DistinctRows 匯總腳本的頂端。

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>建立資料列雜湊指紋 
在資料流程腳本中使用此程式碼來建立名為的新衍生資料行 ```DWhash``` ，以產生 ```sha1``` 三個數據行的雜湊。

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

您也可以使用下列腳本，使用資料流程中出現的所有資料行來產生資料列雜湊，而不需要為每個資料行命名：

```
derive(DWhash = sha1(columns()))
```

### <a name="string_agg-equivalent"></a>String_agg 對等
此程式碼的作用就像 T-sql 函式 ```string_agg()``` ，會將字串值匯總成陣列。 然後，您可以將該陣列轉換成字串，以便搭配 SQL 目的地使用。

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> DerivedColumn2
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>更新次數、更新插入、插入、刪除
當使用 Alter Row 轉換時，您可能會想要計算從您的 Alter Row 原則產生的更新、更新插入、插入、刪除數目。 在您的 alter row 之後加入匯總轉換，並將此資料流程腳本貼入這些計數的匯總定義中。

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>使用所有資料行的相異資料列
此程式碼片段會將新的匯總轉換加入至您的資料流程，此資料流程會接受所有傳入的資料行、產生用於分組以消除重複專案的雜湊，然後提供每個重複的第一次出現做為輸出。 您不需要明確地命名資料行，它們將會從您的傳入資料流程自動產生。

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

## <a name="next-steps"></a>後續步驟

開始使用資料流程[總覽文章](concepts-data-flow-overview.md)來探索資料流程
