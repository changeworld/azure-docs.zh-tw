---
title: 存在對應資料流程中的轉換
description: 使用 Azure Data Factory 對應資料流程中的「存在」轉換來檢查現有的資料列
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82982627"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>存在對應資料流程中的轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

「存在」轉換是一種資料列篩選轉換，可檢查您的資料是否存在於另一個來源或資料流程中。 輸出資料流程包含左邊資料流程中的所有資料列，這些資料列存在於右邊的資料流程中或不存在於右邊的資料流程中。 「存在」轉換類似于 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS``` 。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>組態

1. 選擇您要檢查的資料流程是否存在於右邊的 **資料流程** 下拉式清單中。
1. 指定您要尋找的資料是否存在，或存在於 **存在型** 別設定中。
1. 選取您是否想要 **自訂表格達式**。
1. 選擇您想要比較的索引鍵資料行，做為您的現有條件。 根據預設，資料流程會在每個資料流中的資料行之間尋找等式。 若要透過計算值進行比較，請將滑鼠停留在資料行下拉式清單上，然後選取 [計算資料行]。

![存在的設定](media/data-flow/exists.png "存在1")

### <a name="multiple-exists-conditions"></a>有多個存在條件

若要比較每個資料流程中的多個資料行，請按一下現有資料列旁的加號圖示，以加入新的存在條件。 每個額外的條件都會以 "and" 語句聯結。 比較兩個數據行與下列運算式相同：

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>自訂表格達式

若要建立包含「和」和「等於」以外之運算子的自由格式運算式，請選取 [ **自訂表格達式** ] 欄位。 透過 [資料流程運算式產生器] 輸入自訂表格達式，方法是按一下藍色方塊。

![存在自訂設定](media/data-flow/exists1.png "存在自訂")

## <a name="broadcast-optimization"></a>廣播最佳化

![廣播聯結](media/data-flow/broadcast.png "廣播聯結")

在聯結、查閱和存在轉換中，如果其中一個或兩個資料流納入背景工作角色節點記憶體中，您可以藉由啟用 [廣播] 來最佳化效能。 根據預設，Spark 引擎會自動決定是否要廣播一邊。 若要手動選擇廣播哪一邊，請選取 [固定]。

除非您的聯結遇到逾時錯誤，否則不建議透過 [關閉] 選項停用廣播。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>範例

下列範例是名為的 exists 轉換 `checkForChanges` ，它會取得左邊的資料流程 `NameNorm2` 和右邊的資料流程 `TypeConversions` 。  `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`如果 `EMPID` 每個資料流程中的和資料行都相符，則存在條件就是傳回 true 的運算式 `Region` 。 由於我們正在檢查是否存在，因此為 `negate` false。 我們不會在 [優化] 索引標籤中啟用任何廣播，因此 `broadcast` 具有值 `'none'` 。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![Exists 範例](media/data-flow/exists-script.png "Exists 範例")

此轉換的資料流指令碼位於下列程式碼片段中：

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>後續步驟

類似的轉換是 [查閱](data-flow-lookup.md) 和 [聯結](data-flow-join.md)。
