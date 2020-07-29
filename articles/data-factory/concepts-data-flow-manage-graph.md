---
title: 資料流程圖形
description: 如何使用 data factory 的資料流程圖形
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81605364"
---
# <a name="mapping-data-flow-graphs"></a>對應資料流程圖形

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

對應資料流程設計介面是一種「建築」介面，由上而下由左至右建立資料流程。 每個轉換皆附有工具箱 (加號 (+) 符號)。 專注在商務邏輯，而非透過自由格式 DAG 環境中的邊緣連接節點。

以下是用來管理資料流程圖形的內建機制。

## <a name="move-nodes"></a>移動節點

![匯總轉換選項](media/data-flow/agghead.png "匯總工具標頭")

如果沒有拖放架構，「移動」轉換節點的方式是變更傳入的資料流程。 您將改為變更「傳入資料流」來四處移動轉換。

## <a name="streams-of-data-inside-of-data-flow"></a>資料流程內的資料流程

在 Azure Data Factory 資料流程中，資料流代表資料的流程。 在 [轉換設定] 窗格中，您會看到 [傳入資料流程] 欄位。 這會告訴您提供轉換的是哪一個資料流。 您可以按一下傳入資料流名稱，然後選擇另一個資料流，變更圖表上轉換節點的實體位置。 目前的轉換以及該資料流上的所有後續轉換，將會接著移至新位置。

如果您在之後要移動具有一或多個轉換的轉換，則資料流程中的新位置會透過新的分支聯結。

如果您在選擇節點之後沒有後續的轉換，則只有該轉換會移至新的位置。

## <a name="hide-graph-and-show-graph"></a>隱藏圖表並顯示圖形

底部設定窗格的最右側有一個按鈕，可讓您在處理轉換設定時，將底部窗格展開為全螢幕。 這可讓您使用 [上一個] 和 [下一步] 按鈕來流覽圖形的設定。 若要移回圖表視圖，請按一下向下按鈕並返回 [分割] 畫面。

## <a name="search-graph"></a>搜尋圖表

您可以使用設計介面上的 [搜尋] 按鈕來搜尋圖表。

![搜尋](media/data-flow/search001.png "搜尋圖表")

## <a name="next-steps"></a>後續步驟

完成資料流程設計之後，請開啟 [偵錯工具] 按鈕，然後直接在 [[資料流程設計師](concepts-data-flow-debug-mode.md)] 或 [[管線] debug](control-flow-execute-data-flow-activity.md)中以 [debug] 模式測試它。
