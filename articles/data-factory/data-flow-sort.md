---
title: 對應資料串序轉換
description: Azure 資料工廠映射資料排序轉換
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606329"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>對應資料串序轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

排序轉換允許您對當前資料流上的傳入行進行排序。 您可以選擇單列,然後按升序或降序排序。

> [!NOTE]
> 映射資料流在跨多個節點和分區分佈數據的Spark群集上執行。 如果選擇在後續轉換中重新分區數據,則可能會由於重新洗牌而丟失排序。

## <a name="configuration"></a>組態

![排序設定](media/data-flow/sort.png "Sort")

**不區分大小寫:** 在排序字串或文字欄位時是否希望忽略大小寫

**只在分割區內排序:** 當數據流在火花上運行時,每個數據流被劃分為分區。 此設置僅對傳入分區中的數據進行排序,而不是對整個數據流進行排序。 

**排序條件:** 選擇要按排序的列以及排序順序。 順序確定排序優先順序。 選擇空值是否出現在數據流的開頭或結尾。

### <a name="computed-columns"></a>計算資料行

要在應用排序之前修改或提取列值,請將滑鼠懸停在列上並選擇"計算列"。 這將打開表達式生成器以創建排序操作的運算式,而不是使用列值。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>範例

![排序設定](media/data-flow/sort.png "Sort")

上述排序配置的數據流腳本位於下面的代碼段中。

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>後續步驟

排序後,您可能需要使用[聚合轉換](data-flow-aggregate.md)
