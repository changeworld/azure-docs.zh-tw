---
title: 從 Windows Azure 媒體編碼器遷移到媒體編碼器標準 |微軟文檔
description: 本主題討論如何從 Azure 媒體編碼器遷移到媒體編碼器標準媒體處理器。
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
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513196"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>從 Windows Azure 媒體編碼器遷移到媒體編碼器標準

本文討論從舊版 Windows Azure 媒體編碼器 （WAME） 媒體處理器（正在停用）遷移到媒體編碼器標準媒體處理器的步驟。 有關停用日期，請參閱此[遺留元件](legacy-components.md)主題。

使用 WAME 對檔進行編碼時，客戶通常使用指定的預設`H264 Adaptive Bitrate MP4 Set 1080p`字串，如 。 為了遷移，需要更新代碼才能使用**媒體編碼器標準**媒體處理器而不是 WAME，以及等效[的系統預設](media-services-mes-presets-overview.md)之一，如`H264 Multiple Bitrate 1080p`。 

## <a name="migrating-to-media-encoder-standard"></a>遷移到媒體編碼器標準

下面是使用舊元件的典型 C# 代碼示例。 

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

下面是使用媒體編碼器標準的更新版本。

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

如果您已使用其架構為 WAME 創建了自己的編碼預設，則[媒體編碼器標準存在等效架構](media-services-mes-schema.md)。

## <a name="known-differences"></a>已知差異 

介質編碼器標準比傳統的 WAME 編碼器更堅固、更可靠、性能更好、輸出品質更好。 另外： 

* 媒體編碼器標準生成具有與 WAME 不同的命名約定的輸出檔案。
* 媒體編碼器標準生成專案，如包含[輸入檔中繼資料](media-services-input-metadata-schema.md)和[輸出檔案中繼資料](media-services-output-metadata-schema.md)的檔。
* 如[定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)（尤其是常見問題解答部分）中所述，當您使用媒體編碼器標準對視頻進行編碼時，將根據作為輸出生成的檔的持續時間計費。 使用 WAME，將根據輸入視頻檔的大小對您進行計費，並輸出視頻檔。

## <a name="need-help"></a>需要協助嗎？

您可以藉由瀏覽至[新增支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)來開啟支援票證

## <a name="next-steps"></a>後續步驟

* [舊版元件](legacy-components.md)
* [定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)
