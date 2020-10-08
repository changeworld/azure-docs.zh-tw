---
title: 對應資料流程中的視窗轉換
description: Azure Data Factory 對應的資料流程視窗轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 0231fc8919444558abcbc965ad127f7372eceb66
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823600"
---
# <a name="window-transformation-in-mapping-data-flow"></a>對應資料流程中的視窗轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

視窗轉換可供您定義資料流資料行的視窗型彙總。 在運算式產生器中，您可以定義以資料或時間視窗 (SQL OVER 子句) 為基礎的各種彙總，例如 LEAD、LAG、NTILE、CUMEDIST、RANK 等等。 包含這些彙總的輸出中，將產生新的欄位。 您也可以包含選擇性的群組依據欄位。

![視窗選項](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>超過
對於您的視窗轉換設定資料行資料的資料分割。 SQL 對等項目是 SQL 之中 Over 子句的 ```Partition By```。 如果您想要建立計算，或建立要對於資料分割使用的運算式，可以將滑鼠停留在資料行名稱上，並選取「計算資料行」。

![視窗選項](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Sort
Over 子句的另一個部分是設定 ```Order By```。 這會設定資料排序順序。 您也可以在此資料行欄位中建立排序用的計算值運算式。

![視窗選項](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>範圍依據
接下來，將視窗框架設定為「未繫結」或「已繫結」。 若要設定未繫結的視窗框架，可在兩端將滑桿設定為「未繫結」。 如果您選擇「未繫結」與「目前資料列」之間的設定，則您必須設定位移的開始及結束值。 這兩個值會是正整數。 您可以使用資料中的相對數字或值。

視窗滑桿有兩個值須設定：目前資料列之前的值，以及目前資料列之後的值。 開始和結束位移符合滑桿上的兩個選取器。

![視窗選項](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>視窗資料行
最後，使用運算式產生器定義您想要在資料視窗上使用的彙總，例如 RANK、COUNT、MIN、MAX、DENSE RANK、LEAD、LAG 等等。

![視窗選項](media/data-flow/windows7.png "windows 7")

彙總及分析的函式 (可供您在 ADF 資料流程運算式語言透過運算式產生器中使用) 完整清單如下：https://aka.ms/dataflowexpressions。

## <a name="next-steps"></a>下一步

如果您要尋找簡單的分組依據匯總，請使用「 [匯總」轉換](data-flow-aggregate.md)
