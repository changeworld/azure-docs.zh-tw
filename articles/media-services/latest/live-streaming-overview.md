---
title: 使用 Azure 媒體服務 v3 進行即時串流的總覽 |Microsoft Docs
description: 本文提供使用 Azure 媒體服務 v3 進行即時串流的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: 23ee7ba7a5456916eb307e21aa2074924614cb4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84418138"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒體服務 v3 進行即時串流

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要使用媒體服務串流實況活動，您需要下列項目：  

- 拍攝實況活動用的相機。<br/>如需了解裝備，請參閱[簡單的可攜式活動視訊器材裝備]( https://link.medium.com/KNTtiN6IeT) \(英文\)。

    如果您沒有攝影機的存取權，可以使用像是[Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm)的工具，從影片檔案產生即時摘要。
- 即時視訊編碼器，可將來自相機 (或另一部裝置，例如膝上型電腦) 的訊號轉換成發佈摘要以傳送給「媒體服務」。 發佈摘要可以包含廣告相關訊號，例如 SCTE-35 標記。<br/>如需建議的即時串流編碼器清單，請參閱[即時串流編碼器](recommended-on-premises-live-encoders.md)。 此外，請參閱此 blog：[使用 OBS 的即時串流生產](https://link.medium.com/ttuwHpaJeT)。
- 媒體服務中的元件可讓您內嵌、預覽、封裝、記錄、加密實況活動，並向客戶廣播這些活動，或是向 CDN 廣播以進一步發佈。

對於想要將內容傳遞給大型網際網路物件的客戶，我們建議您在[串流端點](streaming-endpoint-concept.md)上啟用 CDN。

本文提供使用媒體服務的即時串流總覽和指導方針，以及其他相關文章的連結。
 
> [!NOTE]
> 您可以使用[Azure 入口網站](https://portal.azure.com/)來管理 V3[即時事件](live-events-outputs-concept.md)、查看 v3[資產](assets-concept.md)、取得存取 api 的相關資訊。 針對所有其他管理工作 (例如，轉換和作業)，請使用 [REST API](https://docs.microsoft.com/rest/api/media/)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

## <a name="dynamic-packaging-and-delivery"></a>動態封裝和傳遞

使用媒體服務，您可以利用[動態封裝](dynamic-packaging-overview.md)，讓您以[MPEG 破折號、HLS 和 Smooth Streaming 格式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，從傳送到服務的發佈摘要中預覽和廣播即時串流。 您的檢視者可以搭配支援 HLS、DASH 或 Smooth Streaming 的播放器播放即時串流。 您可以在網路或行動應用程式中使用 [Azure 媒體播放器](https://amp.azure.net/libs/amp/latest/docs/index.html)，透過這些通訊協定中的任何一個傳遞串流。

## <a name="dynamic-encryption"></a>動態加密

「動態加密」可讓您使用 AES-128 或下列三個主要數位版權管理（DRM）系統中的任何一種，以動態方式加密您的即時或隨選內容： Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 如需詳細資訊，請參閱[動態加密](content-protection-overview.md)。

> [!NOTE]
> Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="dynamic-filtering"></a>動態篩選

動態篩選是用來控制傳送給播放程式的曲目、格式、位元速率和呈現時間時段的數目。 如需詳細資訊，請參閱[篩選器和動態資訊清單](filters-dynamic-manifest-overview.md)。

## <a name="live-event-types"></a>即時事件類型

[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)會負責內嵌和處理即時影片摘要。 即時事件可設定為*傳遞* (內部部署即時編碼器會傳送多重位元速率串流) 或*即時編碼* (內部部署即時編碼器會傳送單一位元速率串流)。 如需媒體服務 v3 中即時串流的詳細資訊，請參閱[即時事件和即時輸出](live-events-outputs-concept.md)。

### <a name="pass-through"></a>傳遞

![即時通行](./media/live-streaming/pass-through.svg)

使用傳遞**即時事件**時，您會依賴內部部署即時編碼器來產生多位元率的影片串流，並將其作為發佈摘要傳送至即時事件（使用 RTMP 或分散的輸入通訊協定）。 實況活動接著會透過傳入的影片串流傳送至動態封裝程式（串流端點），而不需要任何進一步的轉碼。 這類傳遞即時事件已針對長時間執行的即時事件或24x365 線性即時串流進行優化。 

### <a name="live-encoding"></a>即時編碼  

![即時編碼](./media/live-streaming/live-encoding.svg)

搭配媒體服務使用雲端編碼時，您會設定內部部署即時編碼器將單一位元速率的影片當做發佈摘要（最多32Mbps 匯總）傳送至即時事件（使用 RTMP 或分散的輸入通訊協定）。 即時事件會以不同的解析度將內送單一位元速率串流轉碼到[多個位元速率影片串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，以改善傳遞並讓它可透過業界標準的通訊協定（例如 MPEG-破折號、Apple HTTP 即時串流（HLS）和 Microsoft Smooth Streaming）傳遞到播放裝置。 

### <a name="live-transcription-preview"></a>即時轉譯（預覽）

即時轉譯是一項功能，可讓您搭配傳遞或即時編碼的即時事件使用。 如需詳細資訊，請參閱[即時](live-transcription.md)轉譯。 啟用這項功能時，此服務會使用認知服務的[語音轉換文字](../../cognitive-services/speech-service/speech-to-text.md)功能，將傳入的音訊中說出口的話轉譯成文字。 然後，在 MPEG-2 和 HLS 通訊協定中，此文字就能隨著視訊和音訊一起傳遞。

> [!NOTE]
> 目前，即時轉譯在美國西部2中是以預覽功能的形式提供。

## <a name="live-streaming-workflow"></a>即時串流工作流程

若要瞭解媒體服務 v3 中的即時串流工作流程，您必須先複習並瞭解下列概念： 

- [串流端點](streaming-endpoint-concept.md)
- [即時活動與即時輸出](live-events-outputs-concept.md)
- [串流定位器](streaming-locators-concept.md)

### <a name="general-steps"></a>一般步驟

1. 在您的媒體服務帳戶中，確定**串流端點**（原點）正在執行。 
2. 建立[即時事件](live-events-outputs-concept.md)。 <br/>在建立事件時，您可以指定要自動啟動它。 或者，您可以在準備好開始進行串流處理時啟動事件。<br/> 當自動啟動設定為 true 時，即時事件將會在建立後隨即啟動。 只要即時事件一執行，就會立即開始計費。 您必須在即時事件資源上明確呼叫「停止」，才能終止進一步計費。 如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。
3. 取得內嵌 URL，並將您的內部部署編碼器設定為使用 URL 來傳送發佈摘要。<br/>請參閱[建議的即時編碼器](recommended-on-premises-live-encoders.md)。
4. 取得預覽 URL 並使用它來確認實際上已收到來自編碼器的輸入。
5. 建立新的**資產**物件。 

    每個即時輸出都會與資產相關聯，用來將影片記錄到相關聯的 Azure blob 儲存體容器中。 
6. 建立**即時輸出**並使用您所建立的資產名稱，讓串流可以封存到資產中。

    「實況輸出」會在建立時開始，並在刪除時結束。 當您刪除即時輸出時，不會刪除資產中的基礎資產和內容。
7. 使用[內建的串流原則類型](streaming-policy-concept.md)來建立**串流定位器**。

    若要發佈即時輸出，您必須為相關聯的資產建立串流定位器。 
8. 列出**串流定位器**上的路徑，以取回要使用的 url （這些都是具決定性的）。
9. 取得您想要串流**處理的串流端點**（來源）的主機名稱。
10. 結合步驟 8 的 URL 和步驟 9 的主機名稱，即可取得完整的 URL。
11. 如果您想要停止讓**即時事件**可供查看，您必須停止串流處理事件，並刪除**串流定位器**。
12. 如果您完成串流處理事件，而且想要清除先前佈建的資源，請遵循下列程序。

    * 停止從編碼器發送串流。
    * 停止即時事件。 實況活動一旦停止，就不會產生任何費用。 當您需要重新啟動它時，它會具有相同的內嵌 URL，因此您不需要重新設定編碼器。
    * 除非您想要繼續將即時事件封存為隨選串流，否則您可以停止串流端點。 如果即時事件處於 [已停止] 狀態，則不會產生任何費用。

即時輸出所封存的資產，會在即時輸出刪除時自動成為隨選資產。 您必須先刪除所有即時輸出，才能停止即時事件。 您可以使用選擇性的旗標[removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) ，在停止時自動移除即時輸出。 

> [!TIP]
> 請參閱[即時串流教學](stream-live-tutorial-with-api.md)課程，這篇文章會檢查執行上述步驟的程式碼。

## <a name="other-important-articles"></a>其他重要文章

- [建議的即時編碼器](recommended-on-premises-live-encoders.md)
- [使用雲端 DVR](live-event-cloud-dvr.md)
- [即時事件種類功能比較](live-event-types-comparison.md)
- [狀態與計費](live-event-states-billing.md)
- [延遲](live-event-latency.md)

## <a name="frequently-asked-questions"></a>常見問題集

請參閱[常見問題](frequently-asked-questions.md#live-streaming)文章。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [即時串流快速入門](live-events-wirecast-quickstart.md)
* [即時串流教學課程](stream-live-tutorial-with-api.md)
* [從媒體服務 v2 移動至 v3 的移轉指導](migrate-from-v2-to-v3.md)
