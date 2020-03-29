---
title: Azure 媒體服務舊元件 |微軟文檔
description: 本主題討論 Azure 媒體服務舊元件。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921074"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒體服務舊元件

隨著時間的推移，媒體服務元件得到了穩步的改進和增強。 因此，某些舊元件已停用。 您可以在以下文章中找到有關如何將應用程式從舊元件遷移到當前元件的說明。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>遺留元件的退休計畫和遷移指南

我們宣佈棄用*Windows Azure 媒體編碼器*（WAME） 和*Azure 媒體編碼器*（AME） 媒體處理器。 這些處理器于 2020 年 3 月 31 日停用。

* [從 Windows Azure 媒體編碼器遷移到媒體編碼器標準](migrate-windows-azure-media-encoder.md)
* [從 Azure 媒體編碼器遷移到媒體編碼器標準](migrate-azure-media-encoder.md)

我們還宣佈停用以下媒體分析媒體處理器： 
 
|媒體處理器名稱|停用日期|其他注意事項|
|---|---|
|[Azure 媒體索引器 2](media-services-process-content-with-indexer2.md)|2020年1月1日|此媒體處理器正在被 Azure[媒體服務視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)替換。 有關詳細資訊，請參閱從[Azure 媒體索引子 2 遷移到 Azure 媒體服務視頻索引子](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2023年3月1日|此媒體處理器正在被 Azure[媒體服務視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)替換。 有關詳細資訊，請參閱從[Azure 媒體索引子遷移到 Azure 媒體服務視頻索引子](migrate-indexer-v1-v2.md)|
|[運動檢測](media-services-motion-detection.md)|2020年6月1日|目前沒有更換計畫。|
|[視頻摘要](media-services-video-summarization.md)|2020年6月1日|目前沒有更換計畫。|
|[視頻光學字元辨識](media-services-video-optical-character-recognition.md)|2020年6月1日|此媒體處理器正在被 Azure[媒體服務視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)替換。 此外，請考慮使用[Azure 媒體服務 v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)。 <br/>請參閱[比較 Azure 媒體服務 v3 預設和視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[臉部偵測](media-services-face-and-emotion-detection.md)|2020年6月1日|此媒體處理器正在被 Azure[媒體服務視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)替換。 此外，請考慮使用[Azure 媒體服務 v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)。 <br/>請參閱[比較 Azure 媒體服務 v3 預設和視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[內容檢閱者](media-services-content-moderation.md)|2020年6月1日|此媒體處理器正在被 Azure[媒體服務視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)替換。 此外，請考慮使用[Azure 媒體服務 v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)。 <br/>請參閱[比較 Azure 媒體服務 v3 預設和視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>後續步驟

[從媒體服務 v2 移動至 v3 的移轉指導](../latest/migrate-from-v2-to-v3.md)
