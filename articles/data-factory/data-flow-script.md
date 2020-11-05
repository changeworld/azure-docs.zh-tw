---
title: 對應資料流程腳本
description: Data Factory 的資料流程腳本程式碼後端語言總覽
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 69cc835b37d2405e15638d85309dc89d51c6d043
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360270"
---
# <a name="data-flow-script-dfs"></a> (DFS) 的資料流程腳本

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

資料流程腳本 (DFS) 是與編碼語言類似的基礎中繼資料，可用來執行對應資料流程中包含的轉換。 每個轉換都是由一系列屬性所表示，這些屬性提供必要的資訊來正確執行作業。 您可以按一下瀏覽器 UI 頂端功能區上的 [腳本] 按鈕，以查看並從 ADF 編輯腳本。

![腳本按鈕](media/data-flow/scriptbutton.png "腳本按鈕")

例如， `allowSchemaDrift: true,` 在來源轉換中，會告知服務將源資料集中的所有資料行包含在資料流程中，即使這些資料行未包含在架構投射中也一樣。

## <a name="use-cases"></a>使用案例
使用者介面會自動產生 DFS。 您可以按一下 [腳本] 按鈕來查看和自訂腳本。 您也可以在 ADF UI 外部產生腳本，然後將其傳遞至 PowerShell Cmdlet。 在偵測複雜的資料流程時，您可能會發現掃描腳本的程式碼後端更容易，而不是掃描您流程的 UI 圖形表示。

以下是一些範例使用案例：
- 以程式設計方式產生相當類似的許多資料流程，也就是「標記輸出」的資料流程。
- 在 UI 中難以管理或導致驗證問題的複雜運算式。
- 對執行期間所傳回的各種錯誤進行調試和更深入的瞭解。

當您建立要搭配 PowerShell 或 API 使用的資料流程腳本時，您必須將格式化的文字折迭成單一行。 您可以將定位字元和分行符號保留為 escape 字元。 但是，必須將文字格式化以納入 JSON 屬性中。 底部的腳本編輯器 UI 上有一個按鈕，可將腳本格式化為單一行。

![[複製] 按鈕](media/data-flow/copybutton.png "[複製] 按鈕")

## <a name="how-to-add-transforms"></a>如何新增轉換
新增轉換需要三個基本步驟：新增核心轉換資料、將輸入資料流程重設路徑，然後再將輸出資料流程重設成路由。 在範例中，這可能是最簡單的。
假設我們開始使用簡單的來源來接收資料流程，如下所示：

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

如果我們決定要新增衍生轉換，首先我們需要建立核心轉換文字，此文字具有簡單的運算式，可新增名為的新大寫資料行 `upperCaseTitle` ：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

接著，我們會採用現有的 DFS 並新增轉換：
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

現在) ，我們會在此情況下找出要讓新轉換成為 (的轉換，然後將 `source1` 資料流程的名稱複製到新的轉換，藉此重新路由傳入的資料流程：
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

最後，我們會找出我們想要在這個新的轉換之後採用的轉換，並將其輸入資料流程取代 (在此案例中， `sink1`) 具有新轉換的輸出資料流程名稱：
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

## <a name="dfs-fundamentals"></a>DFS 基礎
DFS 是由一系列的連線轉換所組成，包括來源、接收，以及各種可新增資料行、篩選資料、聯結資料等等的其他專案。 腳本的開頭通常是一或多個來源，後面接著許多轉換，並以一或多個接收結束。

來源全都具有相同的基本結構：
```
source(
  source properties
) ~> source_name
```

比方說，有三個數據行的簡單來源 (movieId、title、內容) 如下：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

來源以外的所有轉換都有相同的基本結構：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，將資料行 (標題的簡單衍生轉換) 並以大寫版本覆寫，如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

而且沒有架構的接收就是：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>腳本程式碼片段

腳本程式碼片段是可共用的資料流程腳本程式碼，可讓您用來跨資料流程進行共用。 以下影片說明如何使用腳本程式碼片段，以及如何利用資料流程腳本，將部分腳本複製並貼到您的資料流程圖形後方：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>匯總的摘要統計資料
將「匯總」轉換加入至名為「SummaryStats」的資料流程，然後針對腳本中的彙總函式，貼上下列程式碼，以取代現有的 SummaryStats。 這會提供資料設定檔摘要統計資料的一般模式。

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
您也可以使用下列範例來計算資料中相異資料列的唯一數目和數目。 您可以使用稱為 ValueDistAgg 的「匯總」轉換，將下列範例貼入資料流程中。 此範例會使用名為 "title" 的資料行。 請務必將 "title" 取代為您想要用來取得值計數的資料中的字串資料行。

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>在匯總中包含所有資料行
這是一般匯總模式，會示範如何在建立匯總時，保留輸出中繼資料中其餘的資料行。 在此情況下，我們 ```first()``` 會使用函數來選擇名稱不是 "movie" 的每個資料行中的第一個值。 若要使用此功能，請建立名為 DistinctRows 的匯總轉換，然後在您的腳本中貼上現有 DistinctRows 匯總腳本的上方。

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>建立資料列雜湊指紋 
在您的資料流程腳本中使用此程式碼，建立名為的新衍生資料行，此資料行會 ```DWhash``` 產生 ```sha1``` 三個數據行的雜湊。

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

您也可以使用下列腳本，利用資料流程中出現的所有資料行來產生資料列雜湊，而不需要為每個資料行命名：

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg 相等
此程式碼的作用如同 T-sql 函式 ```string_agg()``` ，並且會將字串值匯總成陣列。 然後，您可以將該陣列轉換成要搭配 SQL 目的地使用的字串。

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>更新次數、upsert、插入、刪除
使用 Alter Row 轉換時，您可能會想要計算您的 Alter Row 原則所產生的更新、upsert、插入、刪除數目。 將「匯總」轉換加入至 alter row 之後，然後將此資料流程腳本貼到這些計數的匯總定義中。

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>使用所有資料行的相異資料列
此程式碼片段會將新的匯總轉換加入至您的資料流程，以取得所有傳入的資料行、產生用來分組以消除重複專案的雜湊，然後提供每個重複專案的第一次出現作為輸出。 您不需要明確地命名資料行，就會自動從您的內送資料流程產生它們。

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>檢查所有資料行中的 Null
這是您可以貼到資料流程中的程式碼片段，以一般檢查所有資料行的 Null 值。 這項技術會利用架構漂移來查看所有資料列中的所有資料行，並使用條件式分割，將資料列的資料列與沒有 Null 的資料列區隔開。 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>使用 select 自動對應架構漂移
當您需要從一組未知或動態的傳入資料行載入現有的資料庫架構時，您必須在接收轉換中對應右邊的資料行。 只有當您載入現有的資料表時，才需要此項。 將此程式碼片段新增至您的接收，以建立自動對應資料行的選取。 讓您的接收對應保持自動對應。

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

## <a name="next-steps"></a>後續步驟

從[資料流程的總覽文章](concepts-data-flow-overview.md)開始探索資料流程
