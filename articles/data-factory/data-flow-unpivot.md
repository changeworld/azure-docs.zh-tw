---
title: 對應資料流程中的 Unpivot 轉換
description: Azure Data Factory 對應的資料流程 Unpivot 轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040195"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>對應資料流程中的 Unpivot 轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 ADF 對應資料流程中使用 Unpivot，藉由將單一記錄中多個資料行的值展開成單一資料行中具有相同值的多筆記錄，將未正規化的資料集轉換成更正規化的版本。

![螢幕擷取畫面：顯示從功能表選取的 Unpivot。](media/data-flow/unpivot1.png "Unpivot 選項1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>取消分組依據

![螢幕擷取畫面顯示已選取 [取消群組] 索引標籤的 Unpivot 設定。](media/data-flow/unpivot5.png "Unpivot 選項2")

首先，設定您想要針對 unpivot 匯總進行取消群組的資料行。 使用資料行清單旁邊的 [+] 號，設定要取消群組的一或多個資料行。

## <a name="unpivot-key"></a>取消樞紐索引鍵

![螢幕擷取畫面顯示已選取 [Unpivot 索引鍵] 索引標籤的 [Unpivot] 設定。](media/data-flow/unpivot6.png "Unpivot 選項3")

Unpivot 索引鍵是 ADF 將從資料行切換到資料列的資料行。 依預設，此欄位之資料集中的每個唯一值都將會切換到資料列。 不過，您可以選擇性地輸入您想要將樞紐分析表轉換成資料列值的資料集值。

## <a name="unpivoted-columns"></a>取消資料行樞紐

![螢幕擷取畫面顯示已選取 [資料預覽] 索引標籤的 Unpivot 設定。](media/data-flow//unpivot7.png "Unpivot 選項4")

最後，選擇資料行名稱，以儲存轉換成資料列的取消樞紐資料行值。

 (選擇性) 您可以卸載具有 Null 值的資料列。

例如，SumCost 是在上述共用範例中選擇的資料行名稱。

![顯示使用水果資料行作為 unipivot 索引鍵的 unipivot 轉換之前和之後的 PO、供應商和水果資料行的影像。](media/data-flow/unpivot3.png)

將資料行相片順序設定為 [正常]，會將所有新的取消樞紐資料行從單一值群組在一起。 將資料行相片順序設定為「橫向」會將從現有資料行產生的新取消樞紐資料行群組在一起。

![螢幕擷取畫面：顯示轉換的結果。](media/data-flow//unpivot7.png "Unpivot 選項5")

最終之取消樞紐的資料結果集所顯示的資料行總計，現已取消樞紐為個別的資料列值。

## <a name="next-steps"></a>後續步驟

使用 [pivot 轉換](data-flow-pivot.md) 將資料列樞紐分析表資料行。
