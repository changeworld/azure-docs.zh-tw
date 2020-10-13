---
title: Azure 媒體服務舊版元件 |Microsoft Docs
description: 本主題討論 Azure 媒體服務舊版元件。
services: media-services
documentationcenter: ''
author: myoungerman
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: v-myoung
ms.openlocfilehash: d5a10ccc887124b2bc5595d66baad833a32275cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600886"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒體服務舊版元件

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

經過一段時間之後，我們就能增強媒體服務元件並淘汰舊版元件。 本文可協助您將應用程式從舊版元件遷移至目前的元件。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>舊版元件和遷移指引的淘汰計畫

*Windows Azure 媒體編碼器* (WAME) 和*Azure 媒體編碼器* (AME) 媒體處理器已淘汰。

* [從 Windows Azure 媒體編碼器遷移至媒體編碼器標準](migrate-windows-azure-media-encoder.md)
* [從 Azure 媒體編碼器遷移至媒體編碼器標準](migrate-azure-media-encoder.md)

下列媒體分析媒體處理器已淘汰或即將淘汰：  
 
|媒體處理器名稱|停用日期|其他注意事項|
|---|---|
|Azure 媒體索引器 2|2020年1月1日|此媒體處理器已由 [Azure 媒體服務影片索引子](../video-indexer/index.yml)取代。 如需詳細資訊，請參閱 [從 Azure 媒體索引子2遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2023年3月1日|[Azure 媒體服務的影片索引子](../video-indexer/index.yml)將會取代此媒體處理器。 如需詳細資訊，請參閱 [從 Azure 媒體索引子遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)。|
|動作偵測|2020年6月1日|目前沒有取代方案。|
|影片摘要] (media-services-video-summarization.md|2020年6月1日|目前沒有取代方案。|
|影片光學字元辨識|2020年6月1日|此媒體處理器已由 [Azure 媒體服務影片索引子](../video-indexer/index.yml)取代。 此外，請考慮使用 [Azure 媒體服務 V3 API](../latest/analyzing-video-audio-files-concept.md)。 <br/>請參閱 [比較 Azure 媒體服務 v3 預設和影片索引子](../video-indexer/compare-video-indexer-with-media-services-presets.md)。|
|臉部偵測|2020年6月1日|此媒體處理器已由 [Azure 媒體服務影片索引子](../video-indexer/index.yml)取代。 此外，請考慮使用 [Azure 媒體服務 V3 API](../latest/analyzing-video-audio-files-concept.md)。 <br/>請參閱 [比較 Azure 媒體服務 v3 預設和影片索引子](../video-indexer/compare-video-indexer-with-media-services-presets.md)。|
|內容仲裁|2020年6月1日|此媒體處理器已由 [Azure 媒體服務影片索引子](../video-indexer/index.yml)取代。 此外，請考慮使用 [Azure 媒體服務 V3 API](../latest/analyzing-video-audio-files-concept.md)。 <br/>請參閱 [比較 Azure 媒體服務 v3 預設和影片索引子](../video-indexer/compare-video-indexer-with-media-services-presets.md)。|

## <a name="next-steps"></a>後續步驟

[從媒體服務 v2 移動至 v3 的移轉指導](../latest/migrate-from-v2-to-v3.md)
