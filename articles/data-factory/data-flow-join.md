---
title: 加入映射資料串流加入轉換
description: 使用 Azure 資料工廠映射資料串流的聯接轉換合併來自兩個資料來源的資料
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 0c0e35f7f06afd0cafa4a1e353b7eda84ed226f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413659"
---
# <a name="join-transformation-in-mapping-data-flow"></a>加入映射資料串流加入轉換

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用聯接轉換將來自映射數據流中的兩個源或流的數據合併。 輸出流將包括來自兩個源的所有列,這些列基於聯接條件進行匹配。 

## <a name="join-types"></a>聯結類型

映射數據流當前支援五種不同的聯接類型。

### <a name="inner-join"></a>內部聯結

內部聯接僅輸出兩個表中具有匹配值的行。

### <a name="left-outer"></a>左方外部

左外部聯接返回來自左流的所有行,並從右流返回匹配的記錄。 如果來自左流的行不匹配,則來自右流的輸出列將設置為 NULL。 輸出將是內部聯接返回的行以及來自左流的不匹配的行。

> [!NOTE]
> 數據流使用的 Spark 引擎偶爾會由於聯接條件下的點菜產品而出現故障。 如果發生這種情況,您可以切換到自定義交叉聯接並手動輸入聯接條件。 這可能會導致數據流的性能變慢,因為執行引擎可能需要計算關係兩側的所有行,然後篩選行。

### <a name="right-outer"></a>右方外部

右外部聯接從右流返回所有行,並從左流返回匹配的記錄。 如果右流中的行不匹配,則左流的輸出列將設置為 NULL。 輸出將是內部聯接返回的行加上右流中不匹配的行。

### <a name="full-outer"></a>完整外部

完全外部聯接輸出來自兩側的所有列和行,並且對不匹配的列具有 NULL 值。

### <a name="custom-cross-join"></a>自訂交叉聯結

交叉聯接根據條件輸出兩個流的交叉積。 如果使用的條件不是相等的,請指定自定義表達式作為交叉聯接條件。 輸出流將是滿足聯接條件的所有行。

可以使用此聯接類型進行非聯接和```OR```條件。

如果要顯式生成完整的點菜產品,請使用聯接前兩個獨立流中的每個派生列變換創建要匹配的合成鍵。 例如,每個呼叫```SyntheticKey```的串流中,在「派生列」中建立新欄位,```1```並將其設定為 。 然後用作```a.SyntheticKey == b.SyntheticKey```自定義聯接表達式。

> [!NOTE]
> 請確保在自定義交叉聯接中至少包含一個來自左右關係兩側的列。 使用靜態值而不是每一側的列執行交叉聯接會導致對整個數據集進行完整掃描,從而導致數據流執行不佳。

## <a name="configuration"></a>組態

1. 在**右流**下拉下下拉清單中選擇要加入的數據流。
1. 選擇**您的加入型態**
1. 選擇要匹配的鍵列,以便加入條件。 默認情況下,數據流在每個流中查找一列之間的相等性。 要透過計算值, 請將滑鼠的移動在列下拉清單上, 然後選擇 **「計算」 欄位**。

![加入轉換](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>優化聯結效能

與 SSIS 等工具中的合併聯接不同,聯接轉換不是強制合併聯接操作。 聯接鍵不需要排序。 聯接操作基於 Spark 中的最佳聯接操作(廣播或地圖側聯接)進行。

![聯接轉型優化](media/data-flow/joinoptimize.png "聯接優化")

如果其中一個或兩個數據流適合輔助節點記憶體,則通過在優化選項卡中啟用 **「廣播**」來進一步優化性能。您還可以在聯接操作上重新分區數據,使其更好地適合每個工作人員的記憶體。

## <a name="self-join"></a>自我聯結

要自行加入數據流,請用選擇轉換對現有流進行別名。 通過按一下轉換旁邊的加號圖示並選擇 **「新建分支**」來創建新分支。 將選擇轉換添加到原始流的別名。 新增聯結轉換,並將原始流作為**左流**選擇,選擇轉換作為**右流**。

![自聯接](media/data-flow/selfjoin.png "自聯接")

## <a name="testing-join-conditions"></a>測試聯結條件

在調試模式下使用數據預覽測試聯接轉換時,請使用一小組已知數據。 從大型數據集對行進行採樣時,無法預測將讀取哪些行和鍵進行測試。 結果是非確定性的,這意味著您的聯接條件可能不會返回任何匹配項。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>內部聯結範例

下面的範例是名為「`JoinMatchedData`左流」 和「`TripData``TripFare`右流 」的聯接轉換。  聯接條件是,如果每個流`hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}``hack_license`中的`medallion`、`vendor_id``pickup_datetime`列匹配,則返回 true 的運算式。 `joinType` 為 `'inner'`。 我們只允許在左流廣播,所以`broadcast`有價值`'left'`。

在資料工廠的一個數字的數字, 此轉換類似於下圖:

![加入範例](media/data-flow/join-script1.png "加入範例")

此轉換的資料串流文稿位於下面的代碼段中:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>自訂交叉聯接範例

下面的範例是名為「`JoiningColumns`左流」 和「`LeftStream``RightStream`右流 」的聯接轉換。 此轉換採用兩個流,並將列大於列`leftstreamcolumn``rightstreamcolumn`的所有行合併在一起。 `joinType` 為 `cross`。 未開啟`broadcast`廣播具有`'none'`價值 。

在資料工廠的一個數字的數字, 此轉換類似於下圖:

![加入範例](media/data-flow/join-script2.png "加入範例")

此轉換的資料串流文稿位於下面的代碼段中:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>後續步驟

加入資料後,創建[派生列](data-flow-derived-column.md)並將數據[接收器](data-flow-sink.md)到目標數據存儲。
