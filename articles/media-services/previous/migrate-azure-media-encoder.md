---
title: 從 Azure 媒體編碼器遷移至媒體編碼器標準 |Microsoft Docs
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
ms.date: 08/21/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 32eab0f729280ad25f45853e05ea982b60a6d9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259331"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>從 Azure 媒體編碼器遷移至媒體編碼器標準

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

本文將討論從舊版 Azure 媒體編碼器 (AME) 媒體處理器 (（即將淘汰) 至媒體編碼器標準媒體處理器）的步驟。 如需淘汰日期，請參閱此[舊版元件](legacy-components.md)主題。

以 AME 編碼檔案時，客戶通常會使用名為的預設字串（例如） `H264 Adaptive Bitrate MP4 Set 1080p` 。 為了遷移，您的程式碼必須更新為使用 **媒體編碼器標準** 媒體處理器，而不是使用 AME，而其中一個對等的 [系統](media-services-mes-presets-overview.md) 預設值（例如） `H264 Multiple Bitrate 1080p` 。 

## <a name="migrating-to-media-encoder-standard"></a>遷移至媒體編碼器標準

以下是使用舊版媒體處理器的一般 c # 程式碼範例。 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
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

如果您已使用其架構為 AME 建立自己的編碼預設值，則會有 [對等的媒體編碼器標準架構](media-services-mes-schema.md)。 如果您有關于如何將較舊的設定對應到新編碼器的問題，請透過 mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>已知的差異 

媒體編碼器標準更穩定、可靠、具有更佳的效能，並產生比舊版的 AME 編碼器更佳的品質輸出。 其他情況： 

* 媒體編碼器標準產生具有不同命名慣例的輸出檔。
* 媒體編碼器標準會產生成品，例如包含 [輸入檔中繼資料](media-services-input-metadata-schema.md) 的檔案，以及 [ (s) 中繼資料的輸出](media-services-output-metadata-schema.md)檔案。

## <a name="next-steps"></a>後續步驟

* [舊版元件](legacy-components.md)
* [定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)
