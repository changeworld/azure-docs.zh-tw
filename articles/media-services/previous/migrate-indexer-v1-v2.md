---
title: 從索引子 v1 和 v2 遷移到 Azure 媒體服務視頻索引子 |微軟文檔
description: 本主題討論如何從 Azure 媒體索引子 v1 和 v2 遷移到 Azure 媒體服務視頻索引子。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513230"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>從媒體索引子和媒體索引子 2 遷移到視頻索引子

[Azure 媒體索引子](media-services-index-content.md)媒體處理器和[Azure 媒體索引子 2 預覽](media-services-process-content-with-indexer2.md)媒體處理器正在停用。 有關停用日期，請參閱此[遺留元件](legacy-components.md)主題。 [Azure 媒體服務視頻索引子](https://docs.microsoft.com/azure/media-services/video-indexer/)將替換這些舊媒體處理器。

Azure 媒體服務視頻索引子基於 Azure 媒體分析、Azure 認知搜索、認知服務（如人臉 API、Microsoft 翻譯器、電腦視覺 API 和自訂語音服務）構建。 它可讓您使用影片索引器的影片與音訊模型，從影片中擷取見解。 要查看視頻索引子可以使用哪些方案、它提供哪些功能以及如何入門，請參閱[視頻索引子視頻和音訊模型](../video-indexer/video-indexer-overview.md)。 

您可以使用[Azure 媒體服務 v3 分析器預設](../latest/analyzing-video-audio-files-concept.md)或直接使用[視頻索引子 API](https://api-portal.videoindexer.ai/)從視頻和音訊檔中獲取見解。 目前，視頻索引子 API 和媒體服務 v3 API 提供的功能之間存在重疊。

> [!NOTE]
> 要瞭解何時要使用視頻索引子與媒體服務分析器預設，請查看[比較文檔](../video-indexer/compare-video-indexer-with-media-services-presets.md)。 

本文討論從 Azure 媒體索引子和 Azure 媒體索引子 2 遷移到 Azure 媒體服務視頻索引子的步驟。  

## <a name="migration-options"></a>移轉選項 

|如果您需要  |然後在受影響的網域控制站上執行 |
|---|---|
|以隱藏字幕檔案格式為任何媒體檔案格式提供語音到文本轉錄的解決方案：VTT、SRT 或 TTML<br/>以及其他音訊見解，例如：關鍵字、主題推斷、聲學事件、揚聲器分化、實體提取和翻譯| 通過視頻索引子 v2 REST API 或 Azure 媒體服務 v3 音訊分析器預設更新應用程式以使用 Azure 視頻索引子功能。|
|語音到文本功能| 直接使用認知服務語音 API。|  

## <a name="getting-started-with-video-indexer"></a>開始使用視頻索引子

以下部分將您指向相關連結：[如何開始使用視頻索引子？](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>開始使用媒體服務 v3 API

Azure 媒體服務 v3 API 使您能夠通過[Azure 媒體服務 v3 分析器預設](../latest/analyzing-video-audio-files-concept.md)從視頻和音訊檔中提取見解。 

**AudioAnalyzerPreset** 可讓您從音訊檔案或視訊檔案擷取多個音訊深入資訊。 輸出包括音訊腳本的 VTT 或 TTML 檔和 JSON 檔（以及所有其他音訊見解）。 音訊見解包括關鍵字、揚聲器索引和語音情緒分析。 音訊分析器預設還支援特定語言的語言檢測。 有關詳細資訊，請參閱[轉換](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)。

### <a name="get-started"></a>開始使用

若要開始使用，請參閱：

* [教學課程](../latest/analyze-videos-tutorial-with-api.md)
* 音訊分析器預設示例[：JAVA SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)或[.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* 視頻分析器預設示例[：JAVA SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)或[.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>開始認知服務語音服務

[Azure 認知服務](https://docs.microsoft.com/azure/cognitive-services/)提供語音到文本服務，該服務可即時將音訊流轉錄到文本，而應用程式、工具或設備可以使用或顯示這些文本。 您可以使用語音到文本自訂[您自己的聲學模型、語言模型或發音模型](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)。 有關詳細資訊，請參閱[認知服務語音到文本](../../cognitive-services/speech-service/speech-to-text.md)。 

> [!NOTE] 
> 語音到文本服務不採用視頻檔案格式，只採用[某些音訊格式](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats)。 

有關文本到語音服務以及如何開始使用的詳細資訊，請參閱[什麼是語音到文本？](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>與棄用服務的已知差異 

您會發現，視頻索引子、Azure 媒體服務 v3 音訊Analyzer預設和認知服務語音服務服務比已停用的 Azure 媒體索引子 1 和 Azure 媒體索引子 2 處理器更可靠，並生成更好的高品質輸出。  

一些已知的差異包括： 

* 認知服務語音服務不支援關鍵字提取。 但是，視頻索引子和媒體服務 v3 音訊分析器預設都提供了一組更強大的 JSON 檔案格式的關鍵字。 

## <a name="need-help"></a>需要協助嗎？

您可以藉由瀏覽至[新增支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來開啟支援票證

## <a name="next-steps"></a>後續步驟

* [舊版元件](legacy-components.md)
* [定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)


