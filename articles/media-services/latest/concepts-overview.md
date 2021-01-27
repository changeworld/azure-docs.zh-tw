---
title: 媒體服務術語和概念
description: 瞭解 Azure 媒體服務的術語和概念。
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: b425cd9268b336a8926e4fad9cb1f288f4fe3e87
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897912"
---
# <a name="media-services-terminology-and-concepts"></a>媒體服務術語和概念

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本主題提供 Azure 媒體服務術語和概念的簡短總覽。 本文也會提供文章的連結，其中包含媒體服務 v3 概念和功能的深入說明。

在開始開發之前，請先檢查這些主題中所述的基本概念。

> [!NOTE]
> 目前，您可以使用 [Azure 入口網站](https://portal.azure.com/) 來：管理媒體服務 V3 的 [實況活動](live-events-outputs-concept.md)、觀看 (不管理) v3 [資產](assets-concept.md)，以及 [取得存取 api 的相關資訊](./access-api-howto.md)。
> 針對所有其他管理工作 (例如，[轉換和作業](transforms-jobs-concept.md)和[內容保護](content-protection-overview.md))，請使用 [REST API](/rest/api/media/accountfilters)、[CLI](/cli/azure/ams) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>媒體服務 v3 術語

|詞彙|描述|
|---|---|
|實況活動|**實況活動** 代表擷取的管線、轉碼 (選擇性) ，以及封裝影片、音訊和即時中繼資料的即時資料流。<br/><br/>針對從媒體服務 v2 Api 遷移的客戶， **實況活動** 會取代 v2 中的 **通道** 實體。 如需詳細資訊，請參閱 [從 V2 遷移至 v3](migrate-v-2-v-3-migration-introduction.md)。|
|串流端點/封裝/來源|**串流端點** 代表動態 (即時) 封裝和原始服務，可將您的即時和隨選內容直接傳遞至用戶端播放程式應用程式。 它會使用其中一種常見的串流媒體通訊協定 (HLS 或虛線) 。 此外， **串流端點** 也提供動態的 (即時) 加密給領先業界的數位版權管理系統 (drm) 。<br/><br/>在媒體串流產業中，這項服務通常稱為封裝 **程式或****來源**。  這項功能的產業中其他常見的詞彙包括 JITP (即時封裝) 或 JITE (及時加密) 。

## <a name="media-services-v3-concepts"></a>媒體服務 v3 概念

|概念|描述|連結|
|---|---|---|
|資產和上傳內容|若要開始在 Azure 中管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶，並將數位檔案上傳至 **資產**。|[雲端上傳和儲存體](storage-account-concept.md)<br/><br/>[資產概念](assets-concept.md)|
|內容編碼|將高品質的數位媒體檔案上傳到資產之後，您就可以將它們編碼成可在各種不同的瀏覽器和裝置上播放的格式。 <br/><br/>若要使用媒體服務 v3 來編碼，您需要建立 **轉換** 和 **作業**。|[轉換和作業](transforms-jobs-concept.md)<br/><br/>[使用媒體服務編碼](encoding-concept.md)|
|分析內容 (影片索引器)|媒體服務 v3 可讓您使用媒體服務 v3 預設，從影片和音訊檔案中解壓縮見解。 若要使用媒體服務 v3 預設來分析您的內容，您需要建立 **轉換** 和 **作業**。<br/><br/>如果您想要更多詳細的深入解析，請直接使用[影片索引子](../video-indexer/index.yml)。|[分析視訊和音訊檔案](analyzing-video-audio-files-concept.md)|
|封裝和交付|當您的內容經過編碼之後，您就可以利用 **動態封裝**。 在媒體服務中， **串流端點** 是用來將媒體內容傳遞給用戶端播放程式的動態封裝服務。 若要讓輸出資產中的影片可供用戶端播放，您必須建立 **串流定位器** ，然後建立串流 url。 <br/><br/>建立 **串流定位器** 時，除了資產的名稱之外，您還需要指定 **串流原則**。 如果您的 **串流定位器** 有任何) ，**串流原則** 可讓您定義串流通訊協定和加密選項 (。 無論您是即時或依需求串流您的內容，都會使用動態封裝。 <br/><br/>您可以使用媒體服務 **動態資訊清單** ，只串流特定的轉譯或影片 subclips。|[動態封裝](dynamic-packaging-overview.md)<br/><br/>[串流端點](streaming-endpoint-concept.md)<br/><br/>[串流定位器](streaming-locators-concept.md)<br/><br/>[串流原則](streaming-policy-concept.md)<br/><br/>[動態資訊清單](filters-dynamic-manifest-overview.md)<br/><br/>[篩選條件](filters-concept.md)|
|內容保護|使用媒體服務時，您可以提供動態加密的即時和隨選內容，並使用進階加密標準 (AES-128) 或/和三個主要 DRM 系統的任何一個： Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 <br/><br/>如果在您的串流上指定加密選項，請建立 **內容金鑰原則** ，並將它與您的 **串流定位器** 建立關聯。 **內容金鑰原則** 可讓您設定將內容金鑰傳遞給終端用戶端的方式。<br/><br/> 當需要相同的選項時，請嘗試重複使用原則。| [內容金鑰原則](content-key-policy-concept.md)<br/><br/>[內容保護](content-protection-overview.md)|
|即時串流|媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 **實況活動** 負責內嵌和處理即時視訊摘要。 當您建立 **實況活動** 時，系統會建立輸入端點，供您用來從遠端編碼器傳送即時信號。 當資料流程流入 **實況活動** 之後，您就可以建立 **資產**、 **即時輸出** 和 **串流定位器** 來開始串流事件。 **即時輸出** 會將串流封存至 **資產** ，並透過 **串流端點** 將其提供給檢視器。 即時事件可設定為 *傳遞* (內部部署即時編碼器會傳送多重位元速率串流) 或 *即時編碼* (內部部署即時編碼器會傳送單一位元速率串流)。 |[即時串流概觀](live-streaming-overview.md)<br/><br/>[實況活動與實況輸出](live-events-outputs-concept.md)|
|使用事件方格進行監視|若要查看作業的進度，請使用 **事件方格**。 媒體服務也會發出即時事件種類。 透過事件方格，您的應用程式幾乎可以從所有 Azure 服務和自訂來源接聽及回應事件。 |[處理事件方格事件](reacting-to-media-services-events.md)<br/><br/>[結構描述](media-services-event-schemas.md)|
|使用 Azure 監視器監視|監視計量和診斷記錄，以協助您瞭解應用程式如何使用 Azure 監視器來執行。|[計量和診斷記錄](media-services-metrics-diagnostic-logs.md)<br/><br/>[診斷記錄結構描述](media-services-diagnostic-logs-schema.md)|
|播放程式用戶端|您可以使用 Azure 媒體播放機來播放媒體服務在各種不同的瀏覽器和裝置上串流處理的媒體內容。 Azure Media Player 採用業界標準，例如 HTML5、媒體來源延伸模組 (MSE) 和加密媒體擴充功能 (EME)，提供豐富的調適性串流體驗。 |[Azure 媒體播放器概觀](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [遠端檔案編碼和串流影片 - REST](stream-files-tutorial-with-rest.md)
* [已上傳檔案編碼和串流影片 - .NET](stream-files-tutorial-with-api.md)
* [即時串流 - .NET](stream-live-tutorial-with-api.md)
* [分析影片 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動態加密 - .NET](protect-with-aes128.md)
* [透過多重 DRM 進行動態加密 - .NET](protect-with-drm.md)