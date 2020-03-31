---
title: 使用視頻索引子進行即時流分析
titleSuffix: Azure Media Services
description: 本文演示如何使用視頻索引子執行即時流分析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186004"
---
# <a name="live-stream-analysis-with-video-indexer"></a>使用視頻索引子進行即時流分析

Azure 媒體服務視頻索引子是一種 Azure 服務，旨在從視頻和音訊檔離線獲取深入見解。 這是用於分析已提前創建的給定媒體檔案。 但是，對於某些用例，儘快從即時源獲取媒體見解以及時解鎖操作和其他用例非常重要。 例如，內容製作者可以使用即時流上的此類豐富中繼資料來自動執行電視製作。

本文中描述的解決方案允許客戶在即時源上以近乎即時的解析度使用視頻索引子。 使用此解決方案，索引的延遲可能低至 4 分鐘，具體取決於要編制索引的資料塊、輸入解析度、內容類型以及用於此過程的計算電源。

![即時流上的視頻索引子中繼資料](./media/live-stream-analysis/live-stream-analysis01.png)

*圖 1 = 在即時流上顯示視頻索引子中繼資料的示例播放機*

手頭的[流分析解決方案](https://aka.ms/livestreamanalysis)使用 Azure 函數和兩個邏輯應用使用視頻索引子在 Azure 媒體服務的即時通道中處理即時程式，並使用 Azure 媒體播放機顯示近乎即時結果流的結果。

在高級別上，它由兩個主要步驟組成。 第一步每 60 秒運行一次，並採用播放的最後 60 秒的子剪輯，從中創建資產並通過視頻索引子進行索引。 然後，在索引完成後調用第二步。 捕獲的見解將處理，發送到 Azure Cosmos DB，並刪除已索引的子剪輯。

示例播放機使用專用 Azure 函數播放即時流並從 Azure Cosmos DB 獲取見解。 它顯示中繼資料和縮略圖與即時視頻同步。

![兩個邏輯應用程式在雲中每分鐘處理即時流](./media/live-stream-analysis/live-stream-analysis02.png)

*圖 2 = 在雲中每分鐘處理即時流的兩個邏輯應用。*

## <a name="step-by-step-guide"></a>分步指南 

完整的代碼和部署結果的分步指南可以在[GitHub 專案中找到，用於使用視頻索引子進行即時媒體分析](https://aka.ms/livestreamanalysis)。 

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
