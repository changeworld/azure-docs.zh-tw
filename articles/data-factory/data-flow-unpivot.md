---
title: 對應資料流程 Unpivot 轉換
description: Azure Data Factory 對應的資料流程 Unpivot 轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: c3e769334beb6a5739eebb8d7e8dc370533c2dc6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606252"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory Unpivot 轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 ADF 對應資料流程中的 Unpivot 做為將未正規化的資料集轉換成更正規化版本的方法，方法是將單一記錄中多個資料行的值擴充為單一資料行中具有相同值的多筆記錄。

![取消樞紐轉換](media/data-flow/unpivot1.png "Unpivot 選項1")

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

比方說，根據 "Region" 對 "Sales" 進行樞紐處理，就會得到來自每個銷售值的新資料行值。 例如： "25"、"50"、"1000"、.。。不過，如果您設定「銷售」的前置詞值，則「銷售」會在值前面加上。

<img src="media/data-flow/unpivot3.png" width="400">

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

![取消樞紐轉換](media/data-flow//unpivot7.png "Unpivot 選項5")

最終之取消樞紐的資料結果集所顯示的資料行總計，現已取消樞紐為個別的資料列值。

## <a name="next-steps"></a>後續步驟

使用 [[樞紐分析表] 轉換](data-flow-pivot.md)來將資料列資料行。
