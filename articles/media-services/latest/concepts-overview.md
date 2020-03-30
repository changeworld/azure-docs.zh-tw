---
title: 媒體服務術語和概念
titleSuffix: Azure Media Services
description: 瞭解 Azure 媒體服務的術語和概念。
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500098"
---
# <a name="media-services-terminology-and-concepts"></a>媒體服務術語和概念

本主題簡要概述了 Azure 媒體服務的術語和概念。 本文還提供了指向文章的連結，並深入解釋了媒體服務 v3 的概念和功能。

在開始開發之前，應審查這些主題中描述的基本概念。

> [!NOTE]
> 目前，您可以使用 Azure[門戶](https://portal.azure.com/)：管理媒體服務 v3[即時事件](live-events-outputs-concept.md)、查看（未管理）v3[資產](assets-concept.md)，以及[獲取有關訪問 API 的資訊](access-api-portal.md)。
> 對於所有其他管理工作（例如，[轉換、作業](transforms-jobs-concept.md)和[內容保護](content-protection-overview.md)），請使用[REST API](https://aka.ms/ams-v3-rest-ref) [、CLI](https://aka.ms/ams-v3-cli-ref)或受支援的[SDK](media-services-apis-overview.md#sdks)之一。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>媒體服務 v3 術語

|詞彙|描述|
|---|---|
|現場活動|**即時事件**表示用於引入、轉碼（可選）和打包視頻、音訊和即時中繼資料的即時流的管道。<br/><br/>對於從媒體服務 v2 API 遷移的客戶，**即時事件**將替換 v2 中的**通道**實體。 有關詳細資訊，請參閱從[v2 遷移到 v3](migrate-from-v2-to-v3.md)。|
|流式處理終結點/打包/原點|**流式處理終結點**表示動態（即時）打包和原點服務，可直接將即時和按需內容傳遞到用戶端播放機應用程式。 它使用一種常見的流媒體協議（HLS 或 DASH）。 此外，**流式處理終結點**為業界領先的數位版權管理系統 （DRM） 提供動態（及時）加密。<br/><br/>在媒體流媒體行業，此服務通常稱為**打包器**或**源**。  此功能的行業中其他常見術語包括 JITP（及時打包器）或 JITE（及時加密）。

## <a name="media-services-v3-concepts"></a>媒體服務 v3 概念

|概念|描述|連結|
|---|---|---|
|資產和上傳內容|要開始在 Azure 中管理、加密、編碼、分析和資料流媒體內容，需要創建媒體服務帳戶並將數位檔上載到**資產**中。|[雲端上傳和儲存體](storage-account-concept.md)<br/><br/>[資產概念](assets-concept.md)|
|內容編碼|將高品質數位媒體檔案上載到"資產"後，即可將它們編碼為可在各種瀏覽器和設備上播放的格式。 <br/><br/>要使用媒體服務 v3 進行編碼，您需要創建**轉換**和**作業**。|[轉換與作業](transforms-jobs-concept.md)<br/><br/>[使用媒體服務編碼](encoding-concept.md)|
|分析內容 (影片索引器)|媒體服務 v3 允許您使用媒體服務 v3 預設從視頻和音訊檔中獲取見解。 要使用媒體服務 v3 預設分析內容，您需要創建**轉換**和**作業**。<br/><br/>如果需要更詳細的見解，請使用[視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)。|[分析視訊和音訊檔案](analyzing-video-audio-files-concept.md)|
|封裝和交付|一旦您的內容被編碼，你可以利用**動態打包**。 在媒體服務中，**流式處理終結點**是用於向用戶端播放機提供媒體內容的動態打包服務。 要使輸出資產中的視頻可供用戶端播放，必須創建**流式定位器**，然後生成流式處理 URL。 <br/><br/>創建**流式處理器**時，除了資產的名稱外，還需要指定**流式處理策略**。 **流式處理策略**使您能夠為**流式處理器**定義流式處理協定和加密選項（如果有）。 無論您即時資料流內容還是按需資料流內容，都使用動態打包。 <br/><br/>您可以使用媒體服務**動態清單**僅資料流視頻的特定格式副本或子剪輯。|[動態封裝](dynamic-packaging-overview.md)<br/><br/>[流式處理終結點](streaming-endpoint-concept.md)<br/><br/>[串流定位器](streaming-locators-concept.md)<br/><br/>[串流原則](streaming-policy-concept.md)<br/><br/>[動態資訊清單](filters-dynamic-manifest-overview.md)<br/><br/>[篩選器](filters-concept.md)|
|內容保護|借助媒體服務，您可以使用進階加密標準 （AES-128） 或/以及三個主要 DRM 系統中的任何一個動態加密的即時和點播內容：微軟 PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 <br/><br/>如果在流中指定加密選項，請創建**內容金鑰策略**並將其與**流式處理器**相關聯。 通過 **"內容金鑰策略**"，您可以配置內容金鑰如何傳遞到最終用戶端。<br/><br/> 當需要相同的選項時，請嘗試重用策略。| [內容金鑰原則](content-key-policy-concept.md)<br/><br/>[內容保護](content-protection-overview.md)|
|即時串流|媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 **實況活動**負責內嵌和處理即時視訊摘要。 創建**即時事件**時，將創建一個輸入終結點，可用於從遠端編碼器發送即時信號。 一旦流流入**即時事件**，您可以通過創建**資產**、**即時輸出**和**流式處理器**來開始流式處理事件。 **即時輸出**將動態流存檔到**資產**中，並通過**流式處理終結點**向查看者提供。 即時事件可以設置為*傳遞*（本地即時編碼器發送多個位元速率流）或*即時編碼*（本地即時編碼器發送單個位元速率流）。 |[即時串流概觀](live-streaming-overview.md)<br/><br/>[實況活動與實況輸出](live-events-outputs-concept.md)|
|使用事件網格進行監控|要查看作業的進度，請使用**事件網格**。 媒體服務還會發出即時事件種類。 透過事件方格，您的應用程式幾乎可以從所有 Azure 服務和自訂來源接聽及回應事件。 |[處理事件方格事件](reacting-to-media-services-events.md)<br/><br/>[結構描述](media-services-event-schemas.md)|
|使用 Azure 監視器進行監視|監視指標和診斷日誌，説明您瞭解應用如何使用 Azure 監視器。|[計量和診斷記錄](media-services-metrics-diagnostic-logs.md)<br/><br/>[診斷記錄結構描述](media-services-diagnostic-logs-schema.md)|
|播放程式用戶端|您可以使用 Azure 媒體播放機播放媒體服務在各種瀏覽器和設備上資料流的媒體內容。 Azure 媒體播放機使用行業標準（如 HTML5、媒體源擴展 （MSE） 和加密媒體擴展 （EME） 來提供豐富的自我調整流體驗。 |[Azure 媒體播放器概觀](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [遠端檔案編碼和串流影片 - REST](stream-files-tutorial-with-rest.md)
* [已上傳檔案編碼和串流影片 - REST](stream-files-tutorial-with-api.md)
* [即時串流 - .NET](stream-live-tutorial-with-api.md)
* [分析影片 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動態加密 - .NET](protect-with-aes128.md)
* [透過多重 DRM 進行動態加密 - .NET](protect-with-drm.md)
