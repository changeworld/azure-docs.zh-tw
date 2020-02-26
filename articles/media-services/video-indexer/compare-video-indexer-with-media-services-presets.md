---
title: 影片索引子和 Azure 媒體服務 v3 預設值的比較
description: 本文會比較影片索引子功能和 Azure 媒體服務 v3 預設值。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602183"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>比較 Azure 媒體服務 v3 預設值與影片索引子 

本文將比較**影片索引子 API** 與**媒體服務 v3 API** 的功能。 

目前，[影片索引子 api](https://api-portal.videoindexer.ai/)和[媒體服務 v3 api](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)所提供的功能有重迭。 下表提供了解其差異和相似性的現行指導方針。 

## <a name="compare"></a>比較

|功能|影片索引子 API |影片分析器和音訊分析器的預設值<br/>在 Azure 媒體服務 v3 中|
|---|---|---|
|媒體深入解析|[增強](video-indexer-output-json-v2.md) |[基礎](../latest/intelligence-concept.md)|
|體驗|請參閱支援功能的完整清單︰ <br/> [概觀](video-indexer-overview.md)|僅傳回影片深入解析|
|計費|[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|法規遵循|如需最新的合規性更新，請造訪[Azure 合規性供應專案 .pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf)並搜尋「影片索引子」，以查看它是否符合相關憑證。|如需最新的合規性更新，請造訪[Azure 合規性供應專案 .pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf)並搜尋 "媒體服務"，以查看其是否符合相關憑證。|
|免費試用|美國東部|無法使用|
|區域可用性|查看[依區域的認知服務可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|請參閱[依區域的媒體服務可用性](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。|

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)

[媒體服務 v3 概觀](../latest/media-services-overview.md)
