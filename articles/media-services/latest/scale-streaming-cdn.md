---
title: 使用 CDN 整合來串流內容
titleSuffix: Azure Media Services
description: 瞭解如何使用 CDN 整合來串流內容，以及預先提取和原始協助 CDN 預先提取。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80128060"
---
# <a name="stream-content-with-cdn-integration"></a>使用 CDN 整合來串流內容

Azure 內容傳遞網路 (CDN) 為開發人員提供一套全域解決方案，讓他們可藉由在策略性分布於全球的實體節點上快取內容，將高頻寬內容迅速傳遞給使用者。  

CDN 會針對每個編解碼器、每個串流處理通訊協定、每一位容器格式，以及每個加密/DRM，快取從媒體服務[串流端點（原點）串流](streaming-endpoint-concept.md)的內容。 針對每個編解碼器串流通訊協定-容器格式-位元速率-加密的組合，將會有個別的 CDN 快取。

只要快取影片片段，受歡迎的內容就會直接從 CDN 快取中提供。 快取實況內容的機率很高，因為通常會有許多人觀看一模一樣的內容。 視需要內容可能有點棘手，因為您可能會有一些熱門的內容，有些則不會。 如果您有數百萬個不受歡迎的影片資產（一周只能有一或兩個檢視器），但您有數千名人員監看所有不同的影片，CDN 就會變得更不有效率。

您也需要考量彈性資料流的運作方式。 每個個別的影片片段都會快取為自己的實體。 例如，想像一下第一次監看特定的影片。 如果檢視器只會在此略過幾秒鐘的時間，而且只有與監看的人員相關的影片片段會在 CDN 中快取。 因為有彈性資料流，您通常會有 5 到 7 個位元速率不同的視訊。 如果其中一個人正在監看一個位元速率，而另一個人正在監看不同的位元速率，則它們會分別在 CDN 中快取。 即使兩個人監看相同的位元速率，它們也可以透過不同的通訊協定進行串流處理。 每個通訊協定 (HLS、MPEG DASH、Smooth Streaming) 會分別進行快取。 因此，每一個位元速率和通訊協定都會分別進行快取，而且只會快取已要求的那些視訊片段。

在決定是否要在媒體服務[串流端點](streaming-endpoint-concept.md)上啟用 CDN 時，請考慮預期的檢視器數目。 只有當您希望內容有許多檢視器時，CDN 才有説明。 如果檢視器的最大並行程度低於500，建議您停用 CDN，因為 CDN 會以並行的最佳方式調整。

本主題討論如何啟用[CDN 整合](#enable-azure-cdn-integration)。 它也會說明預先提取（主動快取）和[原始輔助 CDN 預先提取](#origin-assist-cdn-prefetch)概念。

## <a name="considerations"></a>考量

* [streaming endpoint](streaming-endpoint-concept.md) `hostname` 無論您是否啟用 CDN，串流端點和串流 URL 都保持不變。
* 如果您需要能夠在不使用 CDN 的情況下測試內容，請建立另一個未啟用 CDN 的串流端點。

## <a name="enable-azure-cdn-integration"></a>啟用 Azure CDN 整合

> [!IMPORTANT]
> 您無法為試用版或學生版 Azure 帳戶啟用 CDN。
>
> 除了美國聯邦政府和中國地區以外，所有 Azure 資料中心都會啟用 CDN 整合。

在布建啟用 CDN 的串流端點之後，在完成 DNS 更新以將串流端點對應至 CDN 端點之前，媒體服務已定義等待時間。

如果您稍後想要停用/啟用 CDN，串流端點必須處於**已停止**狀態。 可能需要將近 2 小時，Azure CDN 整合才會啟用，變更也才會遍及所有 CDN POP。 不過，您可以啟動串流端點和串流，而不會中斷串流端點。 整合完成後，就會從 CDN 傳遞串流。 在布建期間，您的串流端點會處於 [**啟動**中] 狀態，而您可能會發現效能降低。

建立標準串流端點時，預設會使用標準 Verizon 來設定它。 您可以使用 REST Api 來設定 Premium Verizon 或 Standard Akamai 提供者。

如果是標準串流端點，Azure 媒體服務與 Azure CDN 的整合是在**來自 Verizon 的 Azure CDN** 上實作。 您可以使用所有 **Azure CDN 定價層和提供者**來設定進階串流端點。

> [!NOTE]
> 如需 Azure CDN 的詳細資訊，請參閱[CDN 總覽](../../cdn/cdn-overview.md)。

## <a name="determine-if-a-dns-change-was-made"></a>判斷是否已進行 DNS 變更

您可以使用，判斷是否已在串流端點上進行 DNS 變更（流量會導向至 Azure CDN） <https://www.digwebinterface.com> 。 如果您在結果中看到 azureedge.net 的功能變數名稱，則流量現在會指向 CDN。

## <a name="origin-assist-cdn-prefetch"></a>原始-協助 CDN-預先提取

CDN 快取是一種被動的程式。 如果 CDN 可以預測下一個物件的要求，CDN 可以主動要求並快取下一個物件。 在此程式中，您可以針對物件的所有（或大部分）達到快取叫用，以改善效能。

預先提取的概念致力於將物件放在「網際網路的邊緣」，以預期這些會由播放程式即將要求，因而減少將該物件傳遞給播放程式的時間。

若要達成此目標，串流端點（原點）和 CDN 必須以數種方式在手中工作：

- 媒體服務原點必須要有「智慧」（來源協助），才能向 CDN 通知要預先提取的下一個物件。
- CDN 會進行預先提取和快取（CDN 預先提取部分）。 CDN 也必須有「智慧」，才能通知來源它是預先提取或一般的提取、處理404回應，以及避免無限的預先提取迴圈的方法。

### <a name="benefits"></a>優點

*原始協助 CDN 預先提取*功能的優點包括：

- 預先提取會在播放期間將預期的影片區段放在邊緣，以縮短檢視器的延遲，並改善影片區段下載時間，藉此改善影片播放品質。 這會導致影片啟動時間更快，而且 rebuffering 出現的次數較低。
- 這個概念適用于一般 CDN 來源案例，並不限於媒體。
- Akamai 已將這項功能新增至[Akamai Cloud Embed （ACE）](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)。

> [!NOTE]
> 這項功能尚未適用于與媒體服務串流端點整合的 Akamai CDN。 不過，它適用于具有既有 Akamai 合約，而且需要 Akamai CDN 與媒體服務來源之間自訂整合的媒體服務客戶。

### <a name="how-it-works"></a>運作方式

`Origin-Assist CDN-Prefetch`具有 AKAMAI cdn 直接合約的客戶可以使用對標頭的 CDN 支援（適用于即時和影片隨選串流）。 此功能牽涉到 Akamai CDN 與媒體服務來源之間的下列 HTTP 標頭交換：

|HTTP 標頭|值|傳送者|接收者|目的|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 0 (預設) 或 1 |CDN|來源|表示 CDN 已啟用預先提取。|
|`CDN-Origin-Assist-Prefetch-Path`| 範例： <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|來源|CDN|提供 CDN 的預先提取路徑。|
|`CDN-Origin-Assist-Prefetch-Request`|1 (預先提取要求) 或0 (一般要求)|CDN|來源|若要指出來自 CDN 的要求是預先提取。|

若想看部分標頭交換的實際情況，您可以嘗試下列步驟：

1. 使用 Postman 或捲曲對音訊或影片區段或片段的媒體服務原點發出要求。 請務必 `CDN-Origin-Assist-Prefetch-Enabled: 1` 在要求中新增標頭。
2. 在回應中，您應該會看到 `CDN-Origin-Assist-Prefetch-Path` 具有相對路徑的標頭作為其值。

### <a name="supported-streaming-protocols"></a>支援的串流通訊協定

此 `Origin-Assist CDN-Prefetch` 功能支援下列串流通訊協定來進行即時和隨選串流處理：

* HLS v3
* HLS v4
* HLS CMAF
* 破折號（CSF）
* 破折號（CMAF）
* Smooth Streaming

### <a name="faqs"></a>常見問題集

* 如果預先提取路徑 URL 無效，讓 CDN 預先提取取得404，該怎麼辦？

    CDN 只會快取404回應10秒（或其他已設定的值）。

* 假設您有隨選影片。 如果已啟用 CDN 預先提取，這項功能是否表示一旦用戶端要求第一個影片區段，預先提取將會啟動迴圈，以相同的位元速率預先提取所有後續的影片區段？

    否，CDN 預先提取只會在用戶端起始的要求/回應後完成。 預先提取不會觸發 CDN 預先提取，以避免預先提取迴圈。

* 是原點輔助 CDN 預先提取功能 always on？ 如何開啟/關閉該功能？

    這項功能預設為關閉。 客戶必須透過 Akamai API 將其開啟。

* 針對即時串流，如果下一個區段或片段尚未提供，原點會有何説明？

    在此情況下，媒體服務原始來源不會提供 `CDN-Origin-Assist-Prefetch-Path` 標頭，且不會進行 CDN 預先提取。

* 如何 `Origin-Assist CDN-Prefetch` 使用動態資訊清單篩選？

    這項功能的運作方式與資訊清單篩選器無關。 當下一個片段超出篩選視窗時，其 URL 仍然會藉由查看原始用戶端資訊清單，然後以 CDN 預先提取回應標頭的形式傳回。 因此，CDN 會取得從破折號/HLS/平滑資訊清單篩選出之片段的 URL。 不過，播放程式永遠不會向 CDN 提出 GET 要求來提取該片段，因為該片段不會包含在播放程式所持有的虛線/HLS/平滑資訊清單中（播放程式不知道片段是否存在）。

* 是否可以將 MPD/HLS 播放清單/平滑資訊清單自動提取？

    否、虛線 MPD、HLS 主要播放清單、HLS 變體播放清單，或流暢的資訊清單 URL 不會新增至預先提取標頭。

* 預先提取 Url 是相對或絕對的嗎？

    雖然 Akamai CDN 允許兩者，但媒體服務來源只會提供預先提取路徑的相對 Url，因為使用絕對 Url 並沒有明顯的好處。

* 這項功能是否可與受 DRM 保護的內容搭配使用？

    是，因為這項功能是在 HTTP 層級運作，所以不會解碼或剖析任何區段/片段。 這並不在意內容是否已加密。

* 這項功能是否適用伺服器端廣告插入（SSAI）？
    
    其適用于原始/主要內容（廣告插入前的原始影片內容）運作，因為 SSAI 不會變更來源內容從媒體服務來源的時間戳記。 此功能是否可與 ad 內容搭配使用，取決於 ad 源是否支援原始協助。 例如，如果 ad 內容也裝載在 Azure 媒體服務（相同或不同的來源），則 ad 內容也會被預先提取。

* 這項功能是否可與 UHD/HEVC 內容搭配使用？

    是。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* 請務必查看[串流端點（原始）](streaming-endpoint-concept.md)檔。
* [此存放庫中](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)的範例會示範如何使用 .NET 啟動預設的串流端點。
