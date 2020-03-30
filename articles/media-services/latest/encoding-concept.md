---
title: 使用媒體服務對視頻和音訊進行編碼
titleSuffix: Azure Media Services
description: 本文介紹使用 Azure 媒體服務對視頻和音訊進行編碼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366582"
---
# <a name="encoding-video-and-audio-with-media-services"></a>使用媒體服務對視頻和音訊進行編碼

媒體服務中的術語編碼適用于將包含數位視訊和/或音訊的檔從一種標準格式轉換為另一種標準格式的過程，目的是 （a） 減小檔案大小，和/或 （b） 生成與各種設備和應用程式的相容格式的過程。 此過程也稱為視訊壓縮或轉碼。 有關概念的進一步討論，請參閱[資料壓縮](https://en.wikipedia.org/wiki/Data_compression)[和什麼是編碼和轉碼。](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

視頻通常通過[漸進式下載](https://en.wikipedia.org/wiki/Progressive_download)或通過[自我調整位元速率流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)傳送到設備和應用程式。

> [!IMPORTANT]
> 媒體服務不為已取消或出錯的作業收費。 例如，進度達到 50% 且已取消的作業不會以工作分鐘數的 50% 計費。 您只收取已完成作業的費用。

* 要通過漸進式下載進行交付，可以使用 Azure 媒體服務將數位媒體檔案（夾層）轉換為[MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14)檔，該檔包含使用[H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC)編解碼器編碼的視頻以及使用[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)編解碼器編碼的音訊。 此 MP4 檔寫入存儲帳戶中的資產。 您可以使用 Azure 存儲 API 或 SDK（例如，存儲[REST API](../../storage/common/storage-rest-api-auth.md)或[.NET SDK）](../../storage/blobs/storage-quickstart-blobs-dotnet.md)直接下載檔案。 如果創建具有存儲中特定容器名稱的輸出資產，請使用該位置。 否則，您可以使用媒體服務[列出資產容器 URL。](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) 
* 要準備內容通過自我調整位元速率流傳遞，夾層檔需要以多個位元速率（高到低）進行編碼。 為了確保品質的優雅過渡，隨著位元速率的降低，視頻的解析度會降低。 這將導致一個所謂的編碼梯 - 解析度和位元速率表（請參閱[自動生成的自我調整位元速率階梯](autogen-bitrate-ladder.md)）。 您可以使用媒體服務以多個位元速率對夾層檔進行編碼。 這樣，您將獲得一組 MP4 檔和關聯的流式處理設定檔，這些檔將寫入存儲帳戶中的資產。 然後，您可以使用媒體服務中[的動態打包](dynamic-packaging-overview.md)功能通過[MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP)和[HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)等流式處理協定提供視頻。 這要求您創建[流式處理器](streaming-locators-concept.md)並生成與受支援的協定對應的流式處理 URL，然後根據設備/應用的功能將其移交給設備/應用。

下圖顯示了使用動態打包進行按需編碼的工作流。

![動態打包按需編碼的工作流](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

本主題會指導您如何使用媒體服務 v3 將內容編碼。

## <a name="transforms-and-jobs"></a>轉換和作業

要使用媒體服務 v3 進行編碼，您需要創建[一個轉換](https://docs.microsoft.com/rest/api/media/transforms)和一個[作業](https://docs.microsoft.com/rest/api/media/jobs)。 轉換定義編碼設置和輸出的配方;作業是配方的實例。 有關詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)。

使用媒體服務編碼時，需使用預設來告訴編碼器應該如何處理輸入媒體檔案。 在媒體服務 v3 中，您可以使用標準編碼器對檔進行編碼。 例如，您可以指定所編碼內容中需要的視訊解析度及/或音訊聲道數目。

您可以使用依據業界最佳做法所建議的其中一個內建預設來快速開始，也可以選擇建置以特定案例或裝置需求為標的的自訂預設。 如需詳細資訊，請參閱[使用自訂轉換進行編碼](customize-encoder-presets-how-to.md)。

從 2019 年 1 月開始，當使用標準編碼器進行編碼以生成 MP4 檔時，將生成新的 .mpi 檔並將其添加到輸出資產中。 此 MPI 檔案的目的是用來改進[動態封裝](dynamic-packaging-overview.md)與串流處理案例的效能。

> [!NOTE]
> 您不應修改或刪除 MPI 檔，也不應在服務中對此類檔是否存在（或不）採取任何依賴項。

### <a name="creating-job-input-from-an-https-url"></a>從 HTTPS URL 創建作業輸入

提交作業以處理視頻時，必須告訴媒體服務部在哪裡可以找到輸入視頻。 其中一個選項是指定 HTTPS URL 作為作業輸入。 目前，媒體服務 v3 不支援通過 HTTPS URL 進行分段傳輸編碼。

#### <a name="examples"></a>範例

* [使用 .NET 的 HTTPS URL 進行編碼](stream-files-dotnet-quickstart.md)
* [使用 REST 從 HTTPS URL 進行編碼](stream-files-tutorial-with-rest.md)
* [使用 CLI 從 HTTPS URL 進行編碼](stream-files-cli-quickstart.md)
* [使用 Node.js 從 HTTPS URL 進行編碼](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>從本地檔創建作業輸入

輸入視訊可以儲存為媒體服務資產，在這種情況下，您會建立一個以檔案為基礎的輸入資產 (儲存在本機或 Azure Blob 儲存體)。

#### <a name="examples"></a>範例

[使用內置預設對本地檔進行編碼](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>使用子裁剪創建作業輸入

對視頻進行編碼時，可以指定還可以修剪或剪輯原始檔案，並生成僅具有輸入視頻所需部分的輸出。 此功能適用于使用[內置標準編碼預設預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)或[標準編碼預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)構建的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。

您可以指定使用視頻點播或即時存檔（錄製的事件）的單個剪輯創建[作業](https://docs.microsoft.com/rest/api/media/jobs/create)。 作業輸入可以是資產或 HTTPS URL。

> [!TIP]
> 如果要資料流視頻的子唇而不重新編碼視頻，請考慮[使用動態打包器的預篩選清單](filters-dynamic-manifest-overview.md)。

#### <a name="examples"></a>範例

請參閱示例：

* [使用 .NET 對視頻進行子剪輯](subclip-video-dotnet-howto.md)
* [使用 REST 對視頻進行子剪輯](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>內建預設

媒體服務支援以下內置編碼預設：  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) \(英文\) 是用來設定內建預設，以供使用標準編碼器為輸入視訊編碼時使用。

目前支援的預設如下：

- **編碼器命名預設.AACGoodQualityAudio：** 生成單個MP4檔，僅包含以192 kbps編碼的身歷聲音訊。
- **編碼器命名預設.自我調整流**（推薦）：有關詳細資訊，請參閱[自動生成位元速率梯](autogen-bitrate-ladder.md)。
- **編碼器命名預設.內容感知編碼實驗**：公開內容感知編碼的實驗預設。 給定任何輸入內容，服務嘗試自動確定最佳層數，以及適當的位元速率和解析度設置，以便通過自我調整流式處理進行傳遞。 基礎演算法將隨著時間的推移繼續演變。 輸出將包含帶有視頻和音訊交錯的 MP4 檔。 有關詳細資訊，請參閱[內容感知編碼的實驗預設](content-aware-encoding.md)。
- **編碼器命名預設.H264多倍位元速率1080p：** 生成一組8個GOP對齊的MP4檔，範圍從6000 kbps到400 kbps，和身歷聲AAC音訊。 解析度起自 1080p，下至 360p。
- **編碼器命名預設.H264多倍比特720p：** 生成一組六個GOP對齊的MP4檔，範圍從3400 kbps到400 kbps，和身歷聲AAC音訊。 解析度起自 720p，下至 360p。
- **編碼器命名預設.H264多位元率SD：** 生成一組五個GOP對齊的MP4檔，範圍從1600 kbps到400 kbps，和身歷聲AAC音訊。 解析度起自 480p，下至 360p。
- **編碼器命名預設.H264單比特1080p：** 生成MP4檔，其中視頻編碼與H.264編解碼器在6750 kbps和圖像高度1080圖元，身歷聲音訊編碼與AAC-LC編解碼器在64 kbps。
- **編碼器命名預設.H264單比特720p**：生成MP4檔，其中視頻編碼與H.264編解碼器在4500 kbps和圖像高度720圖元，身歷聲音訊編碼與AAC-LC編解碼器在64 kbps。
- **編碼器命名預設.H264單比特SD**：生成一個MP4檔，其中視頻編碼與H.264編解碼器在2200 kbps和圖像高度480圖元，身歷聲音訊編碼與AAC-LC編解碼器在64 kbps。

要查看最新的預設清單，請參閱[用於編碼視頻的內置預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。

要查看如何使用預設，請參閱[上傳、編碼和流式處理檔](stream-files-tutorial-with-api.md)。

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) \(英文\) 能描述在使用標準編碼器為輸入視訊編碼時所要使用的設定。 在自訂轉換預設時，請使用此預設。

#### <a name="considerations"></a>考量

創建自訂預設時，需要考慮以下因素：

- AVC 內容上的高度和寬度的所有值必須為四倍。
- 在 Azure 媒體服務 v3 中，所有編碼位元速率均以每秒位表示。 這與 v2 API 的預設不同，後者使用千位/秒作為單元。 例如，如果 v2 中的位元速率指定為 128 （千位/秒），則 v3 中它將設置為 128000（位/秒）。

### <a name="customizing-presets"></a>自訂預設

「媒體服務」可完整支援自訂預設中的所有值，以滿足您的特定編碼需要和需求。 有關演示如何自訂編碼器預設的示例，請參閱以下清單：

#### <a name="examples"></a>範例

- [使用 .NET 自訂預設](customize-encoder-presets-how-to.md)
- [使用 CLI 自訂預設](custom-preset-cli-howto.md)
- [使用 REST 自訂預設](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>預設架構

在媒體服務 v3 中，預設是 API 本身中的強型別實體。 您可以在[Open API 規範（或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到這些物件的"架構"定義。 您還可以在[REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [、.NET SDK（](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)或其他媒體服務 v3 SDK 參考文檔中）中查看預設定義（如**標準編碼預設**）。

## <a name="scaling-encoding-in-v3"></a>在 v3 中調整編碼

要擴展媒體處理，請參閱[使用 CLI 進行縮放](media-reserved-units-cli-how-to.md)。

## <a name="billing"></a>計費

媒體服務不為已取消或出錯的作業收費。 例如，進度達到 50% 且已取消的作業不會以工作分鐘數的 50% 計費。 您只收取已完成作業的費用。

有關詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [使用媒體服務上載、編碼和資料流](stream-files-tutorial-with-api.md)。
* [使用內置預設從 HTTPS URL 進行編碼](job-input-from-http-how-to.md)。
* [使用內置預設對本地檔進行編碼](job-input-from-local-file-how-to.md)。
* [構建自訂預設以定位您的特定方案或設備要求](customize-encoder-presets-how-to.md)。
