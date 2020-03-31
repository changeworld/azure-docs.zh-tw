---
title: 資料流程圖
description: 如何使用資料工廠資料流程圖
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: da180bfb1aec29fa15b070fd73ba84d708ada927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928314"
---
# <a name="mapping-data-flow-graphs"></a>映射資料流程圖

映射資料流程設計介面是一個"構造"曲面，您可以在其中自上而下、從左至右生成資料流程。 每個轉換皆附有工具箱 (加號 (+) 符號)。 專注在商務邏輯，而非透過自由格式 DAG 環境中的邊緣連接節點。

下面是管理資料流程圖的內置機制。

## <a name="move-nodes"></a>移動節點

![聚合轉換選項](media/data-flow/agghead.png "聚合器標頭")

如果沒有拖放範例，"移動"轉換節點的方法就是更改傳入流。 您將改為變更「傳入資料流」來四處移動轉換。

## <a name="streams-of-data-inside-of-data-flow"></a>資料流程內部的資料流程

在 Azure Data Factory 資料流程中，資料流代表資料的流程。 在轉換設置窗格中，您將看到一個"傳入流"欄位。 這會告訴您提供轉換的是哪一個資料流。 您可以按一下傳入資料流名稱，然後選擇另一個資料流，變更圖表上轉換節點的實體位置。 目前的轉換以及該資料流上的所有後續轉換，將會接著移至新位置。

如果您在之後要移動具有一或多個轉換的轉換，則資料流程中的新位置會透過新的分支聯結。

如果您在選擇節點之後沒有後續的轉換，則只有該轉換會移至新的位置。

## <a name="hide-graph-and-show-graph"></a>隱藏圖形並顯示圖形

底部配置窗格的右側有一個按鈕，您可以在處理轉換配置時將底部窗格擴展到全屏。 這將允許您使用"上一個"和"下一個"按鈕流覽圖形的配置。 要移回圖形視圖，請按一下向下按鈕並返回到拆分螢幕。

## <a name="search-graph"></a>搜索圖

您可以使用設計介面上的搜尋按鈕搜索圖形。

![搜尋](media/data-flow/search001.png "搜索圖")

## <a name="next-steps"></a>後續步驟

完成資料流程設計後，打開調試按鈕，直接在[資料流程設計器](concepts-data-flow-debug-mode.md)或[管道調試](control-flow-execute-data-flow-activity.md)中以偵錯模式進行測試。
