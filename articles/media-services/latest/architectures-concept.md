---
title: 媒體服務架構
titleSuffix: Azure Media Services
description: 本文說明媒體服務的架構。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: c14dbe4253696e01e99111a24319e455c1d551e9
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533990"
---
# <a name="media-services-architectures"></a>媒體服務架構

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>即時視訊串流數位媒體

即時串流解決方案可讓您即時捕獲影片，並即時將影片廣播給取用者，例如線上串流訪談、會議及體育活動。 在此解決方案中，影片會由攝影機捕獲，並傳送至通道輸入端點。 通道會接收即時輸入資料流程，並讓它可透過串流端點串流至網頁瀏覽器或行動應用程式。 通道也會提供預覽監視端點，以預覽和驗證您的串流，然後再進一步處理和傳遞。 通道也可以記錄和儲存內嵌內容，以便稍後 (隨選影片) 。

此解決方案建基於 Azure 受控服務：媒體服務和內容傳遞網路。 這些服務是在高可用性環境中執行（經過修補和支援），可讓您專注于解決方案，而不是其執行所在的環境。

請參閱 Azure 架構中心內的 [即時串流數位媒體](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-live-stream) 。

## <a name="video-on-demand-digital-media"></a>點播視訊數位媒體

一種基本的隨選影片解決方案，可讓您將錄製的影片內容（例如電影、新聞剪輯、運動區段、訓練影片和客戶支援教學課程）串流至任何支援影片的端點裝置、行動應用程式或桌面瀏覽器。 影片檔案會上傳至 Azure Blob 儲存體，並編碼為多位元率標準格式，然後透過所有主要的彈性位元速率串流通訊協定散發 (HLS、MPEG、順暢) 至 Azure 媒體播放機用戶端。

此解決方案建基於 Azure 受控服務： Blob 儲存體、內容傳遞網路及 Azure 媒體播放機。 這些服務是在高可用性環境中執行（經過修補和支援），可讓您專注于解決方案，而不是其執行所在的環境。

查看 Azure 架構中心內 [隨選影片的數位媒體](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-video) 。

## <a name="gridwich-media-processing-system"></a>Gridwich 媒體處理系統

Gridwich 系統是在 Azure 上處理和傳遞媒體資產的最佳作法。 雖然 Gridwich 系統是媒體專屬的，但訊息處理和事件架構可以套用至任何無狀態事件處理工作流程。

請參閱 Azure 架構中心的 [Gridwich 媒體處理系統](https://docs.microsoft.com/azure/architecture/reference-architectures/media-services/gridwich-architecture) 。

## <a name="next-steps"></a>後續步驟

> [Azure 媒體服務概觀](media-services-overview.md)