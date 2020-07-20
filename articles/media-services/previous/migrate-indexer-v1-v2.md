---
title: 從索引子 v1 和 v2 遷移至 Azure 媒體服務影片索引子 |Microsoft Docs
description: 本主題討論如何從 Azure 媒體索引子 v1 和 v2 遷移至 Azure 媒體服務影片索引子。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76513230"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>從媒體索引子和媒體索引子2遷移到影片索引子

[Azure 媒體索引子](media-services-index-content.md)媒體處理器和[Azure 媒體索引子 2 Preview](media-services-process-content-with-indexer2.md)媒體處理器已淘汰。 如需淘汰日期，請參閱此[舊版元件](legacy-components.md)主題。 [Azure 媒體服務影片索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)將取代這些舊版媒體處理器。

Azure 媒體服務影片索引子建基於 Azure 媒體分析、Azure 認知搜尋、認知服務（例如臉部 API、Microsoft Translator、電腦視覺 API 和自訂語音服務）。 它可讓您使用影片索引器的影片與音訊模型，從影片中擷取見解。 若要查看影片索引子可以在哪些案例中使用、它所提供的功能，以及如何開始進行，請參閱[影片索引子的影片和音訊模型](../video-indexer/video-indexer-overview.md)。 

您可以使用[Azure 媒體服務 v3 分析器](../latest/analyzing-video-audio-files-concept.md)預設，或直接使用[影片索引子 api](https://api-portal.videoindexer.ai/)，從您的影片和音訊檔案中摘錄見解。 目前，影片索引子 Api 和媒體服務 v3 Api 所提供的功能有重迭。

> [!NOTE]
> 若要瞭解何時要使用影片索引子與媒體服務分析器預設值，請參閱[比較檔](../video-indexer/compare-video-indexer-with-media-services-presets.md)。 

本文討論從 Azure 媒體索引子和 Azure 媒體索引子2遷移至 Azure 媒體服務影片索引子的步驟。  

## <a name="migration-options"></a>移轉選項 

|如果您需要  |然後在受影響的網域控制站上執行 |
|---|---|
|針對隱藏式輔助字幕檔案格式的任何媒體檔案格式，提供語音轉換文字轉譯的解決方案： VTT、SRT 或 TTML<br/>還有其他的音訊深入資訊，例如關鍵字、主題推斷、聲場活動、喇叭 diarization、實體的提取和轉譯。| 透過影片索引子 v2 REST API 或 Azure 媒體服務 v3 音訊分析器預設值，將您的應用程式更新為使用 Azure 影片索引子功能。|
|語音轉換文字功能| 直接使用認知服務語音 API。|  

## <a name="getting-started-with-video-indexer"></a>開始使用影片索引子

下一節會將您指向相關的連結：[如何開始使用影片索引子？](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>開始使用媒體服務 v3 Api

Azure 媒體服務 v3 API 可讓您透過[Azure 媒體服務 v3 分析器](../latest/analyzing-video-audio-files-concept.md)預設值，從您的影片和音訊檔案中摘錄見解。 

**AudioAnalyzerPreset** 可讓您從音訊檔案或視訊檔案擷取多個音訊深入資訊。 輸出包含音訊文字記錄的 VTT 或 TTML 檔案，以及 JSON 檔案（包含所有其他的音訊深入解析）。 音訊深入解析包含關鍵字、說話者索引和語音情感分析。 AudioAnalyzerPreset 也支援特定語言的語言偵測。 如需詳細資訊，請參閱[轉換](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)。

### <a name="get-started"></a>開始使用

若要開始使用，請參閱：

* [教學課程](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset 範例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)或[.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset 範例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)或[.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>認知服務語音服務入門

[Azure 認知服務](https://docs.microsoft.com/azure/cognitive-services/)提供語音轉換文字服務，可即時可將音訊串流至文字，讓您的應用程式、工具或裝置可以取用或顯示。 您可以使用語音轉換文字[來自訂您自己的聲場模型、語言模型或發音模型](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)。 如需詳細資訊，請參閱[認知服務語音轉換文字](../../cognitive-services/speech-service/speech-to-text.md)。 

> [!NOTE] 
> 語音轉換文字服務不會採用影片檔案格式，而且只會採用[特定的音訊格式](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats)。 

如需文字轉換語音服務以及如何開始使用的詳細資訊，請參閱[什麼是語音轉換文字？](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>與已淘汰服務的已知差異 

您會發現影片索引子、Azure 媒體服務 v3 AudioAnalyzerPreset 和認知服務語音服務服務較可靠，並產生比淘汰的 Azure 媒體索引子1和 Azure 媒體索引子2處理器更佳的品質輸出。  

一些已知的差異包括： 

* 認知服務語音服務不支援關鍵字解壓縮。 不過，影片索引子和媒體服務 v3 AudioAnalyzerPreset 都提供一組更健全的 JSON 檔案格式關鍵字。 

## <a name="need-help"></a>需要協助嗎？

您可以藉由瀏覽至[新增支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來開啟支援票證

## <a name="next-steps"></a>後續步驟

* [舊版元件](legacy-components.md)
* [定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)


