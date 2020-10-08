---
title: 對應資料流程中的 Unpivot 轉換
description: Azure Data Factory 對應的資料流程 Unpivot 轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 38986c3f93856981e903ae93ed7788ae01fc6d5b
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823587"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>對應資料流程中的 Unpivot 轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 ADF 對應資料流程中使用 Unpivot，藉由將單一記錄中多個資料行的值展開成單一資料行中具有相同值的多筆記錄，將未正規化的資料集轉換成更正規化的版本。

![取消樞紐轉換](media/data-flow/unpivot1.png "Unpivot 選項1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>取消分組依據

![取消樞紐轉換](media/data-flow/unpivot5.png "Unpivot 選項2")

首先，設定您想要對於樞紐彙總分組所依據的資料行。 使用資料行清單旁邊的 [+] 號，設定要取消群組的一或多個資料行。

## <a name="unpivot-key"></a>取消樞紐索引鍵

![取消樞紐轉換](media/data-flow/unpivot6.png "Unpivot 選項3")

樞紐索引鍵是 ADF 從資料列樞紐處理為資料行的資料行。 根據預設，此欄位的資料集之內的每個唯一值都會樞紐處理為資料行。 不過，您可以選擇從資料集輸入您想要樞紐處理為資料行值的數值。

## <a name="unpivoted-columns"></a>取消資料行樞紐

![取消樞紐轉換](media/data-flow//unpivot7.png "Unpivot 選項4")

最後，選擇您想要對已樞紐的值所使用的彙總，以及您要如何在轉換的新輸出投影中顯示資料行。

(選擇性) 您可以設定對於每個新資料行名稱從資料列值新增前置詞、中置詞及後置詞的命名模式。

比方說，根據 "Region" 對 "Sales" 進行樞紐處理，就會得到來自每個銷售值的新資料行值。 例如： "25"、"50"、"1000"、.。。但是，如果您設定 "Sales" 的前置詞值，則 "Sales" 將會加上值的前置詞。

![顯示使用水果資料行作為 unipivot 索引鍵的 unipivot 轉換之前和之後的 PO、供應商和水果資料行的影像。](media/data-flow/unpivot3.png)

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

![取消樞紐轉換](media/data-flow//unpivot7.png "Unpivot 選項5")

最終之取消樞紐的資料結果集所顯示的資料行總計，現已取消樞紐為個別的資料列值。

## <a name="next-steps"></a>下一步

使用 [pivot 轉換](data-flow-pivot.md) 將資料列樞紐分析表資料行。
