---
title: 從索引子 v1 和 v2 遷移至 Azure 媒體服務影片索引子 |Microsoft Docs
description: 本主題討論如何從 Azure 媒體索引子 v1 和 v2 遷移至 Azure 媒體服務影片索引子。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 330bffebb870635fd473e88a8eadb300eed40b9b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012130"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>從媒體索引子和媒體索引子2遷移至影片索引子

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> 建議客戶從索引子 v1 和索引子 v2 遷移至使用 [媒體服務 V3 AudioAnalyzerPreset 基本模式](../latest/analyzing-video-audio-files-concept.md)。 [Azure 媒體索引子](media-services-index-content.md)媒體處理器和[Azure 媒體索引子 2 Preview](./legacy-components.md)媒體處理器即將淘汰。 如需淘汰日期，請參閱此[舊版元件](legacy-components.md)主題。

Azure 媒體服務影片索引子建基於 Azure 媒體分析、Azure 認知搜尋、認知服務 (例如臉部 API、Microsoft Translator、電腦視覺 API 和自訂語音服務) 。 它可讓您使用影片索引器的影片與音訊模型，從影片中擷取見解。 若要瞭解影片索引子可用於哪些案例、它提供哪些功能，以及如何開始使用，請參閱 [影片索引子影片和音訊模型](../video-indexer/video-indexer-overview.md)。 

您可以使用 [Azure 媒體服務 v3 分析器](../latest/analyzing-video-audio-files-concept.md) 預設，或直接使用 [影片索引子 api](https://api-portal.videoindexer.ai/)，從影片和音訊檔案中解壓縮見解。 目前，影片索引子 Api 和媒體服務 v3 Api 所提供的功能有重迭。

> [!NOTE]
> 若要瞭解影片索引子與媒體服務分析器預設值之間的差異，請參閱 [比較檔](../video-indexer/compare-video-indexer-with-media-services-presets.md)。

本文討論從 Azure 媒體索引子和 Azure 媒體索引子2遷移至 Azure 媒體服務影片索引子的步驟。  

## <a name="migration-options"></a>移轉選項

|如果您需要  |然後在受影響的網域控制站上執行 |
|---|---|
|針對隱藏式輔助字幕檔案格式的任何媒體檔案格式，提供語音轉換文字轉譯的解決方案： VTT、SRT 或 TTML<br/>以及其他音訊見解，例如：關鍵字、主題推斷、聲場活動、喇叭 diarization、實體解壓縮和轉譯| 將您的應用程式更新為透過影片索引子 v2 REST API 或 Azure 媒體服務 v3 音訊分析器預設值來使用 Azure 影片索引子功能。|
|語音轉換文字功能| 直接使用認知服務語音 API。|  

## <a name="getting-started-with-video-indexer"></a>開始使用影片索引子

下一節會將您指向相關連結： [如何開始使用影片索引子？](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>開始使用媒體服務 v3 Api

Azure 媒體服務 v3 API 可讓您透過 [Azure 媒體服務 v3 分析器](../latest/analyzing-video-audio-files-concept.md)預設，從影片和音訊檔案中解壓縮見解。

**AudioAnalyzerPreset** 可讓您從音訊檔案或視訊檔案擷取多個音訊深入資訊。 輸出中會包含音訊文字記錄的 VTT 或 TTML 檔案，以及與所有其他音訊深入解析)  (的 JSON 檔案。 音訊見解包括關鍵字、說話者索引和語音情感分析。 AudioAnalyzerPreset 也支援特定語言的語言偵測。 如需詳細資訊，請參閱 [轉換](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)。

### <a name="get-started"></a>開始使用

若要開始使用，請參閱：

* [教學課程](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset 範例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) 或 [.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset 範例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) 或 [.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>開始使用認知服務語音服務

[Azure 認知服務](../../cognitive-services/index.yml) 提供語音轉換文字服務，可即時將音訊串流轉譯至文字，讓您的應用程式、工具或裝置可以使用或顯示。 您可以使用語音轉換文字 [來自訂您自己的聲場模型、語言模型或發音模型](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)。 如需詳細資訊，請參閱 [認知服務語音轉換文字](../../cognitive-services/speech-service/speech-to-text.md)。 

> [!NOTE] 
> 語音轉換文字服務不會採用影片檔案格式，而且只會採用 [特定的音訊格式](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)。 

如需文字轉換語音服務及如何開始使用的詳細資訊，請參閱 [什麼是語音轉文字？](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>已淘汰服務的已知差異

您會發現影片索引子、Azure 媒體服務 v3 AudioAnalyzerPreset 和認知服務語音服務服務更可靠，而且會產生比淘汰的 Azure 媒體索引子1和 Azure 媒體索引子2處理器更高的品質輸出。  

有一些已知的差異，包括：

* 認知服務語音服務不支援關鍵字解壓縮。 不過，影片索引子和媒體服務 v3 AudioAnalyzerPreset 都提供一組更健全的 JSON 檔案格式關鍵字。

## <a name="support"></a>支援

您可以藉由瀏覽至[新增支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來開啟支援票證

## <a name="next-steps"></a>後續步驟

* [舊版元件](legacy-components.md)
* [定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)