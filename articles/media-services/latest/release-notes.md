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
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 1db7009096635fc1279ce8a8358e0d8131209722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372592"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure 媒體服務 v3 版本資訊

>通過複製和粘貼此 URL：`https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us`到 RSS 源閱讀器，獲得有關何時重新訪問此頁面以進行更新的通知。

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="known-issues"></a>已知問題

> [!NOTE]
> 您可以使用 Azure[門戶](https://portal.azure.com/)管理 v3[即時事件](live-events-outputs-concept.md)、查看 v3[資產](assets-concept.md)、獲取有關訪問 API 的資訊。 對於所有其他管理工作（例如，轉換和作業），請使用[REST API](https://aka.ms/ams-v3-rest-ref) [、CLI](https://aka.ms/ams-v3-cli-ref)或受支援的[SDK](media-services-apis-overview.md#sdks)之一。

如需詳細資訊，請參閱[從媒體服務 v2 移至 v3 的移轉指導](migrate-from-v2-to-v3.md#known-issues)。
 
## <a name="january-2020"></a>2020 年 1 月

### <a name="improvements-in-media-processors"></a>媒體處理器的改進

- 改進了視頻分析中對隔行掃描源的支援 - 此類內容現在在發送到推理引擎之前已正確取消隔行掃描。
- 使用"最佳"模式生成縮略圖時，編碼器現在搜索超過 30 秒以選擇非單色的幀。

### <a name="azure-government-cloud-updates"></a>Azure 政府雲更新

媒體服務 GA'ed 在以下 Azure 政府區域： *USGov 亞利桑那州*和美國*德克薩斯州 USGov*.

## <a name="december-2019"></a>2019 年 12 月

添加了對*源-輔助預取*標頭的 CDN 支援，用於即時和視頻點播流;適用于與 Akamai CDN 有直接合同的客戶。 源-輔助 CDN-預獲取功能涉及 Akamai CDN 和 Azure 媒體服務源之間的以下 HTTP 標頭交換：

|HTTP 標頭|值|傳送者|接收者|目的|
| ---- | ---- | ---- | ---- | ----- |
|CDN-原點-輔助-預取啟用 | 1（預設）或 0 |CDN|來源|指示 CDN 已啟用預取|
|CDN-原點-輔助-預取路徑| 範例： <br/>片段（視頻=1400000000，格式=mpd-time-cmaf）|來源|CDN|提供 CDN 的預取路徑|
|CDN-原點-輔助-預取-請求|1（預取請求）或 0（常規請求）|CDN|來源|指示來自 CDN 的請求是預取|

要查看標頭交換的一部分，可以嘗試以下步驟：

1. 使用 Postman 或捲曲向媒體服務源發出音訊或視頻片段或片段的請求。 請確保在請求中添加標頭 CDN-原源-輔助-預取啟用：1。
2. 在回應中，您應該看到以相對路徑為值的標頭 CDN-原源-輔助-預取路徑。

## <a name="november-2019"></a>2019 年 11 月

### <a name="live-transcription-preview"></a>即時轉錄預覽

即時轉錄現已公開預覽，可在美國西部 2 區域使用。

即時轉錄設計為與即時事件結合使用，作為附加功能。  在傳遞和標準編碼或高級編碼即時事件上都支援它。  啟用此功能後，該服務將使用認知服務的["語音到文本](../../cognitive-services/speech-service/speech-to-text.md)"功能將傳入音訊中的口語轉錄為文本。 然後，在 MPEG-DASH 和 HLS 協定中，此文本與視頻和音訊一起提供。 計費基於新的附加儀錶，該表是即時事件處於"運行"狀態時的額外費用。  有關即時轉錄和計費的詳細資訊，請參閱[即時轉錄](live-transcription.md)

> [!NOTE]
> 目前，即時轉錄僅在美國西部 2 區域作為預覽功能提供。 它支援在這個時候以英語轉錄口語（ en-us）。

### <a name="content-protection"></a>內容保護

9 月份在有限區域發佈的*權杖重播預防*功能現已在所有區域提供。
媒體服務客戶現在可以對同一權杖可用於請求金鑰或許可證的次數設置限制。 有關詳細資訊，請參閱[權杖重播預防](content-protection-overview.md#token-replay-prevention)。

### <a name="new-recommended-live-encoder-partners"></a>新的推薦即時編碼器合作夥伴

添加了對 RTMP 即時流的以下新推薦合作夥伴編碼器的支援：

- [坎布里亞現場 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 和最大動作攝像機](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>檔編碼增強功能

- 新的內容感知編碼預設現已可用。 它通過使用內容感知編碼生成一組與 GOP 對齊的 MP4。 給定任何輸入內容，服務對輸入內容執行初始羽量級分析。 它使用這些結果來確定通過自我調整流道傳送的最佳圖層數、適當的位元速率和解析度設置。 此預設對於低複雜性和中等複雜性視頻特別有效，其中輸出檔案位元速率較低，但品質仍可為觀看者提供良好的體驗。 輸出將包含帶有視頻和音訊交錯的 MP4 檔。 有關詳細資訊，請參閱打開的[API 規範](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)。
- 標準編碼器中重新分型器的性能和多執行緒性改進。 在特定情況下，客戶應看到 5-40% VOD 編碼之間的性能提升。 編碼為多個位元速率的低複雜內容的性能將最高。 
- 標準編碼現在保持在 VOD 編碼期間可變畫面播放速率 （VFR） 內容的常規 GOP 節奏，使用基於時間的 GOP 設置。  這意味著，提交混合畫面播放速率內容（例如，在 15-30 fps 之間變化）的客戶現在應該看到在輸出自我調整位元速率流 MP4 檔時計算的常規 GOP 距離。 這將提高通過 HLS 或 DASH 交付時在軌道之間無縫切換的能力。 
-  改進了可變畫面播放速率 （VFR） 源內容的 AV 同步

### <a name="video-indexer-video-analytics"></a>視頻索引子，視頻分析

- 使用 VideoAnalyzer 預設提取的關鍵幀現在採用視頻的原始解析度，而不是調整大小。 高解析度關鍵幀提取可為您提供原始品質圖像，並允許您利用 Microsoft 電腦視覺和自訂視覺服務提供的基於圖像的人工智慧模型，從視頻中獲得更多見解。

## <a name="september-2019"></a>2019 年 9 月

###  <a name="media-services-v3"></a>媒體服務 v3  

#### <a name="live-linear-encoding-of-live-events"></a>即時事件的即時線性編碼

媒體服務 v3 宣佈預覽 24 小時 x 365 天的即時線性編碼的即時事件。

###  <a name="media-services-v2"></a>媒體服務 v2  

#### <a name="deprecation-of-media-processors"></a>媒體處理器的棄用

我們宣佈棄用 Azure*媒體索引子*和*Azure 媒體索引子 2 預覽*。 有關停用日期，請參閱[舊元件](../previous/legacy-components.md)主題。 [Azure 媒體服務視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)將替換這些舊媒體處理器。

有關詳細資訊，請參閱從[Azure 媒體索引子和 Azure 媒體索引子 2 遷移到 Azure 媒體服務視頻索引子](../previous/migrate-indexer-v1-v2.md)。

## <a name="august-2019"></a>2019 年 8 月

###  <a name="media-services-v3"></a>媒體服務 v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>南非區域對對媒體服務開放 

媒體服務現已在南非北部和南非西部地區提供。

有關詳細資訊，請參閱[媒體服務 v3 存在的雲和區域](azure-clouds-regions.md)。

###  <a name="media-services-v2"></a>媒體服務 v2  

#### <a name="deprecation-of-media-processors"></a>媒體處理器的棄用

我們宣佈棄用正在停用的*Windows Azure 媒體編碼器*（WAME） 和*Azure 媒體編碼器*（AME） 媒體處理器。 有關停用日期，請參閱此[遺留元件](../previous/legacy-components.md)主題。

有關詳細資訊，請參閱[將 WAME 遷移到媒體編碼器標準](https://go.microsoft.com/fwlink/?LinkId=2101334)並將[AME 遷移到媒體編碼器標準](https://go.microsoft.com/fwlink/?LinkId=2101335)。
 
## <a name="july-2019"></a>2019 年 7 月

### <a name="content-protection"></a>內容保護

當資料流受權杖限制保護的內容時，最終使用者需要獲取作為金鑰傳遞請求的一部分發送的權杖。 *權杖重播防止*功能允許媒體服務客戶對同一權杖可用於請求金鑰或許可證的次數設置限制。 有關詳細資訊，請參閱[權杖重播預防](content-protection-overview.md#token-replay-prevention)。

截至 7 月，預覽功能僅在美國中部和美國中西部提供。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-subclipping"></a>視頻子剪輯

現在，您可以使用[作業](https://docs.microsoft.com/rest/api/media/jobs)對其進行編碼時修剪或子剪輯視頻。 

此功能適用于使用[內置標準編碼預設預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)或[標準編碼預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)構建的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。 

請參閱示例：

* [使用 .NET 對視頻進行子剪輯](subclip-video-dotnet-howto.md)
* [使用 REST 對視頻進行子剪輯](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019 年 5 月

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure 監視器支援媒體服務診斷日誌和指標

現在可以使用 Azure 監視器查看媒體服務發出的遙測資料。

* 使用 Azure 監視器診斷日誌監視媒體服務金鑰傳遞終結點發送的請求。 
* 監視媒體服務[流終結點發出的指標](streaming-endpoint-concept.md)。   

有關詳細資訊，請參閱[監視媒體服務指標和診斷日誌](media-services-metrics-diagnostic-logs.md)。

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>動態包裝中支援多聲道 

當具有多個音軌和語言的流式處理資產時，[動態打包](dynamic-packaging-overview.md)現在支援 HLS 輸出的多聲道（版本 4 或以上）。

### <a name="korea-regional-pair-is-open-for-media-services"></a>韓國區域對對媒體服務開放 

媒體服務現已在韓國中部和韓國南部地區提供。 

有關詳細資訊，請參閱[媒體服務 v3 存在的雲和區域](azure-clouds-regions.md)。

### <a name="performance-improvements"></a>效能改善

添加了包含媒體服務性能改進的更新。

* 已更新支援處理的最大檔案大小。 請參閱，[配額和限制](limits-quotas-constraints.md)。
* [編碼速度改進](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)。

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-presets"></a>新預設

* [FaceDetector 預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)已添加到內置分析儀預設中。
* [內容感知編碼實驗](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)已添加到內置編碼器預設中。 有關詳細資訊，請參閱[內容感知編碼](content-aware-encoding.md)。 

## <a name="march-2019"></a>2019 年 3 月

動態包裝現在支援杜比全景聲。 有關詳細資訊，請參閱[動態打包支援的音訊編解碼器](dynamic-packaging-overview.md#audio-codecs)。

您現在可以指定適用于流式處理器的資產或帳戶篩選器的清單。 有關詳細資訊，請參閱[將篩選器與流式處理器相關聯](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="february-2019"></a>2019 年 2 月

媒體服務 v3 現在支援 Azure 國家雲。 尚未在所有雲端中提供所有功能。 如需詳細資訊，請參閱[存在 Azure 媒體服務 v3 的雲端和區域](azure-clouds-regions.md)。

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

.NET SDK 中存在以下功能：

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
