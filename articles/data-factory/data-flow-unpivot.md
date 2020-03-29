---
title: 映射資料流程取消軸變換
description: Azure 資料工廠映射資料流程取消透視轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930144"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure 資料工廠取消透視轉換



在 ADF 映射資料流程中使用 Unpivot，通過將單個記錄中的多個列的值擴展到單個列中具有相同值的多個記錄，將未正常化資料集轉換為更正常化的版本。

![取消支點變換](media/data-flow/unpivot1.png "取消透視選項 1")

## <a name="ungroup-by"></a>取消分組依據

![取消支點變換](media/data-flow/unpivot5.png "取消透視選項 2")

首先，設定您想要對於樞紐彙總分組所依據的資料行。 使用資料行清單旁邊的 [+] 號，設定要取消群組的一或多個資料行。

## <a name="unpivot-key"></a>取消樞紐索引鍵

![取消支點變換](media/data-flow/unpivot6.png "取消透視選項 3")

樞紐索引鍵是 ADF 從資料列樞紐處理為資料行的資料行。 根據預設，此欄位的資料集之內的每個唯一值都會樞紐處理為資料行。 不過，您可以選擇從資料集輸入您想要樞紐處理為資料行值的數值。

## <a name="unpivoted-columns"></a>取消資料行樞紐

![取消支點變換](media/data-flow//unpivot7.png "取消透視選項 4")

最後，選擇您想要對已樞紐的值所使用的彙總，以及您要如何在轉換的新輸出投影中顯示資料行。

(選擇性) 您可以設定對於每個新資料行名稱從資料列值新增前置詞、中置詞及後置詞的命名模式。

比方說，根據 "Region" 對 "Sales" 進行樞紐處理，就會得到來自每個銷售值的新資料行值。 例如："25"，"50"，"1000"，...但是，如果設置了首碼值"銷售"，則"銷售"將預固定到這些值。

<img src="media/data-flow/unpivot3.png" width="400">

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

![取消支點變換](media/data-flow//unpivot7.png "取消透視選項 5")

最終之取消樞紐的資料結果集所顯示的資料行總計，現已取消樞紐為個別的資料列值。

## <a name="next-steps"></a>後續步驟

使用[資料透視轉換](data-flow-pivot.md)將行透視到列。
