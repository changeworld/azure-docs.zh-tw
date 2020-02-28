---
title: 對應資料流程壓平合併轉換
description: Azure data factory 對應資料流程壓平合併轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 435d2469be8eb572caa02e381d84ae4e9ac32f4b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674840"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure Data Factory 簡維轉換

簡維轉換可以用來將階層式結構內的陣列值資料透視到新的資料列，基本上是反正規化您的資料。

![轉換工具箱](media/data-flow/flatten5.png "轉換工具箱")

![壓平合併轉換1](media/data-flow/flatten7.png "壓平合併轉換1")

## <a name="unroll-by"></a>展開者

首先，選擇您想要展開和 pivot 的 [陣列] 資料行。

![壓平合併轉換設定](media/data-flow/flatten1.png "壓平合併轉換設定")

## <a name="unroll-root"></a>展開根目錄

根據預設，ADF 會在您于上方所選的展開陣列上壓平合併結構。 或者，您可以選擇階層的不同部分來展開至。

## <a name="input-columns"></a>輸入資料行

最後，根據傳入欄位以及您所 unrolled 的正規化資料行，選擇新結構的投影。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>範例

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>後續步驟

* 使用 [[樞紐分析表] 轉換](data-flow-pivot.md)來將資料列資料行。
* 使用「 [Unpivot」轉換](data-flow-unpivot.md)，將資料行樞紐分析表。
