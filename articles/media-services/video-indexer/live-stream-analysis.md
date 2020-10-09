---
title: 使用影片索引子的即時串流分析
titleSuffix: Azure Media Services
description: 本文說明如何使用影片索引子執行即時串流分析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74186004"
---
# <a name="live-stream-analysis-with-video-indexer"></a>使用影片索引子進行即時串流分析

Azure 媒體服務影片索引子是一項 Azure 服務，其設計目的是要從離線的影片和音訊檔案中解壓縮深度見解。 這是為了分析已預先建立的指定媒體檔案。 不過，在某些使用案例中，請務必儘快取得即時摘要中的媒體深入解析，以解除鎖定操作及時間所按下的其他使用案例。 例如，即時資料流上的這類豐富中繼資料可供內容產生者用來將電視生產自動化。

本文所述的解決方案，可讓客戶以近乎即時的方式在即時摘要上使用影片索引子。 根據所編制索引的資料區塊、輸入解析度、內容類型以及此程式所使用的計算，索引編制的延遲可能會是使用此解決方案所需的最少四分鐘。

![即時串流上的影片索引子中繼資料](./media/live-stream-analysis/live-stream-analysis01.png)

*圖1–顯示即時資料流上影片索引子中繼資料的範例播放機*

目前的 [資料流程分析解決方案](https://aka.ms/livestreamanalysis) 使用 Azure Functions 和兩個 Logic Apps，利用影片索引子從 Azure 媒體服務的實況頻道處理即時程式，並以 Azure 媒體播放機顯示近乎即時結果資料流程的結果來顯示結果。

在高層級中，它是由兩個主要步驟所組成。 第一個步驟會每隔60秒執行一次，並子剪輯最後一次60秒的時間，從其建立資產，並透過影片索引子編制索引。 然後在編制索引完成後呼叫第二個步驟。 系統會處理所捕捉的深入解析，並將其傳送至 Azure Cosmos DB，並刪除子剪輯索引。

範例播放機會播放即時串流，並使用專用的 Azure 函數從 Azure Cosmos DB 取得見解。 它會顯示與即時影片同步的中繼資料和縮圖。

![這兩個邏輯應用程式會在雲端中每分鐘處理即時串流](./media/live-stream-analysis/live-stream-analysis02.png)

*圖2–兩個邏輯應用程式在雲端中每分鐘處理即時資料流。*

## <a name="step-by-step-guide"></a>逐步指南 

您可以在 [使用影片索引子進行即時媒體分析的 GitHub 專案](https://aka.ms/livestreamanalysis)中，找到部署結果的完整程式碼和逐步指南。 

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
