---
title: Azure 媒體服務 v3 版本資訊 | Microsoft Docs
description: 為了讓您隨時掌握最新的開發訊息，本文提供 Azure 媒體服務 v3 最新資訊。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 05/28/2020
ms.author: juliako
ms.openlocfilehash: e072bcb0edc741b7843f470f14c3c37153338efb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647648"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure 媒體服務 v3 版本資訊

>將 URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` 複製並貼到 RSS 摘要閱讀程式中，以獲知何時該重新造訪此頁面來取得最新消息。

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="known-issues"></a>已知問題

> [!NOTE]
> 您可以使用 [Azure 入口網站](https://portal.azure.com/) 來管理 v3 [即時活動](live-events-outputs-concept.md)、查看 v3 [資產](assets-concept.md)、取得存取 API的相關資訊。 針對所有其他管理工作 (例如，轉換和作業)，請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

如需詳細資訊，請參閱[從媒體服務 v2 移至 v3 的移轉指導](migrate-from-v2-to-v3.md#known-issues)。

## <a name="may-2020"></a>2020 年 5 月

Azure 媒體服務現在於下列區域正式運作：德國北部、德國中西部、瑞士北部和瑞士西部。 客戶可以使用 Azure 入口網站，將媒體服務部署到這些區域。

## <a name="april-2020"></a>2020 年 4 月

### <a name="improvements-in-documentation"></a>文件改進

Azure 媒體播放器文件已遷移至 [Azure 文件](../azure-media-player/azure-media-player-overview.md)。

## <a name="january-2020"></a>2020 年 1 月

### <a name="improvements-in-media-processors"></a>媒體處理器改進

- 在影片分析中改善支援交錯式來源 – 這類內容現在會先正確去交錯，再傳送到推斷引擎。
- 在產生具有「最佳」模式的縮圖時，編碼器現在會搜尋超過 30 秒，以選取非單色的畫面格。

### <a name="azure-government-cloud-updates"></a>Azure Government 雲端最新消息

媒體服務已在下列 Azure Government 區域正式運作：「USGov 亞利桑那州」和「USGov 德克薩斯州」。

## <a name="december-2019"></a>2019 年 12 月

在即時和隨選視訊串流處理方面，新增對 *Origin-Assist Prefetch* 標頭的 CDN 支援；適用於直接簽訂 Akamai CDN 的客戶。 Origin-Assist CDN-Prefetch 功能涉及 Akamai CDN 與 Azure 媒體服務來源之間交換下列 HTTP 標頭：

|HTTP 標頭|值|傳送者|接收者|目的|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 0 (預設) 或 1 |CDN|來源|表示 CDN 已啟用預先提取|
|CDN-Origin-Assist-Prefetch-Path| 範例： <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|來源|CDN|提供 CDN 的預先提取路徑|
|CDN-Origin-Assist-Prefetch-Request|1 (預先提取要求) 或0 (一般要求)|CDN|來源|表示來自 CDN 的要求是預先提取|

若想看部分標頭交換的實際情況，您可以嘗試下列步驟：

1. 使用 Postman 或 curl 向媒體服務來源發出要求，以取得音訊或視訊區段或片段。 務必在要求中新增標頭 CDN-Origin-Assist-Prefetch-Enabled:1。
2. 在回應中，您應該會看到值為相對路徑的標頭 CDN-Origin-Assist-Prefetch-Path。

## <a name="november-2019"></a>2019 年 11 月

### <a name="live-transcription-preview"></a>即時轉譯預覽

現在，即時轉譯在美國西部 2 區域公開預覽並開放使用。

即時轉譯旨在以附加功能形式，結合即時事件一起使用。  在傳遞和標準或進階編碼即時事件上都支援此功能。  啟用這項功能時，此服務會使用認知服務的[語音轉換文字](../../cognitive-services/speech-service/speech-to-text.md)功能，將傳入的音訊中說出口的話轉譯成文字。 然後，在 MPEG-2 和 HLS 通訊協定中，此文字就能隨著視訊和音訊一起傳遞。 計費依據一個新的計量，這是即時事件處於「執行中」狀態時的額外成本。  如需即時轉譯和計費的詳細資訊，請參閱[即時轉譯](live-transcription.md)

> [!NOTE]
> 目前，即時轉譯在美國西部 2 區域只是預覽功能。 目前，僅支援轉譯以英文 (en-us) 說出口的話。

### <a name="content-protection"></a>內容保護

早在 9 月於有限區域發行的「權杖重送防護」功能，現已在所有區域推出。
媒體服務客戶現在可以限制以相同權杖來要求金鑰或授權的次數。 如需詳細資訊，請參閱[權杖重送防護](content-protection-overview.md#token-replay-prevention)。

### <a name="new-recommended-live-encoder-partners"></a>新建議的即時編碼器合作夥伴

針對 RTMP 即時串流，新增支援下列建議的新合作夥伴編碼器：

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 和 Max 運作相機](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>檔案編碼增強功能

- 現在提供新的「內容感知編碼」預設。 此預設使用內容感知編碼來產生一組符合 GOP 的 MP4。 針對任何輸入內容，此服務會對輸入內容執行初步簡單的分析。 然後根據這些結果，決定自適性串流傳遞時的最佳層數、適當位元速率及解析度設定。 此預設特別適用於中低複雜度的影片，雖然輸出檔案位元速率較低，但品質仍足以讓觀眾享受良好的體驗。 輸出會包含視訊和音訊交錯的 MP4 檔案。 如需詳細資訊，請參閱[開放式 API 規格](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)。
- 標準編碼器中已改善大小調整器的效能和多執行緒。 在特定情況下，客戶應該會發現 VOD 編碼的效能提升 5-40%。 編碼為多個位元速率的低複雜度內容效能提升最明顯。 
- 在 VOD 編碼期間，使用以時間為基礎的 GOP 設定時，針對可變畫面播放速率 (VFR) 內容，標準編碼現在會維持規律的 GOP 節奏。  這表示，如果客戶提交混合式畫面播放速率內容，例如在 15-30 fps 之間變化，現在應該會看到輸出到自適性位元速率串流 MP4 檔案時所計算的規律 GOP 距離。 這樣在透過 HLS 或 DASH 傳遞時能夠更順暢切換曲目。 
-  已改善可變畫面播放速率 (VFR) 來源內容的 AV 同步

### <a name="video-indexer-video-analytics"></a>影片索引器、影片分析

- 使用 VideoAnalyzer 預設所擷取的主要畫面格，現在為影片的原始解析度，而不調整大小。 擷取高解析度主要畫面格可提供原始品質影像，還可讓您利用 Microsoft 電腦視覺和自訂視覺服務提供的影像人工智慧模型，從影片中獲得更多見解。

## <a name="september-2019"></a>2019 年 9 月

###  <a name="media-services-v3"></a>媒體服務 v3  

#### <a name="live-linear-encoding-of-live-events"></a>即時事件的即時線性編碼

媒體服務 v3 宣佈開放預覽即時事件全年無休的即時線性編碼。

###  <a name="media-services-v2"></a>媒體服務 v2  

#### <a name="deprecation-of-media-processors"></a>取代媒體處理器

我們宣布取代「Azure 媒體索引器」和「Azure 媒體索引器 2 預覽版」。 關於淘汰日期，請參閱[舊版元件](../previous/legacy-components.md)主題。 [Azure 媒體服務影片索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)取代這些舊版媒體處理器。

如需詳細資訊，請參閱[從 Azure 媒體索引器和 Azure 媒體索引器 2 遷移到 Azure 媒體服務影片索引器](../previous/migrate-indexer-v1-v2.md)。

## <a name="august-2019"></a>2019 年 8 月

###  <a name="media-services-v3"></a>媒體服務 v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>開放一對南非區域使用媒體服務 

現在於南非北部和南非西部區域推出媒體服務。

如需詳細資訊，請參閱[媒體服務 v3 存在的雲端和區域](azure-clouds-regions.md)。

###  <a name="media-services-v2"></a>媒體服務 v2  

#### <a name="deprecation-of-media-processors"></a>取代媒體處理器

我們宣布取代逐漸淘汰的「Windows Azure 媒體編碼器」(WAME) 和「Azure 媒體編碼器」(AME) 媒體處理器。 關於淘汰日期，請參閱此[舊版元件](../previous/legacy-components.md)主題。

如需詳細資訊，請參閱[將 WAME 遷移到媒體編碼器標準](https://go.microsoft.com/fwlink/?LinkId=2101334)和[將 AME 遷移到媒體編碼器標準](https://go.microsoft.com/fwlink/?LinkId=2101335)。
 
## <a name="july-2019"></a>2019 年 7 月

### <a name="content-protection"></a>內容保護

當串流以權杖限制保護的內容時，終端使用者必須取得隨金鑰傳遞要求一起傳送的權杖。 「權杖重送防護」功能可讓媒體服務客戶限制以相同權杖來要求金鑰或授權的次數。 如需詳細資訊，請參閱[權杖重送防護](content-protection-overview.md#token-replay-prevention)。

從 7 月起，只在美國中部和美國西部推出此預覽功能。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-subclipping"></a>影片子剪輯

現在，進行影片編碼時，您可以使用[作業](https://docs.microsoft.com/rest/api/media/jobs)來修剪或子剪輯影片。 

這項功能適用於使用 [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 預設或 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 預設所建立的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。 

請參閱範例：

* [使用 .NET 子剪輯影片](subclip-video-dotnet-howto.md)
* [使用 REST 子剪輯影片](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019 年 5 月

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure 監視器對媒體服務診斷記錄和計量的支援

您現在可以使用 Azure 監視器來檢視媒體服務所發出的遙測資料。

* 使用 Azure 監視器診斷記錄來監視由媒體服務金鑰傳遞端點傳送的要求。 
* 監視由媒體服務[串流端點](streaming-endpoint-concept.md)發出的計量。   

如需詳細資訊，請參閱[監視媒體服務計量和診斷記錄](media-services-metrics-diagnostic-logs.md)。

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>動態封裝支援多個曲目 

當串流的資產有多個曲目及多個編解碼器和語言時，[動態封裝](dynamic-packaging-overview.md)現在於 HLS 輸出 (第 4 版或更高版本) 中支援多個曲目。

### <a name="korea-regional-pair-is-open-for-media-services"></a>開放一組韓國區域使用媒體服務 

現在於韓國中部和南韓南部區域推出媒體服務。 

如需詳細資訊，請參閱[媒體服務 v3 存在的雲端和區域](azure-clouds-regions.md)。

### <a name="performance-improvements"></a>效能改善

已新增的更新包括媒體服務效能改進。

* 已更新支援處理的檔案大小上限。 請參閱[配額和限制](limits-quotas-constraints.md)。
* [編碼速度改進](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)。

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-presets"></a>新的預設

* 內建的分析器預設中新增 [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)。
* 內建的編碼器預設中新增 [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。 如需詳細資訊，請參閱[內容感知編碼](content-aware-encoding.md)。 

## <a name="march-2019"></a>2019 年 3 月

動態封裝現在支援 Dolby Atmos。 如需詳細資訊，請參閱[動態封裝支援的音訊轉碼器](dynamic-packaging-overview.md#audio-codecs)。

您現在可以指定適用於串流定位器的資產或帳戶篩選器清單。 如需詳細資訊，請參閱[將篩選準則與串流定位器建立關聯](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="february-2019"></a>2019 年 2 月

Azure 國家雲端現在支援媒體服務 v3。 尚未在所有雲端中提供所有功能。 如需詳細資訊，請參閱[存在 Azure 媒體服務 v3 的雲端和區域](azure-clouds-regions.md)。

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) 事件已被新增至適用於媒體服務的 Azure 事件格線結構描述。

## <a name="january-2019"></a>2019 年 1 月

### <a name="media-encoder-standard-and-mpi-files"></a>媒體編碼器標準與 MPI 檔案 

使用「媒體編碼器標準」來編碼以產生 MP4 檔案時，會產生新的 .mpi 檔案並將該檔案新增到輸出資產。 此 MPI 檔案的目的是用來改進[動態封裝](dynamic-packaging-overview.md)與串流處理案例的效能。

您不應該修改或移除該 MPI 檔案，也不應該在您的服務中相依於此類檔案的存在與否。

## <a name="december-2018"></a>2018 年 12 月

來自 V3 API GA 版本的更新包括：
       
* **PresentationTimeRange** 屬性不再是**資產篩選**和**帳戶篩選**的「必要」項目。 
* **Jobs** 和 **Transforms** 的 $Top 和 $skip 查詢選項已移除，並新增了 $orderby。 在加入新的排序功能時，我們發現以前 $top 和 $skip 選項 (即使並未實作) 會意外公開。
* 已重新啟用列舉擴充性。 此功能已在 SDK 的預覽版本中啟用，但在 GA 版本中意外停用。
* 兩個預先定義的串流原則已重新命名。 **SecureStreaming** 現在重新命名為 **MultiDrmCencStreaming**。 **SecureStreamingWithFairPlay** 現在重新命名為 **Predefined_MultiDrmStreaming**。

## <a name="november-2018"></a>2018 年 11 月

CLI 2.0 模組現已適用於 [Azure 媒體服務 v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50。

### <a name="new-commands"></a>新的命令

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - 可讓您管理媒體保留單位。 如需詳細資訊，請參閱[調整媒體保留單位](media-reserved-units-cli-how-to.md)。

### <a name="new-features-and-breaking-changes"></a>新功能和重大變更

#### <a name="asset-commands"></a>資產命令

- 已新增 ```--storage-account``` 和 ```--container``` 引數。
- 已在 ```az ams asset get-sas-url``` 命令中新增到期時間 (現在 + 23 小時) 和權限 (讀取) 的預設值。

#### <a name="job-commands"></a>作業命令

- 已新增 ```--correlation-data``` 和 ```--label``` 引數
- ```--output-asset-names``` 已重新命名為 ```--output-assets```。 現在它會接受以空格分隔且格式為 'assetName=label' 的資產清單。 可以傳送沒有標籤的資產，例如：'assetName='。

#### <a name="streaming-locator-commands"></a>串流定位器命令

- ```az ams streaming locator``` 基底命令已取代為 ```az ams streaming-locator```。
- 已新增 ```--streaming-locator-id``` 和 ```--alternative-media-id support``` 引數。
- 已更新 ```--content-keys argument``` 引數。
- ```--content-policy-name``` 已重新命名為 ```--content-key-policy-name```。

#### <a name="streaming-policy-commands"></a>串流原則命令

- ```az ams streaming policy``` 基底命令已取代為 ```az ams streaming-policy```。
- 已在 ```az ams streaming-policy create``` 中新增加密參數支援。

#### <a name="transform-commands"></a>轉換命令

- ```--preset-names``` 引數已取代為 ```--preset```。 現在您一次只能設定 1 個輸出/預設 (以新增更多必須執行 ```az ams transform output add``` 的項目)。 此外，您可傳遞自訂 JSON 的路徑以設定自訂 StandardEncoderPreset。
- 傳遞要移除的輸出索引即可執行 ```az ams transform output remove```。
- 已在 ```az ams transform create``` 和 ```az ams transform output add``` 命令中新增 ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 引數。

## <a name="october-2018---ga"></a>2018 年 10 月 - GA

本節說明 Azure 媒體服務 (AMS) 的 10 月更新。

### <a name="rest-v3-ga-release"></a>REST v3 GA 版本

[REST v3 GA 版本](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) 包含更多 API，適用於即時、帳戶/資產層級資訊清單篩選器，以及 DRM 支援。

#### <a name="azure-resource-management"></a>Azure 資源管理 

Azure 資源管理支援可提供統一的管理和作業 API (現在所有項目都在單一位置)。

從這個版本開始，您可以使用 Resource Manager 範本來建立即時事件。

#### <a name="improvement-of-asset-operations"></a>改進資產作業 

引進了下列改進︰

- 從 HTTP(s) 的 URL 或 Azure Blob 儲存體 SAS URL 內嵌。
- 指定您自己資產的容器名稱。 
- 使用 Azure Functions 建立自訂工作流程時有更簡便的輸出支援。

#### <a name="new-transform-object"></a>新的轉換物件

新的**轉換**物件會簡化編碼模型。 新的物件可讓您輕鬆地建立及共用編碼 Resource Manager 範本和預設值。 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory 驗證和 RBAC

Azure AD 驗證和角色型存取控制 (RBAC) 會啟用 Azure AD 中的安全轉換、即時事件、內容金鑰原則，或是依角色或使用者的資產。

#### <a name="client-sdks"></a>用戶端 SDK  

媒體服務 v3 中支援的語言：.NET Core、Java、Node.js、Ruby、Typescript、Python、Go。

#### <a name="live-encoding-updates"></a>即時編碼更新

導入下列即時編碼更新：

- 新的即時低延遲模式 (10 秒端對端)。
- 改進的 RTMP 支援 (更高的穩定性及更多來源編碼器支援)。
- RTMPS 安全內嵌。

    當您建立即時事件時，現在會取得 4 個內嵌 URL。 4 個內嵌 URL 幾乎完全相同，並有相同的串流權杖 (AppId)，只有連接埠號碼部分不同。 其中兩個 URL 是 RTMPS 的主要部分和備份。 
- 24 小時制的轉碼支援。 
- 已改善透過 SCTE35 在 RTMP 中執行的廣告訊號支援。

#### <a name="improved-event-grid-support"></a>已改善事件方格支援

您可以看到下列的事件方格支援增強功能：

- 整合 Azure 事件方格，以更輕鬆地使用 Logic Apps 與 Azure Functions 進行開發。 
- 訂閱編碼、即時頻道等更多的事件。

### <a name="cmaf-support"></a>CMAF 支援

Apple HLS (iOS 11 +) 的 CMAF 和 'cbcs' 加密支援和支援 CMAF 的 MPEG-DASH 播放程式。

### <a name="video-indexer"></a>影片索引子

我們已在 8 月發表了影片索引器 GA 版本。 如需目前支援功能的全新詳細資訊，請參閱[什麼是影片索引器](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)。 

### <a name="plans-for-changes"></a>方案變更

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Azure CLI 2.0 模組即將推出，內含所有功能的操作 (包括即時、內容金鑰原則、帳戶/資產篩選器、串流原則)。 

### <a name="known-issues"></a>已知問題

只有使用適用於資產或帳戶篩選器預覽 API 的客戶會受到下列問題影響。

如果您在 09/28 到 10/12 之間使用媒體服務 v3 CLI 或 API 來建立資產或帳戶篩選器，由於版本衝突，您必須移除所有的資產和帳戶篩選器並加以重新建立。 

## <a name="may-2018---preview"></a>2018 年 5 月 - 預覽

### <a name="net-sdk"></a>.NET SDK

.NET SDK 中推出下列功能：

* **轉換**和**工作**，可編碼或分析媒體內容。 如需範例，請參閱[串流處理檔案](stream-files-tutorial-with-api.md)和[分析](analyze-videos-tutorial-with-api.md)。
* **串流定位器**，用於將內容發佈及串流處理到終端使用者裝置
* **串流原則**和**內容金鑰原則**，可在傳遞內容時設定金鑰傳遞和內容保護 (DRM)。
* **即時事件**和**即時輸出**，可設定內嵌和封存即時串流內容。
* **資產**，可在 Azure 儲存體中儲存及發佈媒體內容。 
* **串流端點**，可設定和擴展動態封裝、加密和串流處理即時與點播媒體內容。

### <a name="known-issues"></a>已知問題

* 提交工作時，您可以使用 HTTPS URL、SAS URL 或位於 Azure Blob 儲存體中檔案的路徑來指定內嵌您的來源影片。 目前，AMS v3 不支援透過 HTTPS URL 的區塊傳送編碼。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

- [概觀](media-services-overview.md)
- [媒體服務 v3 文件更新](docs-release-notes.md)
- [媒體服務 v2 版本資訊](../previous/media-services-release-notes.md)
