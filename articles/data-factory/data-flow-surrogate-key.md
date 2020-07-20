---
title: 對應資料流程中的代理鍵轉換
description: 如何使用 Azure Data Factory 的對應資料流程代理金鑰轉換來產生連續索引鍵值
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606302"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>對應資料流程中的代理鍵轉換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 [代理鍵] 轉換，將遞增的索引鍵值加入至每個資料列。 在星狀架構分析資料模型中設計維度資料表時，這會很有用。 在星狀架構中，維度資料表中的每個成員都需要非業務索引鍵的唯一索引鍵。

## <a name="configuration"></a>組態

![代理鍵轉換](media/data-flow/surrogate.png "代理鍵轉換")

索引**鍵資料行：** 所產生之代理鍵資料行的名稱。

**起始值：** 將產生的最低索引鍵值。

## <a name="increment-keys-from-existing-sources"></a>從現有來源遞增索引鍵

若要從存在於來源的值啟動您的順序，請在您的代理鍵轉換後使用「衍生的資料行」轉換，將兩個值相加：

![SK 新增上限](media/data-flow/sk006.png "代理鍵轉換新增最大值")

### <a name="increment-from-existing-maximum-value"></a>從現有的最大值遞增

若要使用先前的 max 來植入索引鍵值，有兩種技術可供您根據來源資料所在的位置來使用。

#### <a name="database-sources"></a>資料庫來源

使用 [SQL 查詢] 選項，從來源中選取 MAX （）。 例如，`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![代理鍵查詢](media/data-flow/sk002.png "代理金鑰轉換查詢")

#### <a name="file-sources"></a>檔案來源

如果您先前的最大值是在檔案中，請使用「匯總」轉換中的 `max()` 函數來取得先前的最大值：

![代理金鑰檔案](media/data-flow/sk008.png "代理金鑰檔案")

在這兩種情況下，您都必須將傳入的新資料與包含先前最大值的來源聯結在一起。

![代理鍵聯結](media/data-flow/sk004.png "代理鍵聯結")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>範例

![代理鍵轉換](media/data-flow/surrogate.png "代理鍵轉換")

上述代理金鑰設定的資料流程腳本位於下列程式碼片段中。

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>後續步驟

這些範例使用[聯結](data-flow-join.md)和衍生的資料[行](data-flow-derived-column.md)轉換。
