---
title: 從 Windows Azure 媒體編碼器遷移至媒體編碼器標準 |Microsoft Docs
description: 本主題討論如何從 Azure 媒體編碼器遷移至媒體編碼器標準媒體處理器。
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
ms.date: 10/17/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c1a9f98373ac872bb315c1ae621b3895760dc18
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013379"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>從 Windows Azure 媒體編碼器遷移至媒體編碼器標準

本文討論從舊版 Windows Azure 媒體編碼器 (WAME) 媒體處理器 (（即將淘汰) 至媒體編碼器標準媒體處理器）的步驟。 如需淘汰日期，請參閱此[舊版元件](legacy-components.md)主題。

使用 WAME 來編碼檔案時，客戶通常會使用名為的預設字串（例如） `H264 Adaptive Bitrate MP4 Set 1080p` 。 若要遷移，您的程式碼必須更新為使用 **媒體編碼器標準** 媒體處理器，而不是 WAME，以及其中一個對等的 [系統](media-services-mes-presets-overview.md) 預設值（例如） `H264 Multiple Bitrate 1080p` 。 

## <a name="migrating-to-media-encoder-standard"></a>遷移至媒體編碼器標準

以下是使用舊版元件的一般 c # 程式碼範例。 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

以下是使用媒體編碼器標準的更新版本。

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>進階案例 

如果您已使用其架構為 WAME 建立自己的編碼預設值，則會有 [對等的媒體編碼器標準架構](media-services-mes-schema.md)。

## <a name="known-differences"></a>已知的差異 

媒體編碼器標準更穩定、可靠、具有更佳的效能，並產生比舊版 WAME 編碼器更佳的品質輸出。 另外： 

* 媒體編碼器標準會產生與 WAME 不同的命名慣例的輸出檔案。
* 媒體編碼器標準會產生成品，例如包含 [輸入檔中繼資料](media-services-input-metadata-schema.md) 的檔案，以及 [ (s) 中繼資料的輸出](media-services-output-metadata-schema.md)檔案。
* 如 [定價] [頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding) 中所述 (特別是在 [常見問題] 區段中) ，當您使用媒體編碼器標準編碼影片時，會依據產生的檔案持續時間（以輸出形式）來計費。 使用 WAME 時，您會根據輸入影片檔案的大小來計費 (s) 和輸出影片檔案 (s) 。

## <a name="need-help"></a>需要協助嗎？

您可以藉由瀏覽至[新增支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來開啟支援票證

## <a name="next-steps"></a>後續步驟

* [舊版元件](legacy-components.md)
* [定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)
