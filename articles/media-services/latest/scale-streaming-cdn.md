---
title: 通過 CDN 集成資料流內容
titleSuffix: Azure Media Services
description: 瞭解使用 CDN 集成資料流內容，以及預取和源輔助 CDN 預取。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128060"
---
# <a name="stream-content-with-cdn-integration"></a>通過 CDN 集成資料流內容

Azure 內容傳遞網路 (CDN) 為開發人員提供一套全域解決方案，讓他們可藉由在策略性分布於全球的實體節點上快取內容，將高頻寬內容迅速傳遞給使用者。  

CDN 緩存從媒體服務[流終結點（源）](streaming-endpoint-concept.md)資料流的內容，每個編解碼器、每個流式處理協定、每個位元速率、每個容器格式和每個加密/DRM。 對於編解碼器流協定-容器格式-位元速率加密的每個組合，將有一個單獨的 CDN 緩存。

只要緩存視頻片段，熱門內容就會直接從 CDN 緩存中提供。 快取實況內容的機率很高，因為通常會有許多人觀看一模一樣的內容。 點播內容可能有點棘手，因為您可能有一些受歡迎的內容，有些內容不是。 如果你有數以百萬計的視頻資產，其中沒有一個是受歡迎的（只有一個或兩個觀眾一個星期），但你有數千人觀看所有不同的視頻，CDN變得不那麼有效。

您也需要考量彈性資料流的運作方式。 每個單獨的視頻片段都緩存為自己的實體。 例如，想像一下第一次觀看特定視頻。 如果觀看者跳過只在這裡和那裡觀看幾秒鐘，則只有與觀看者關聯的視頻片段才會在 CDN 中緩存。 因為有彈性資料流，您通常會有 5 到 7 個位元速率不同的視訊。 如果一個人正在觀察一個位元速率，而另一個人正在觀察不同的位元速率，則他們每人都在 CDN 中單獨緩存。 即使兩個人正在觀看相同的位元速率，他們也可能通過不同的協定進行資料流。 每個通訊協定 (HLS、MPEG DASH、Smooth Streaming) 會分別進行快取。 因此，每一個位元速率和通訊協定都會分別進行快取，而且只會快取已要求的那些視訊片段。

在決定是否在媒體服務[流終結點](streaming-endpoint-concept.md)上啟用 CDN 時，請考慮預期的觀看者數量。 CDN 僅在您期待許多內容的觀看者時才有所説明。 如果檢視器的最大併發小於 500，建議禁用 CDN，因為 CDN 與併發性一起最大比例。

本主題討論啟用[CDN 集成](#enable-azure-cdn-integration)。 它還解釋了預取（活動緩存）和[源輔助 CDN 預取](#origin-assist-cdn-prefetch)概念。

## <a name="considerations"></a>考量

* 無論是否啟用 CDN，[流式處理終結點](streaming-endpoint-concept.md)`hostname`和流式處理 URL 都保持不變。
* 如果需要使用 CDN 或不使用 CDN 測試內容的能力，請創建另一個未啟用 CDN 的流式處理終結點。

## <a name="enable-azure-cdn-integration"></a>啟用 Azure CDN 整合

> [!IMPORTANT]
> 不能為試用版或學生 Azure 帳戶啟用 CDN。
>
> 除聯邦政府和中國區域外，所有 Azure 資料中心都啟用 CDN 集成。

在啟用了 CDN 後預配流式處理終結點後，在進行 DNS 更新將流式處理終結點映射到 CDN 終結點之前，媒體服務上有定義的等待時間。

如果您稍後想要停用/啟用 CDN，串流端點必須處於**已停止**狀態。 可能需要將近 2 小時，Azure CDN 整合才會啟用，變更也才會遍及所有 CDN POP。 但是，您可以啟動流式處理終結點和流，而不會中斷流式處理終結點。 集成完成後，流將從 CDN 傳遞。 在預配期間，流式處理終結點將處於**啟動**狀態，您可能會觀察到性能下降。

創建標準流式處理終結點時，預設情況下使用標準 Verizon 配置它。 您可以使用 REST API 配置高級 Verizon 或標準 Akamai 供應商。

如果是標準串流端點，Azure 媒體服務與 Azure CDN 的整合是在**來自 Verizon 的 Azure CDN** 上實作。 您可以使用所有 **Azure CDN 定價層和提供者**來設定進階串流端點。

> [!NOTE]
> 有關 Azure CDN 的詳細資訊，請參閱[CDN 概述](../../cdn/cdn-overview.md)。

## <a name="determine-if-a-dns-change-was-made"></a>確定是否進行了 DNS 更改

通過使用，可以確定 DNS 更改是否在流式處理終結點上（流量正定向到 Azure CDN）。 <https://www.digwebinterface.com> 如果在結果中看到azureedge.net個功能變數名稱，則流量現在指向 CDN。

## <a name="origin-assist-cdn-prefetch"></a>原點輔助 CDN 預取

CDN 緩存是一個反應性過程。 如果 CDN 可以預測將請求哪些物件，CDN 可以主動請求和緩存下一個物件。 通過此過程，您可以對所有（或大多數）物件實現緩存命中，從而提高性能。

預取的概念力求將物件定位在"互聯網的邊緣"，以預期玩家會立即要求這些物件，從而減少將物件交付給玩家的時間。

為了實現這一目標，流式處理終結點（原點）和 CDN 需要以多種方式攜手工作：

- 媒體服務源需要具有"智慧"（原始輔助）來通知 CDN 下一個要預取的物件。
- CDN 執行預提取和緩存（CDN 預取部分）。 CDN 還需要有"智慧"來通知來源，無論是預取還是常規提取，處理 404 回應，以及避免無休止的預取迴圈的方法。

### <a name="benefits"></a>優點

*原點輔助 CDN 預取*功能的優點包括：

- Prefetch 通過在播放期間預先將預期視頻段放置在邊緣，減少觀看者延遲，並縮短視頻段下載時間，從而提高視頻播放品質。 這樣可以縮短視頻啟動時間並減少重新緩衝事件。
- 此概念適用于常規 CDN 源方案，並不限於介質。
- Akamai 已將此功能添加到[Akamai 雲嵌入 （ACE）](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)中。

> [!NOTE]
> 此功能還不適用於與媒體服務流終結點集成的 Akamai CDN。 但是，它適用于具有預先存在的 Akamai 合同且需要在 Akamai CDN 和媒體服務來源之間進行自訂集成的媒體服務客戶。

### <a name="how-it-works"></a>運作方式

CDN 對`Origin-Assist CDN-Prefetch`標頭的支援（用於即時和視頻點播流）可供與 Akamai CDN 有直接合同的客戶使用。 此功能涉及 Akamai CDN 和媒體服務源之間的以下 HTTP 標頭交換：

|HTTP 標頭|值|傳送者|接收者|目的|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1（預設）或 0 |CDN|來源|指示 CDN 已啟用預取。|
|`CDN-Origin-Assist-Prefetch-Path`| 範例： <br/>片段（視頻=1400000000，格式=mpd-time-cmaf）|來源|CDN|為 CDN 提供預取路徑。|
|`CDN-Origin-Assist-Prefetch-Request`|1（預取請求）或 0（常規請求）|CDN|來源|指示來自 CDN 的請求是預取。|

要查看標頭交換的一部分，可以嘗試以下步驟：

1. 使用 Postman 或 cURL 向媒體服務源發出音訊或視頻段或片段的請求。 請確保在請求中添加標頭`CDN-Origin-Assist-Prefetch-Enabled: 1`。
2. 在回應中，應看到具有相對路徑`CDN-Origin-Assist-Prefetch-Path`的標頭的值。

### <a name="supported-streaming-protocols"></a>支援的流式處理協定

該`Origin-Assist CDN-Prefetch`功能支援以下即時和按需流式處理的流協定：

* HLS v3
* HLS v4
* HLS CMAF
* DASH （CSF）
* 破折號 （CMAF）
* Smooth Streaming

### <a name="faqs"></a>常見問題集

* 如果預取路徑 URL 無效，使 CDN 預取獲取 404，該怎麼辦？

    CDN 將僅緩存 404 回應 10 秒（或其他已配置的值）。

* 假設您有一個點播視頻。 如果啟用了 CDN 預取，此功能是否意味著一旦用戶端請求第一個視頻段，預取將啟動迴圈以相同的位元速率預取所有後續視頻段？

    否，CDN 預獲取僅在用戶端啟動的請求/回應後完成。 CDN 預取永遠不會由預取觸發，以避免預取迴圈。

* 原點輔助 CDN 預提取功能是否始終處於打開狀態？ 如何打開/關閉它？

    這項功能預設為關閉。 客戶需要通過 Akamai API 將其打開。

* 對於即時流式處理，如果下一個分段或片段不可用，源輔助系統會發生什麼情況？

    在這種情況下，媒體服務源不會提供`CDN-Origin-Assist-Prefetch-Path`標頭，並且不會發生 CDN 預獲取。

* `Origin-Assist CDN-Prefetch`如何使用動態清單篩選器？

    此功能獨立于清單篩選器工作。 當下一個片段從篩選器視窗退出時，其 URL 仍將通過查看原始用戶端清單而找到，然後作為 CDN 預取回應標頭返回。 因此，CDN 將獲得從 DASH/HLS/平滑清單中篩選出的片段的 URL。 但是，玩家永遠不會向 CDN 發出 GET 請求以獲取該片段，因為該片段不包括在玩家持有的 DASH/HLS/平滑清單中（玩家不知道該片段的存在）。

* DASH MPD/HLS 播放清單/平滑清單是否可以預接？

    否，DASH MPD、HLS 主播放清單、HLS 變體播放清單或平滑清單 URL 不會添加到預取標頭。

* 預取 URL 是相對的還是絕對的？

    雖然 Akamai CDN 允許這兩種 URL，但媒體服務源僅為預取路徑提供相對 URL，因為使用絕對 URL 沒有明顯的好處。

* 此功能是否與受 DRM 保護的內容配合使用？

    是的，由於此功能在 HTTP 級別工作，因此它不會解碼或解析任何段/片段。 它不關心內容是否加密。

* 此功能是否與伺服器端廣告插入 （SSAI） 配合使用？
    
    它適用于原始/主要內容（在廣告插入前的原始視頻內容）有效，因為 SSAI 不會更改媒體服務來源源內容的時間戳記。 此功能是否與廣告內容有效，取決於廣告來源是否支援"來源-輔助"。 例如，如果廣告內容也託管在 Azure 媒體服務（相同或單獨的來源），廣告內容也將預占。

* 此功能是否與 UHD/HEVC 內容配合使用？

    是。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* 請確保查看[流式處理終結點（源）](streaming-endpoint-concept.md)文檔。
* [此存放庫中](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)的範例會示範如何使用 .NET 啟動預設的串流端點。
