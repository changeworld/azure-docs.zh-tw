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
ms.date: 12/13/2019
ms.author: juliako
ms.openlocfilehash: 52d8dda8b543e5bdf3ca88ae3784df65be3a2ba1
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962938"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure 媒體服務 v3 版本資訊

>藉由複製並貼上此 URL，取得何時要重新流覽此頁面以進行更新的通知： `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` 至您的 RSS 摘要讀取器。

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="known-issues"></a>已知問題

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-sdk)、CLI 或其中一個支援的 SDK。

如需詳細資訊，請參閱[從媒體服務 v2 移至 v3 的移轉指導](migrate-from-v2-to-v3.md#known-issues)。

## <a name="january-2020"></a>2020 年 1 月

### <a name="improvements-in-media-processors"></a>媒體處理器的改良功能

- 改善影片分析中交錯式來源的支援–這類內容現在已正確地解除交錯，然後才傳送到推斷引擎。
- 當產生具有「最佳」模式的縮圖時，編碼器現在會搜尋超過30秒，以選取未單色的畫面格。
 
## <a name="november-2019"></a>2019 年 11 月

### <a name="live-transcription-preview"></a>即時轉譯預覽

即時轉譯現在處於公開預覽狀態，並可在美國西部2區域中使用。

即時轉譯是設計來與即時事件搭配使用，做為附加元件功能。  傳遞和標準或高階編碼即時事件皆可支援。  啟用這項功能時，服務會使用認知服務的[語音轉換文字](../../cognitive-services/speech-service/speech-to-text.md)功能，將傳入音訊中的單字轉譯成文字。 然後，這段文字會提供給傳遞，以及以 MPEG-2 和 HLS 通訊協定進行的影片和音訊。 計費是以新的附加元件計量為基礎，當即時事件處於「執行中」狀態時，這會額外收費。  如需即時轉譯和計費的詳細資訊，請參閱[即時](live-transcription.md)轉譯

> [!NOTE]
> 目前，即時轉譯僅以美國西部2區域中的預覽功能形式提供。 它目前僅支援英文（en-us）的口頭文字轉譯。

### <a name="content-protection"></a>內容保護

在9月的有限區域中發行的權杖重新執行*防護*功能現在已在所有區域推出。
媒體服務客戶現在可以設定相同權杖可用來要求金鑰或授權的次數限制。 如需詳細資訊，請參閱權杖重新執行[防護](content-protection-overview.md#token-replay-prevention)。

### <a name="new-recommended-live-encoder-partners"></a>新建議的即時編碼器合作夥伴

已針對 RTMP 即時串流新增下列建議的新合作夥伴編碼器支援：

- [Cambria Live 4。3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 和最大動作攝影機](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>檔案編碼增強功能

- 現在提供新的內容感知編碼預設值。 它會使用內容感知編碼來產生一組對齊 GOP 的 Mp4。 針對任何輸入內容，此服務會執行輸入內容的初始輕量分析。 它會使用這些結果來判斷以彈性串流傳遞的最佳層級、適當的位元速率和解析度設定。 此預設值特別適用于低複雜度和中等複雜度的影片，其中輸出檔案是以較低的位元速率，但品質仍然可以讓檢視器獲得良好的體驗。 輸出會包含具有影片和音訊交錯的有案檔案。 如需詳細資訊，請參閱[OPEN API 規格](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)。
- 改善媒體編碼器標準中重新 sizer 的效能和多執行緒處理。 在特定情況下，客戶應該會看到 5-40% VOD 編碼之間的效能提升。 以多個位元速率編碼的低複雜性內容將會看到最高效能的增加。 
- 標準編碼現在會在使用以時間為基礎的 GOP 設定時，為 VOD 編碼期間的變數畫面播放速率（VFR）內容保留一般的 GOP 節奏。  這表示客戶提交混合的畫面播放速率內容，會因 15-30 fps 而有所不同。例如，現在應該會看到輸出到彈性位元速率串流的檔案時，計算出一般的 GOP 距離。 這可改善在傳遞 HLS 或虛線時，在曲目之間順暢切換的能力。 
-  改善可變畫面播放速率（VFR）來源內容的 AV 同步處理

### <a name="video-indexer-video-analytics"></a>影片索引子，影片分析

- 使用 VideoAnalyzer 預設值解壓縮的主要畫面格，現在會在影片的原始解析度中，而不是調整大小。 高解析度的主要畫面格會提供原始品質影像，並可讓您使用 Microsoft 電腦視覺所提供的影像型人工智慧模型，並自訂視覺服務，從您的影片取得更多見解。

## <a name="september-2019"></a>2019 年 9 月

###  <a name="media-services-v3"></a>媒體服務 v3  

#### <a name="live-linear-encoding-of-live-events"></a>即時事件的即時線性編碼

媒體服務 v3 宣佈即時事件即時線性編碼24小時 x 365 天的預覽。

###  <a name="media-services-v2"></a>媒體服務 v2  

#### <a name="deprecation-of-media-processors"></a>淘汰媒體處理器

我們宣佈*Azure 媒體索引子*和*Azure 媒體索引子 2 Preview*已淘汰。 如需停用日期，請參閱此[舊版元件](../previous/legacy-components.md)主題。 [Azure 媒體服務影片索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)會取代這些舊版媒體處理器。

如需詳細資訊，請參閱[從 Azure 媒體索引子遷移和 Azure 媒體索引子2，到 Azure 媒體服務影片索引子](../previous/migrate-indexer-v1-v2.md)。

## <a name="august-2019"></a>2019 年 8 月

###  <a name="media-services-v3"></a>媒體服務 v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>南非地區配對已開放媒體服務 

媒體服務現在可以在南非北部和南非西部區域中使用。

如需詳細資訊，請參閱[媒體服務 v3 所在的雲端和區域](azure-clouds-regions.md)。

###  <a name="media-services-v2"></a>媒體服務 v2  

#### <a name="deprecation-of-media-processors"></a>淘汰媒體處理器

我們宣佈淘汰*Windows Azure 媒體編碼器*（WAME）和*Azure 媒體編碼器*（AME）媒體處理器，這是即將淘汰的。 如需停用日期，請參閱此[舊版元件](../previous/legacy-components.md)主題。

如需詳細資訊，請參閱[將 WAME 遷移至媒體編碼器標準](https://go.microsoft.com/fwlink/?LinkId=2101334)並[將 AME 遷移至媒體編碼器標準](https://go.microsoft.com/fwlink/?LinkId=2101335)。
 
## <a name="july-2019"></a>2019 年 7 月

### <a name="content-protection"></a>內容保護

當串流以權杖限制保護的內容時，終端使用者必須取得在金鑰傳遞要求中傳送的權杖。 「*權杖*重新執行防護」功能可讓媒體服務客戶設定相同權杖可用來要求金鑰或授權的次數限制。 如需詳細資訊，請參閱權杖重新執行[防護](content-protection-overview.md#token-replay-prevention)。

從7月起，預覽功能僅適用于美國中部和美國西部。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-subclipping"></a>影片子剪輯

您現在可以在使用[作業](https://docs.microsoft.com/rest/api/media/jobs)進行編碼時，修剪或子剪輯影片。 

這項功能適用于使用 [ [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ] 預設值或 [ [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) ] 預設建立的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。 

請參閱範例：

* [使用 .NET 子剪輯影片](subclip-video-dotnet-howto.md)
* [使用 REST 子剪輯影片](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019 年 5 月

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure 監視器媒體服務診斷記錄和計量的支援

您現在可以使用 Azure 監視器來查看媒體服務所發出的遙測資料。

* 使用 Azure 監視器診斷記錄來監視由媒體服務金鑰傳遞端點傳送的要求。 
* 監視媒體服務[串流端點](streaming-endpoint-concept.md)所發出的計量。   

如需詳細資訊，請參閱[監視媒體服務計量和診斷記錄](media-services-metrics-diagnostic-logs.md)。

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>動態封裝中的多音訊追蹤支援 

當串流具有多個編解碼器和語言的多個音訊追蹤的資產時，[動態封裝](dynamic-packaging-overview.md)現在支援 HLS 輸出（第4版或更高版本）的多個音軌。

### <a name="korea-regional-pair-is-open-for-media-services"></a>韓國地區配對已開放供媒體服務 

媒體服務現于韓國中部和南韓南部地區提供。 

如需詳細資訊，請參閱[媒體服務 v3 所在的雲端和區域](azure-clouds-regions.md)。

### <a name="performance-improvements"></a>效能改善

已新增包含媒體服務效能改進的更新。

* 已更新支援處理的檔案大小上限。 請參閱[配額和限制](limits-quotas-constraints.md)。
* [編碼速度改進](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)。

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-presets"></a>新的預設

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)已新增至內建分析器預設值。
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)已新增至內建的編碼器預設值。 如需詳細資訊，請參閱[內容感知編碼](content-aware-encoding.md)。 

## <a name="march-2019"></a>2019 年 3 月

動態封裝現在支援杜比 Atmos。 如需詳細資訊，請參閱[動態封裝所支援的音訊編解碼器](dynamic-packaging-overview.md#audio-codecs)。

您現在可以指定資產或帳戶篩選器的清單，其適用于您的串流定位器。 如需詳細資訊，請參閱[將篩選與串流定位器產生關聯](filters-concept.md#associating-filters-with-streaming-locator)。

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

### <a name="video-indexer"></a>影片索引器

我們已在 8 月發表了影片索引器 GA 版本。 如需目前支援功能的全新詳細資訊，請參閱[什麼是影片索引器](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)。 

### <a name="plans-for-changes"></a>方案變更

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Azure CLI 2.0 模組即將推出，內含所有功能的操作 (包括即時、內容金鑰原則、帳戶/資產篩選器、串流原則)。 

### <a name="known-issues"></a>已知問題

只有使用適用於資產或帳戶篩選器預覽 API 的客戶會受到下列問題影響。

如果您在 09/28 到 10/12 之間使用媒體服務 v3 CLI 或 API 來建立資產或帳戶篩選器，由於版本衝突，您必須移除所有的資產和帳戶篩選器並加以重新建立。 

## <a name="may-2018---preview"></a>2018 年 5 月 - 預覽

### <a name="net-sdk"></a>.NET SDK

.NET SDK 中有下列功能：

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
- [媒體服務 v2 版本資訊](../previous/media-services-release-notes.md)
