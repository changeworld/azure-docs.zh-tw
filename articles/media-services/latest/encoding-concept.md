---
title: 使用媒體服務編碼影片和音訊
titleSuffix: Azure Media Services
description: 本文說明如何使用 Azure 媒體服務來編碼影片和音訊。
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
ms.openlocfilehash: 7f3825a2d87d5948de4bb4a9b86be8e3050f2100
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251514"
---
# <a name="encoding-video-and-audio-with-media-services"></a>使用媒體服務編碼影片和音訊

媒體服務中的「編碼」一詞適用于將包含數位影片和/或音訊的檔案從一種標準格式轉換成另一種格式的程式，目的是（a）減少檔案大小，並（或）（b）產生與各種裝置和應用程式相容的格式。 這個進程也稱為「影片壓縮」或轉碼。 如需概念的進一步討論，請參閱[資料壓縮](https://en.wikipedia.org/wiki/Data_compression)和[什麼是編碼和轉碼？](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) 。

影片通常會藉由[漸進式下載](https://en.wikipedia.org/wiki/Progressive_download)或透過自動調整[位元速率串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，傳遞至裝置和應用程式。

> [!IMPORTANT]
> 媒體服務不會針對已取消或錯誤的作業計費。 例如，已達到50% 進度且已取消的作業不會以工作分鐘的50% 來計費。 您只需支付完成作業的費用。

* 若要透過漸進式下載進行傳遞，您可以使用 Azure 媒體服務將數位媒體檔案（夾層）轉換[成有案](https://en.wikipedia.org/wiki/MPEG-4_Part_14)的檔案，其中包含使用[h.264 編解碼器](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC)編碼的影片，以及使用[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)編解碼器編碼的音訊。 此「檔案」檔案會寫入至儲存體帳戶中的資產。 您可以使用 Azure 儲存體 Api 或 Sdk （例如[儲存體 REST API](../../storage/common/storage-rest-api-auth.md)或[.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）直接下載檔案。 如果您已在儲存體中建立具有特定容器名稱的輸出資產，請使用該位置。 否則，您可以使用媒體服務來[列出資產容器 url](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。 
* 若要以彈性位元速率串流準備傳遞內容，必須在多個位元速率（高至低）編碼夾層檔案。 為確保品質正常轉換，影片的解析度會因為位元速率降低而降低。 這會導致所謂的編碼階梯–一份解析度和位元速率的資料表（請參閱[自動產生的彈性位元速率階梯](autogen-bitrate-ladder.md)）。 您可以使用媒體服務，在多個位元速率對夾層檔案進行編碼。 在這種情況下，您會取得一組已寫入儲存體帳戶資產的「檔」檔案和相關聯的串流設定檔案。 然後，您可以使用媒體服務中的[動態封裝](dynamic-packaging-overview.md)功能，透過像是[mpeg-4](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP)和[HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)等串流通訊協定來傳遞影片。 這會要求您建立[串流定位器](streaming-locators-concept.md)，並建立對應于支援之通訊協定的串流 url，然後可以根據其功能將裝置/應用程式交給他們。

下圖顯示隨選編碼與動態封裝的工作流程。

![使用動態封裝進行隨選編碼的工作流程](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

本主題會指導您如何使用媒體服務 v3 將內容編碼。

## <a name="transforms-and-jobs"></a>轉換和作業

若要使用媒體服務 v3 來編碼，您需要建立[轉換](https://docs.microsoft.com/rest/api/media/transforms) \(英文\) 和[作業](https://docs.microsoft.com/rest/api/media/jobs) \(英文\)。 轉換會為您的編碼設定和輸出定義配方;作業是配方的實例。 如需詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)。

使用媒體服務編碼時，需使用預設來告訴編碼器應該如何處理輸入媒體檔案。 例如，您可以指定所編碼內容中需要的視訊解析度及/或音訊聲道數目。

您可以使用依據業界最佳做法所建議的其中一個內建預設來快速開始，也可以選擇建置以特定案例或裝置需求為標的的自訂預設。 如需詳細資訊，請參閱[使用自訂轉換進行編碼](customize-encoder-presets-how-to.md)。

從 2019 年 1 月開始，使用「媒體編碼器標準」來編碼以產生 MP4 檔案時，會產生新的 .mpi 檔案並將該檔案新增到輸出資產。 此 MPI 檔案的目的是用來改進[動態封裝](dynamic-packaging-overview.md)與串流處理案例的效能。

> [!NOTE]
> 您不應該修改或移除 MPI 檔案，或在您的服務中，針對這類檔案的存在與否採取任何相依性。

### <a name="creating-job-input-from-an-https-url"></a>從 HTTPS URL 建立作業輸入

當您提交工作來處理影片時，必須告訴媒體服務在何處尋找輸入影片。 其中一個選項是將 HTTPS URL 指定為作業輸入。 目前，媒體服務 v3 不支援透過 HTTPS Url 的區塊傳輸編碼。

#### <a name="examples"></a>範例

* [使用 .NET 從 HTTPS URL 進行編碼](stream-files-dotnet-quickstart.md)
* [使用 REST 從 HTTPS URL 進行編碼](stream-files-tutorial-with-rest.md)
* [使用 CLI 從 HTTPS URL 進行編碼](stream-files-cli-quickstart.md)
* [使用 node.js 從 HTTPS URL 進行編碼](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>從本機檔案建立工作輸入

輸入視訊可以儲存為媒體服務資產，在這種情況下，您會建立一個以檔案為基礎的輸入資產 (儲存在本機或 Azure Blob 儲存體)。

#### <a name="examples"></a>範例

[使用內建預設值來編碼本機檔案](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>使用子剪輯建立工作輸入

編碼影片時，您可以指定同時修剪或裁剪原始程式檔，並產生只具有輸入影片所需部分的輸出。 這項功能適用于使用 [ [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ] 預設值或 [ [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) ] 預設建立的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。

您可以指定使用影片隨選或即時封存（錄製的事件）的單一剪輯來建立[作業](https://docs.microsoft.com/rest/api/media/jobs/create)。 作業輸入可以是資產或 HTTPS URL。

> [!TIP]
> 如果您想要串流處理影片的 sublip，而不重新編碼影片，請考慮使用[預先篩選的資訊清單搭配動態](filters-dynamic-manifest-overview.md)封裝工具。

#### <a name="examples"></a>範例

請參閱範例：

* [使用 .NET 子剪輯影片](subclip-video-dotnet-howto.md)
* [使用 REST 子剪輯影片](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>內建預設

媒體服務支援下列內建的編碼預設：  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) \(英文\) 是用來設定內建預設，以供使用標準編碼器為輸入視訊編碼時使用。

目前支援的預設如下：

- **EncoderNamedPreset. AACGoodQualityAudio**：產生單一的一個檔案，其中只包含 192 kbps 編碼的身歷聲音訊。
- **EncoderNamedPreset. AdaptiveStreaming** （建議選項）：如需詳細資訊，請參閱[自動產生位元速率階梯](autogen-bitrate-ladder.md)。
- **EncoderNamedPreset. ContentAwareEncodingExperimental**：公開內容感知編碼的實驗性預設值。 針對任何輸入內容，服務會嘗試自動判斷最佳的層級數目，以及彈性串流傳遞所需的適當位元速率和解析度設定。 基礎演算法會在一段時間後繼續進化。 輸出會包含具有影片和音訊交錯的有案檔案。 如需詳細資訊，請參閱[內容感知編碼的實驗](content-aware-encoding.md)性預設值。
- **EncoderNamedPreset. H264MultipleBitrate1080p**：產生一組八個對齊 GOP 的設定檔案，範圍從 6000 kbps 到 400 kbps，以及身歷聲 AAC 音訊。 解析度起自 1080p，下至 360p。
- **EncoderNamedPreset. H264MultipleBitrate720p**：產生一組六個對齊 GOP 的設定檔案，範圍從 3400 kbps 到 400 kbps，以及身歷聲 AAC 音訊。 解析度起自 720p，下至 360p。
- **EncoderNamedPreset. H264MultipleBitrateSD**：產生一組五個對齊 GOP 的設定檔案，範圍從 1600 kbps 到 400 kbps，以及身歷聲 AAC 音訊。 解析度起自 480p，下至 360p。
- **EncoderNamedPreset. H264SingleBitrate1080p**：產生一個有量的檔案，其中的影片是以 6750 kbps 的 h.264 編解碼器和圖片高度（1080圖元）編碼，而身歷聲音訊則是以 64 KBPS 的 AAC-LC 編解碼器編碼。
- **EncoderNamedPreset. H264SingleBitrate720p**：產生一個有量的檔案，其中的影片是以 4500 kbps 的 h.264 編解碼器和圖片高度（720圖元）編碼，而身歷聲音訊則是以 64 KBPS 的 AAC-LC 編解碼器編碼。
- **EncoderNamedPreset. H264SingleBitrateSD**：產生一個有量的檔案，其中的影片是以 2200 kbps 的 h.264 編解碼器和圖片高度（480圖元）編碼，而身歷聲音訊則是以 64 KBPS 的 AAC-LC 編解碼器編碼。

若要查看最新的預設清單，請參閱用來[編碼影片的內建](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)預設值。

若要查看如何使用預設值，請參閱[上傳、編碼和串流](stream-files-tutorial-with-api.md)檔案。

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) \(英文\) 能描述在使用標準編碼器為輸入視訊編碼時所要使用的設定。 在自訂轉換預設時，請使用此預設。

#### <a name="considerations"></a>考量

建立自訂預設時，適用下列考慮事項：

- AVC 內容的所有高度和寬度值都必須是四的倍數。
- 在 Azure 媒體服務 v3 中，所有編碼位元速率都是以每秒位數為單位。 這不同于預設值和我們的 v2 Api，其使用千位/秒作為單位。 例如，如果 v2 中的位元速率指定為128（千位/秒），則在 v3 中，它會設定為128000（位/秒）。

### <a name="customizing-presets"></a>自訂預設

「媒體服務」可完整支援自訂預設中的所有值，以滿足您的特定編碼需要和需求。 如需示範如何自訂編碼器預設值的範例，請參閱下列清單：

#### <a name="examples"></a>範例

- [使用 .NET 自訂預設值](customize-encoder-presets-how-to.md)
- [使用 CLI 自訂預設值](custom-preset-cli-howto.md)
- [使用 REST 自訂預設值](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>預設架構

在媒體服務 v3 中，預設為 API 本身的強型別實體。 您可以在[OPEN API 規格（或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到這些物件的「架構」定義。 您也可以在[REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)、 [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) （或其他媒體服務 v3 SDK 參考檔）中，查看預設的定義（例如**StandardEncoderPreset**）。

## <a name="scaling-encoding-in-v3"></a>在 v3 中調整編碼

若要調整媒體處理，請參閱[使用 CLI 進行調整](media-reserved-units-cli-how-to.md)。

## <a name="billing"></a>計費

媒體服務不會針對已取消或錯誤的作業計費。 例如，已達到50% 進度且已取消的作業不會以工作分鐘的50% 來計費。 您只需支付完成作業的費用。

如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

* [使用媒體服務上傳、編碼和串流](stream-files-tutorial-with-api.md)。
* [使用內建預設值從 HTTPS URL 進行編碼](job-input-from-http-how-to.md)。
* [使用內建的預設值來編碼本機](job-input-from-local-file-how-to.md)檔案。
* [建置自訂預設來以特定案例或裝置需求為目標](customize-encoder-presets-how-to.md)。
