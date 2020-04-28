---
title: 影片索引器概念
titleSuffix: Azure Media Services
description: 本文說明 Azure 媒體服務影片索引子服務的一些概念。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900682"
---
# <a name="video-indexer-concepts"></a>影片索引器概念
 
本文將說明影片索引器服務的一些概念。
    
## <a name="summarized-insights"></a>深入解析摘要

深入解析摘要包含以下資料的彙總檢視：臉部、主題、表情。 例如，為避免逐一查看幾千個時間範圍來檢查其中有哪些臉部，深入解析摘要會包含所有臉部，以及每個臉部出現的時間範圍和其顯示時間的百分比。

## <a name="time-range-vs-adjusted-time-range"></a>時間範圍與調整後的時間範圍

TimeRange 是原始影片中的時間範圍。 AdjustedTimeRange 是相對於目前播放清單的時間範圍。 由於您可以從不同影片的不同時段建立播放清單，因此您可以取用 1 小時的影片，然後只使用其中的 1 個時段，例如，10:00-10:15。 在該案例中，您的播放清單中將會有 1 個時段，其時間範圍為 10:00-10:15，但 adjustedTimeRange 會是 00:00-00:15。
 
## <a name="blocks"></a>區塊

區塊是為了讓您能更輕鬆地瀏覽資料。 例如，區塊的分割點可能會在說話者變更或長時間暫停時。

## <a name="next-steps"></a>後續步驟

如需有關如何開始使用的資訊，請參閱[如何註冊及上傳您的第一個影片](video-indexer-get-started.md)。

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)
