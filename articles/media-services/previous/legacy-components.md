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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: cfc9d944f7851f9b802e18c861fd16438171b8e7
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069570"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒體服務舊版元件

經過一段時間，媒體服務元件已有穩定的改良功能和增強功能。 因此，某些舊版元件已淘汰。 您可以在下列文章中找到如何將應用程式從舊版元件遷移至目前元件的指示。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>舊版元件和遷移指引的淘汰計畫

我們宣佈*Windows Azure 媒體編碼器*（WAME）和*Azure 媒體編碼器*（AME）媒體處理器已淘汰。 這些處理器將于2020年3月31日淘汰。

* [從 Windows Azure 媒體編碼器遷移至媒體編碼器標準](migrate-windows-azure-media-encoder.md)
* [從 Azure 媒體編碼器遷移至媒體編碼器標準](migrate-azure-media-encoder.md)

我們也宣佈淘汰下列媒體分析媒體處理器： 
 
|媒體處理器名稱|停用日期|其他注意事項|
|---|---|
|[Azure 媒體索引子2](media-services-process-content-with-indexer2.md)|2020年1月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 如需詳細資訊，請參閱[從 Azure 媒體索引子2遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2023年3月1日|[Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代此媒體處理器。 如需詳細資訊，請參閱[從 Azure 媒體索引子遷移至 Azure 媒體服務影片索引子](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>後續步驟

[從媒體服務 v2 移動至 v3 的移轉指導](../latest/migrate-from-v2-to-v3.md)
