---
title: 使用 CDN 整合來串流內容
titleSuffix: Azure Media Services
description: 瞭解如何使用 CDN 整合來串流內容，以及預先提取和來源輔助 CDN。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: inhenkel
ms.openlocfilehash: abf4b8dffc69cfee9332d18e59d0a2852fa7617e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226143"
---
# <a name="stream-content-with-cdn-integration"></a>使用 CDN 整合來串流內容

Azure 內容傳遞網路 (CDN) 為開發人員提供一套全域解決方案，讓他們可藉由在策略性分布於全球的實體節點上快取內容，將高頻寬內容迅速傳遞給使用者。  

CDN 會快取從媒體服務 [串流端點串流處理的內容 (來源) ](streaming-endpoint-concept.md) 每個編解碼器、每個串流通訊協定、每一位容器格式，以及每一加密/DRM。 針對每個編解碼器串流處理通訊協定-容器格式-位元速率加密的組合，會有個別的 CDN 快取。

只要快取影片片段，常用的內容就會直接從 CDN 快取中提供。 快取實況內容的機率很高，因為通常會有許多人觀看一模一樣的內容。 隨選內容可能有點棘手，因為您可能會有一些熱門的內容，而且有些不是。 如果您有數百萬個影片資產，其中沒有熱門的 (只有一或兩個檢視器) 但是您有數千人觀賞所有不同的影片，則 CDN 會變得較不有效。

您也需要考量彈性資料流的運作方式。 每個個別的影片片段都會快取為它自己的實體。 例如，想像一下第一次觀看特定影片的時間。 如果檢視器略過只觀賞幾秒鐘的時間，而且只有與監看過的人相關的影片片段會在 CDN 中快取。 因為有彈性資料流，您通常會有 5 到 7 個位元速率不同的視訊。 如果有一位人監看一個位元速率，而另一個人監看不同的位元速率，則它們會分別在 CDN 中快取。 即使兩人監看相同的位元速率，也可以透過不同的通訊協定進行串流處理。 每個通訊協定 (HLS、MPEG DASH、Smooth Streaming) 會分別進行快取。 因此，每一個位元速率和通訊協定都會分別進行快取，而且只會快取已要求的那些視訊片段。

除了測試環境以外，我們建議針對標準和高階串流端點啟用 CDN。 每種類型的串流端點都有不同的支援輸送量限制。
很難精確地計算串流端點所支援的並行資料流程數目上限，因為有許多因素需要考慮。 其中包含：

- 用於串流處理的位元速率上限
- 播放的前置緩衝區和切換行為。 播放程式會嘗試從原點高載區段，並使用載入速度來計算自動調整位元速率切換。 如果串流端點接近飽和，回應時間可能會不同，而玩家會開始切換為較低的品質。 因為這會減少串流端點播放程式的負載，所以請將建立不需要的切換觸發程式調整回更高的品質。
整體來說，您可以藉由採用最大的串流端點輸送量，並將此值除以最大位元速率，以安全地預估並行資料流程的最大值， (假設所有玩家都使用最高的位元速率。 ) 例如，您可以有標準串流端點，其限制為 600 Mbps 和3Mbp 的最高位元速率。 在此情況下，最高的位元速率支援大約200的並行資料流程。 也請務必考慮音訊頻寬需求。 雖然音訊串流可能只會在128的 kps 串流，但是當您將它乘以並行資料流程的數目時，總串流會快速增加。

本主題討論如何啟用 [CDN 整合](#enable-azure-cdn-integration)。 此外也會說明預先提取 (主動式快取) 和 [輔助的 CDN 預先提取](#origin-assist-cdn-prefetch) 概念。

## <a name="considerations"></a>考量

- [streaming endpoint](streaming-endpoint-concept.md) `hostname` 無論您是否啟用 CDN，串流端點和串流 URL 都保持不變。
- 如果您需要使用或不使用 CDN 測試內容的能力，請建立另一個未啟用 CDN 的串流端點。

## <a name="enable-azure-cdn-integration"></a>啟用 Azure CDN 整合

> [!IMPORTANT]
> 您無法為試用版或學生 Azure 帳戶啟用 CDN。
>
> 所有 Azure 資料中心都會啟用 CDN 整合，但美國聯邦政府和中國區域除外。

布建啟用 CDN 的串流端點之後，在完成 DNS 更新以將串流端點對應至 CDN 端點之前，媒體服務上會有已定義的等候時間。

如果您稍後想要停用/啟用 CDN，串流端點必須處於**已停止**狀態。 串流端點啟動後，最多可能需要兩個小時的時間，才能啟用 Azure CDN 整合，並讓所有 CDN Pop 的變更都處於作用中狀態。 不過，您可以啟動串流端點，並在不中斷串流端點的情況下串流處理。 整合完成後，就會從 CDN 傳遞串流。 在布建期間，您的串流端點會處於 **啟動** 狀態，而您可能會發現效能降低。

建立標準串流端點時，預設會使用標準 Verizon 來設定它。 您可以使用 REST Api 來設定 Premium Verizon 或標準 Akamai 提供者。

如果是標準串流端點，Azure 媒體服務與 Azure CDN 的整合是在**來自 Verizon 的 Azure CDN** 上實作。 您可以使用所有 **Azure CDN 定價層和提供者**來設定進階串流端點。

> [!NOTE]
> 如需 Azure CDN 的詳細資訊，請參閱 [CDN 總覽](../../cdn/cdn-overview.md)。

## <a name="determine-if-a-dns-change-was-made"></a>判斷是否已進行 DNS 變更

您可以判斷是否已在串流端點上進行 DNS 變更， (使用將流量導向至 Azure CDN) <https://www.digwebinterface.com> 。 如果您在結果中看到 azureedge.net 的功能變數名稱，流量現在會指向 CDN。

## <a name="origin-assist-cdn-prefetch"></a>原點輔助 CDN-預先提取

CDN 快取是一個回應的程式。 如果 CDN 可以預測下一個物件將被要求的物件，CDN 可以主動要求並快取下一個物件。 透過此程式，您可以針對所有 (或大部分的物件) 達到快取點擊，以改善效能。

預先提取的概念會致力於將物件放在「網際網路邊緣」上，以預期這些物件將會由播放程式即將要求，進而減少將該物件傳遞給播放程式的時間。

為了達成此目標， (原始) 的串流端點，且 CDN 需要以數種方式進行手入：

- 媒體服務原點必須有「智慧」 (源-協助) 通知 CDN 下一個要預先提取的物件。
- CDN 會執行預先提取和快取 (CDN 預先提取部分) 。 CDN 也需要「智慧」來通知來源是否為預先提取或一般的提取、處理404回應，以及避免無限的預先提取迴圈的方法。

### <a name="benefits"></a>優點

「 *來源協助工具」 CDN 預先提取* 功能的優點包括：

- 預先提取可透過在播放期間將預期的影片區段定位、減少檢視器的延遲，以及改善影片區段下載時間，來改善影片播放品質。 這可讓影片的啟動時間更快，並減少 rebuffering 的出現次數。
- 此概念適用于一般的 CDN 來源案例，不限於媒體。
- Akamai 已將此功能新增至 [Akamai Cloud Embed (ACE) ](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)。

> [!NOTE]
> 這項功能尚未適用于與媒體服務串流端點整合的 Akamai CDN。 不過，它適用于具有預先存在 Akamai 合約，且需要 Akamai CDN 與媒體服務來源之間的自訂整合的媒體服務客戶。

### <a name="how-it-works"></a>運作方式

適用于 `Origin-Assist CDN-Prefetch` 即時和隨選串流處理) 的 (的 CDN 支援可供具有 AKAMAI CDN 直接合約的客戶使用。 此功能牽涉到 Akamai CDN 和媒體服務來源之間的下列 HTTP 標頭交換：

|HTTP 標頭|值|傳送者|接收者|目的|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 0 (預設) 或 1 |CDN|來源|表示 CDN 已啟用預先提取。|
|`CDN-Origin-Assist-Prefetch-Path`| 範例： <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|來源|CDN|提供預先提取的 CDN 路徑。|
|`CDN-Origin-Assist-Prefetch-Request`|1 (預先提取要求) 或0 (一般要求)|CDN|來源|表示來自 CDN 的要求是預先提取的。|

若想看部分標頭交換的實際情況，您可以嘗試下列步驟：

1. 使用 Postman 或捲曲對音訊或影片區段或片段的媒體服務來源發出要求。 請務必 `CDN-Origin-Assist-Prefetch-Enabled: 1` 在要求中新增標頭。
2. 在回應中，您應該會看到標頭的 `CDN-Origin-Assist-Prefetch-Path` 相對路徑為其值。

### <a name="supported-streaming-protocols"></a>支援的串流通訊協定

此 `Origin-Assist CDN-Prefetch` 功能支援即時和隨選串流處理的下列串流通訊協定：

* HLS v3
* HLS v4
* HLS CMAF
* 虛線 (CSF) 
* 虛線 (CMAF) 
* Smooth Streaming

### <a name="faqs"></a>常見問題集

* 如果預先提取路徑 URL 無效，讓 CDN 預先提取取得404，該怎麼辦？

    CDN 只會快取10秒的404回應 (或其他設定的值) 。

* 假設您有隨選影片。 如果啟用 CDN，這項功能是否意指用戶端要求第一個影片區段之後，預先提取會開始以相同位元速率預先提取所有後續的影片區段？

    否，CDN 預先提取只會在用戶端起始的要求/回應之後完成。 CDN 絕對不會由預先提取觸發，以避免預先提取迴圈。

* 是原點輔助的 CDN-預先提取功能永遠開啟嗎？ 如何開啟/關閉？

    這項功能預設為關閉。 客戶必須透過 Akamai API 將它開啟。

* 針對即時串流，如果下一個區段或片段還無法使用，則會發生什麼事？

    在此情況下，媒體服務來源不會提供 `CDN-Origin-Assist-Prefetch-Path` 標頭，也不會進行 CDN 預先提取。

* `Origin-Assist CDN-Prefetch`動態資訊清單篩選器的運作方式為何？

    這項功能與資訊清單篩選器無關。 當下一個片段不在篩選視窗中時，它的 URL 仍會藉由查看原始用戶端資訊清單，然後以 CDN 預先提取回應標頭的形式來找到。 因此 CDN 會取得從破折號/HLS/平滑資訊清單篩選出的片段 URL。 不過，播放程式永遠不會對 CDN 提出 GET 要求來提取該片段，因為該片段不包含在播放程式所持有的破折號/HLS/平滑資訊清單中 (播放程式並不知道片段的存在) 。

* 是否可以將 MPD/HLS 播放清單/平滑資訊清單預先提取？

    否，破折號 MPD、HLS 主要播放清單、HLS 變異播放清單或流暢的資訊清單 URL 不會新增至預先提取標頭。

* 預先提取 Url 為相對或絕對？

    雖然 Akamai CDN 允許這兩種方式，但是媒體服務來源只會提供預先提取路徑的相對 Url，因為使用絕對 Url 並沒有明顯的優點。

* 這項功能是否適用于受 DRM 保護的內容？

    是，因為這項功能是在 HTTP 層級運作，所以它不會解碼或剖析任何區段/片段。 這並不在意內容是否經過加密。

* 這項功能是否適用于 (SSAI) 的伺服器端 Ad 插入？
    
    它會在 ad 插入) 運作之前， (原始影片內容的原始/主要內容，因為 SSAI 不會變更媒體服務來源中來源內容的時間戳記。 這項功能是否適用于 ad 內容，取決於 ad 來源是否支援原點輔助。 例如，如果 ad 內容也裝載于 Azure 媒體服務 (相同或不同的原始) ，則 ad 內容也會被預先提取。

* 這項功能是否適用于 UHD/HEVC 內容？

    是。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* 請務必審視 [ (原始) 檔的串流端點 ](streaming-endpoint-concept.md) 。
* [此存放庫中](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)的範例會示範如何使用 .NET 啟動預設的串流端點。
