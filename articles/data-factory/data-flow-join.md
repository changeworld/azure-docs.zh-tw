---
title: 在映射資料流程中加入轉換
description: 使用 Azure 資料工廠映射資料流程的聯接轉換合併來自兩個數據源的資料
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 32100e9cad86f12dc8111ee8a0282a515540a4db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346618"
---
# <a name="join-transformation-in-mapping-data-flow"></a>在映射資料流程中加入轉換

使用聯接轉換將來自映射資料流程中的兩個源或流的資料合併。 輸出流將包括來自兩個源的所有列，這些列基於聯結條件進行匹配。 

## <a name="join-types"></a>聯結類型

映射資料流程當前支援五種不同的聯接類型。

### <a name="inner-join"></a>內部聯結

內部聯接僅輸出兩個表中具有匹配值的行。

### <a name="left-outer"></a>左方外部

左外部聯結返回來自左流的所有行，並從右流返回匹配的記錄。 如果來自左流的行不匹配，則來自右流的輸出列將設置為 Null。 輸出將是內部聯接返回的行以及來自左流的不匹配的行。

> [!NOTE]
> 資料流程使用的 Spark 引擎偶爾會由於聯結條件下的點菜產品而出現故障。 如果發生這種情況，您可以切換到自訂交叉聯接並手動輸入聯結條件。 這可能會導致資料流程的性能變慢，因為執行引擎可能需要計算關係兩側的所有行，然後篩選行。

### <a name="right-outer"></a>右方外部

右外部聯結從右流返回所有行，並從左流返回匹配的記錄。 如果右流中的行不匹配，則左流的輸出列將設置為 Null。 輸出將是內部聯接返回的行加上右流中不匹配的行。

### <a name="full-outer"></a>完整外部

完整外部聯結輸出來自兩側的所有列和行，並且對不匹配的列具有 Null 值。

### <a name="custom-cross-join"></a>自訂交叉聯接

交叉聯接根據條件輸出兩個流的交叉積。 如果使用的條件不是相等的，請指定自訂表格達式作為交叉聯結條件。 輸出流將是滿足聯結條件的所有行。

可以使用此聯接類型進行非聯接和```OR```條件。

如果要顯式生成完整的點菜產品，請使用聯接前兩個獨立流中的每個派生列變換創建要匹配的合成鍵。 例如，在每個調用```SyntheticKey```的流中，在"派生列"中創建一個新列，```1```並將其設置為 。 然後用作```a.SyntheticKey == b.SyntheticKey```自訂聯接運算式。

> [!NOTE]
> 請確保在自訂交叉聯接中至少包含一個來自左右關係兩側的列。 使用靜態值而不是每一側的列執行交叉聯接會導致對整個資料集進行完整掃描，從而導致資料流程執行不佳。

## <a name="configuration"></a>組態

1. 在**右流**下拉下下拉清單中選擇要加入的資料流程。
1. 選擇**您的加入類型**
1. 選擇要匹配的鍵列，以便加入條件。 預設情況下，資料流程在每個流中查找一列之間的相等性。 要通過計算值進行比較，請將滑鼠懸停在列下拉清單上，然後選擇 **"計算"列**。

![加入轉換](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>優化聯接性能

與 SSIS 等工具中的合併聯接不同，聯接轉換不是強制合併聯接操作。 聯接鍵不需要排序。 聯接操作基於 Spark 中的最佳聯接操作（廣播或地圖側聯接）進行。

![聯接轉型優化](media/data-flow/joinoptimize.png "聯接優化")

如果其中一個或兩個數據流適合輔助節點記憶體，則通過在優化選項卡中啟用 **"廣播**"來進一步優化性能。您還可以在聯接操作上重新分區資料，使其更好地適合每個工作人員的記憶體。

## <a name="self-join"></a>自我聯結

要自行加入資料流程，請用選擇轉換對現有流進行別名。 通過按一下轉換旁邊的加號圖示並選擇 **"新建分支**"來創建新分支。 將選擇轉換添加到原始流的別名。 添加聯接轉換，並將原始流作為**左流**選擇，選擇轉換作為**右流**。

![自聯接](media/data-flow/selfjoin.png "自聯接")

## <a name="testing-join-conditions"></a>測試聯結條件

在偵錯模式下使用資料預覽測試聯接轉換時，請使用一小組已知資料。 從大型資料集對行進行採樣時，無法預測將讀取哪些行和鍵進行測試。 結果是非確定性的，這意味著您的聯結條件可能不會返回任何匹配項。

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

### <a name="inner-join-example"></a>內部聯接示例

下面的示例是名為"`JoinMatchedData`左流"和"右流`TripData``TripFare`"的聯接轉換。  聯結條件是，如果每個流`hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`中的`hack_license`、`medallion`和`vendor_id``pickup_datetime`列匹配，則返回 true 的運算式。 `joinType` 為 `'inner'`。 我們只允許在左流廣播，所以`broadcast`有價值。 `'left'`

在資料工廠 UX 中，此轉換類似于下圖：

![加入示例](media/data-flow/join-script1.png "加入示例")

此轉換的資料流程腳本位於下面的程式碼片段中：

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

### <a name="custom-cross-join-example"></a>自訂交叉聯接示例

下面的示例是名為"`JoiningColumns`左流"和"右流`LeftStream``RightStream`"的聯接轉換。 此轉換採用兩個流，並將列大於列`leftstreamcolumn``rightstreamcolumn`的所有行合併在一起。 `joinType` 為 `cross`。 未啟用`broadcast`廣播具有價值`'none'`。

在資料工廠 UX 中，此轉換類似于下圖：

![加入示例](media/data-flow/join-script2.png "加入示例")

此轉換的資料流程腳本位於下面的程式碼片段中：

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>後續步驟

加入資料後，創建[派生列](data-flow-derived-column.md)並將資料[接收器](data-flow-sink.md)到目標資料存儲。
