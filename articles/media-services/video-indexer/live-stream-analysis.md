---
title: 使用影片索引子進行即時串流分析
titleSuffix: Azure Media Services
description: 本文說明如何使用影片索引子來執行即時串流分析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74186004"
---
# <a name="live-stream-analysis-with-video-indexer"></a>使用影片索引子進行即時串流分析

Azure 媒體服務影片索引子是一種 Azure 服務，專門用來從影片和音訊檔案離線提取深入解析。 這是為了分析已事先建立的指定媒體檔案。 不過，在某些使用案例中，請務必快速地從即時摘要取得媒體深入解析，以將作業和其他使用案例按時間釋放。 例如，內容產生者可以使用即時資料流上的這類豐富中繼資料來自動化電視生產。

這篇文章中所述的解決方案可讓客戶以近乎即時的即時摘要解決方案來使用影片索引子。 根據要編制索引的資料區塊、輸入解析度、內容類型，以及此程式所使用的計算，此解決方案的延遲時間最少可達四分鐘。

![即時資料流上的影片索引子中繼資料](./media/live-stream-analysis/live-stream-analysis01.png)

*圖1–顯示即時串流上影片索引子中繼資料的範例播放機*

「[串流分析」解決方案](https://aka.ms/livestreamanalysis)會使用 Azure Functions 和兩個 Logic Apps，以使用影片索引子 Azure 媒體服務中的即時頻道處理即時程式，並顯示結果，Azure 媒體播放機顯示近乎即時的產生串流。

在高層級中，它是由兩個主要步驟組成。 第一個步驟每隔60秒會執行一次，並取得最後一次60秒的子剪輯，並從其建立資產，並透過影片索引子編制索引。 然後在索引完成後呼叫第二個步驟。 系統會處理所捕捉到的見解，並將其傳送至 Azure Cosmos DB，並刪除子剪輯索引。

範例播放者會使用專用的 Azure 函式來播放即時串流，並取得 Azure Cosmos DB 的深入解析。 它會顯示與即時影片同步的中繼資料和縮圖。

![在雲端中每分鐘處理即時串流的兩個邏輯應用程式](./media/live-stream-analysis/live-stream-analysis02.png)

*[圖 2]-兩個邏輯應用程式在雲端中每分鐘處理即時串流。*

## <a name="step-by-step-guide"></a>逐步指南 

如需完整的程式碼和部署結果的逐步指南，請參閱[使用影片索引子進行即時媒體分析的 GitHub 專案](https://aka.ms/livestreamanalysis)。 

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
