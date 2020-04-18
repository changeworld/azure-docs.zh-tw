---
title: 對應資料串流的代理金鑰轉換
description: 如何使用 Azure 資料工廠的映射資料串流代理金鑰轉換產生順序鍵值
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606302"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>對應資料串流的代理金鑰轉換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用代理項金鑰轉換向每行資料添加遞增鍵值。 這在星形架構分析數據模型中設計維度表時非常有用。 在星形架構中,維度表中的每個成員都需要一個非業務密鑰的唯一密鑰。

## <a name="configuration"></a>組態

![代理金鑰轉換](media/data-flow/surrogate.png "代理金鑰轉換")

**鍵列:** 生成的代理項鍵列的名稱。

**開始值:** 將生成的最低鍵值。

## <a name="increment-keys-from-existing-sources"></a>從現有來源的增量鍵

要從來源中存在的值啟動序列,請使用代理金鑰轉換之後的衍生的轉換將兩個值一起新增:

![SK 新增最大值](media/data-flow/sk006.png "代理金鑰轉換新增最大值")

### <a name="increment-from-existing-maximum-value"></a>從現有最大值遞增

要用前面的最大值設置鍵值的種子,可以使用兩種技術,具體取決於源數據的位置。

#### <a name="database-sources"></a>資料庫來源

使用 SQL 查詢選項從源中選擇 MAX()。 例如,`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![代理金鑰查詢](media/data-flow/sk002.png "代理金鑰轉換查詢")

#### <a name="file-sources"></a>檔案來源

如果以前的最大值位於檔案中,請使用集合的音數`max()`來取得以前的最大值:

![代理金鑰檔案](media/data-flow/sk008.png "代理金鑰檔案")

在這兩種情況下,都必須將傳入的新數據與包含前一個最大值的源聯接在一起。

![代理金鑰聯結](media/data-flow/sk004.png "代理金鑰聯結")

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

![代理金鑰轉換](media/data-flow/surrogate.png "代理金鑰轉換")

上述代理項金鑰配置的數據流腳本位於下面的代碼段中。

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>後續步驟

這些示例使用[聯接](data-flow-join.md)和[派生列](data-flow-derived-column.md)轉換。
