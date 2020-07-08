---
title: Azure 媒體服務舊版元件 |Microsoft Docs
description: 本主題討論 Azure 媒體服務的舊版元件。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77921074"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒體服務舊版元件

經過一段時間，媒體服務元件已有穩定的改良功能和增強功能。 因此，某些舊版元件已淘汰。 您可以在下列文章中找到如何將應用程式從舊版元件遷移至目前元件的指示。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>舊版元件和遷移指引的淘汰計畫

我們宣布淘汰「Windows Azure 媒體編碼器」(WAME) 和「Azure 媒體編碼器」(AME) 媒體處理器。 這些處理器將于2020年3月31日淘汰。

* [從 Windows Azure 媒體編碼器遷移至媒體編碼器標準](migrate-windows-azure-media-encoder.md)
* [從 Azure 媒體編碼器遷移至媒體編碼器標準](migrate-azure-media-encoder.md)

我們也宣佈淘汰下列媒體分析媒體處理器： 
 
|媒體處理器名稱|停用日期|其他注意事項|
|---|---|
|[Azure 媒體索引器 2](media-services-process-content-with-indexer2.md)|2020年1月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 如需詳細資訊，請參閱[從 Azure 媒體索引子2遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2023年3月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 如需詳細資訊，請參閱[從 Azure 媒體索引子遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)|
|[動作偵測](media-services-motion-detection.md)|2020年6月1日|目前沒有任何取代計畫。|
|[影片摘要](media-services-video-summarization.md)|2020年6月1日|目前沒有任何取代計畫。|
|[影片光學字元辨識](media-services-video-optical-character-recognition.md)|2020年6月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 此外，請考慮使用[Azure 媒體服務 V3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)。 <br/>請參閱[比較 Azure 媒體服務 v3 預設值和影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[臉部偵測](media-services-face-and-emotion-detection.md)|2020年6月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 此外，請考慮使用[Azure 媒體服務 V3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)。 <br/>請參閱[比較 Azure 媒體服務 v3 預設值和影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[內容仲裁](media-services-content-moderation.md)|2020年6月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 此外，請考慮使用[Azure 媒體服務 V3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)。 <br/>請參閱[比較 Azure 媒體服務 v3 預設值和影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>後續步驟

[從媒體服務 v2 移動至 v3 的移轉指導](../latest/migrate-from-v2-to-v3.md)
