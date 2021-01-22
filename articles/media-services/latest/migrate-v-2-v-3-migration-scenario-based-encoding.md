---
title: 編碼以案例為基礎的遷移指引 |Microsoft Docs
description: 本文提供編碼案例指導方針，可協助您從 Azure 媒體服務 v2 遷移至 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 2d122bdeb98de624d9053852b9bee4595b0ef8c8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690297"
---
# <a name="encoding-scenario-based-migration-guidance"></a>編碼以案例為基礎的遷移指導方針

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-4.svg)

本文提供編碼案例指導方針，可協助您從 Azure 媒體服務 v2 遷移至 v3。

## <a name="prerequisites"></a>必要條件

在您變更編碼工作流程之前，建議您先瞭解存放裝置管理方式的差異。  在 AMS V3 中，Azure 儲存體 API 是用來管理與您的媒體服務帳戶相關聯 () 的儲存體帳戶。

> [!NOTE]
> 在 v2 中建立的作業和工作不會顯示在 v3 中，因為它們沒有與轉換相關聯。 建議切換至 v3 轉換和作業。

## <a name="encoding-workflow-comparison"></a>編碼工作流程比較

請花幾分鐘的時間查看下列流程圖，以瞭解 V2 和 V3 的編碼工作流程的視覺化比較。

### <a name="v2-encoding-workflow"></a>V2 編碼工作流程

按一下下圖以查看較大的版本。

[![V2 ](./media/migration-guide/V2-pretty.svg) 的編碼工作流程](./media/migration-guide/V2-pretty.svg#lightbox)

1. 安裝程式
    1. 建立資產，或使用現有的資產。 如果使用新的資產，請將內容上傳到該資產。 如果使用現有的資產，您應該要編碼資產中已經存在的檔案。
    2. 取得下列專案的值：
        - 媒體處理器識別碼或物件
        - 您要使用之編碼器的編碼器字串 (名稱) 
        - 新資產的資產識別碼或現有資產的資產識別碼
    3. 若要進行監視，請建立作業或工作層級的通知訂閱或 SDK 事件處理常式
2. 建立包含工作的工作。 每項工作都應該包含上述專案和：
    - 需要建立輸出資產的指示詞。  輸出資產是由系統所建立。
    - 輸出資產的選擇性名稱
3. 提交作業。
4. 監視工作。

### <a name="v3-encoding-workflow"></a>V3 編碼工作流程

[![V3 ](./media/migration-guide/V3-pretty.svg) 的編碼工作流程](./media/migration-guide/V3-pretty.svg#lightbox)

1. 設定
    1. 建立資產，或使用現有的資產。 如果使用新的資產，請將內容上傳到該資產。 如果使用現有的資產，您應該要編碼資產中已經存在的檔案。 您不 *應該將更多內容上傳到該資產。*
    1. 建立輸出資產。  輸出資產是編碼的檔案和輸入和輸出中繼資料的儲存位置。
    1. 取得轉換的值：
        - 標準編碼器預設值
        - AMS 資源群組
        - AMS 帳戶名稱
    1. 建立轉換或使用現有的轉換。  轉換可以重複使用。 每次您想要提交工作時，不需要建立新的轉換。
1. 建立作業
    1. 針對作業，取得下列專案的值：
        - 轉換名稱
        - 您資產的 SAS URL 的基底 URI、檔案共用的 HTTPs 來源路徑，或檔案的本機路徑。 `JobInputAsset`也可以使用資產名稱作為輸入。
        - 檔案名 (s) 
        - 輸出資產 (s) 
        - 資源群組
        - AMS 帳戶名稱  
1. 使用 [事件方格](monitor-events-portal-how-to.md) 來監視您的作業。
1. 提交作業。

## <a name="custom-presets-from-v2-to-v3-encoding"></a>從 V2 到 V3 編碼的自訂預設

如果您的 V2 程式碼使用自訂預設值來呼叫標準編碼器，您必須先使用自訂標準編碼器預設值建立新的轉換，再提交作業。

自訂預設現在是 JSON，不再以 XML 為基礎。 依照 [轉換 OPEN API (Swagger) ](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) 檔中定義的自訂預設架構，在 JSON 中重新建立您的預設值。


<!-- removed because this is covered in the tutorials
Common custom [encoding](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) scenarios:
        1. Create a custom Single Bitrate MP4 encode
        1. Create a custom [Adaptive Bitrate Encoding Ladder](autogen-bitrate-ladder.md)
        1. Creating Sprite Thumbnails
        1. Creating Thumbnails (see below for your preferred method)
        1. [Sub Clipping](subclip-video-rest-howto.md)
        1. Cropping
-->

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>編碼作業中的輸入和輸出中繼資料檔案

在 v2 中，XML 輸入和輸出中繼資料檔案會產生為編碼工作的結果。 在 v3 中，元資料格式從 XML 變更為 JSON。 如需中繼資料的詳細資訊，請參閱 [輸入中繼資料](input-metadata-schema.md) 和 [輸出中繼資料](output-metadata-schema.md)。

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Premium 編碼器至 v3 標準編碼器或合作夥伴解決方案

V2 API 不再支援 Premium 編碼器。 如果您先前使用以工作流程為基礎的高階編碼器進行 HEVC 編碼，則應該使用 HEVC 編碼支援來遷移至新的 v3 [標準編碼器](media-encoder-standard-formats.md) 。

如果您需要 Premium 編碼器的高階工作流程功能，建議您從 [想像通訊](https://imaginecommunications.com)、 [Telestream](https://www.telestream.net)或 [Bitmovin](https://bitmovin.com)開始使用 Azure advanced 編碼合作夥伴解決方案。

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>具有 HTTPS 託管 Url 上輸入的工作

您現在可以從儲存在 Azure 儲存體中的檔案、儲存在本機的檔案，或使用 [HTTP (S) 作業輸入支援](job-input-from-http-how-to.md)的外部 web 伺服器，在 V3 中提交作業。

如果您先前已使用工作流程將檔案從 Azure blob 檔案複製到空的資產，然後提交作業，您可以將 Azure blob 儲存體中檔案的 SAS URL 直接傳遞至作業，以簡化您的工作流程。

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>索引子 v1 音訊轉譯至新的 AudioAnalyzer 「基本模式」

針對在 v2 API 中使用索引子 v1 處理器的客戶，您必須建立轉換， `AudioAnalyzer` 以在提交作業之前，先在 [基本模式](how-to-create-basic-audio-transform.md) 中叫用新的。

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>編碼、轉換和作業概念、教學課程和操作指南

### <a name="concepts"></a>概念

- [使用媒體服務編碼影片和音訊](encoding-concept.md)
- [標準編碼器格式和編解碼器](media-encoder-standard-formats.md)
- [使用自動產生的位元速率階梯進行編碼](autogen-bitrate-ladder.md)
- [使用內容感知編碼預設值，為指定的解決方式找出最佳位元速率值](content-aware-encoding.md)
- [媒體保留單元](concept-media-reserved-units.md)
- [輸入中繼資料](input-metadata-schema.md)
- [輸出中繼資料](output-metadata-schema.md)
- [媒體服務 v3 中的動態封裝：音訊編解碼器](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>教學課程

- [教學課程：編碼以 URL 為基礎的遠端檔案及串流處理影片 - .NET](stream-files-dotnet-quickstart.md)
- [教學課程：使用媒體服務 v3 上傳、編碼和串流處理影片](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>使用說明指南

- [從 HTTPS URL 建立工作輸入](job-input-from-http-how-to.md)
- [從本機檔案建立工作輸入](job-input-from-local-file-how-to.md)
- [建立基本音訊轉換](how-to-create-basic-audio-transform.md)
- 透過 .NET
  - [如何使用自訂轉換進行編碼-.NET](customize-encoder-presets-how-to.md)
  - [如何使用媒體編碼器標準建立覆蓋](how-to-create-overlay.md)
  - [如何使用搭配 .NET 的編碼器標準產生縮圖](media-services-generate-thumbnails-dotnet.md)
- 透過 Azure CLI
  - [如何使用自訂轉換進行編碼-Azure CLI](custom-preset-cli-howto.md)
- 透過 REST
  - [如何使用自訂轉換進行編碼-REST](custom-preset-rest-howto.md)
  - [如何使用編碼器標準搭配 REST 來產生縮圖](media-services-generate-thumbnails-rest.md)
- [使用媒體服務編碼時子剪輯影片-.NET](subclip-video-dotnet-howto.md)
- [使用媒體服務編碼時子剪輯影片-REST](subclip-video-rest-howto.md)

## <a name="samples"></a>範例

您也可以 [比較程式碼範例中的 V2 和 V3 程式碼](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
