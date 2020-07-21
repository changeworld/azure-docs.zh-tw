---
title: Azure 隨選媒體編碼器的比較 | Microsoft Docs
description: 本主題比較**媒體編碼器標準**和**媒體編碼器高階工作流程**的編碼功能。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: ca17ffdc2a54b25bbfd855834a366ed381a54678
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530383"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Azure 隨選媒體編碼器的比較  

本主題比較**媒體編碼器標準**和**媒體編碼器高階工作流程**的編碼功能。

## <a name="video-and-audio-processing-capabilities"></a>視訊和音訊處理功能

下表比較媒體編碼器標準 (MES) 和媒體編碼器高階工作流程 (MEPW) 之間的功能。 

|功能|Media Encoder Standard|媒體編碼器高階工作流程|
|---|---|---|
|編碼時套用條件式邏輯<br/>(例如，如果輸入是 HD，則編碼 5.1 音訊)|否|是|
|隱藏式輔助字幕|否|[是](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[杜比 &reg; 專業音量更正](https://professional.dolby.com/product/broadcast/vm600/)<br/> 使用對話智慧&trade;|否|是|
|非交錯顯示、反轉電影|基本|廣播品質|
|偵測並移除黑色框線 <br/>(垂直黑邊、上下黑邊)|否|是|
|縮圖產生|[是](media-services-dotnet-generate-thumbnail-with-mes.md)|[是](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|裁剪/修剪和拼接視訊|[是](media-services-advanced-encoding-with-mes.md#trim_video)|是|
|音訊或視訊的覆疊|[是](media-services-advanced-encoding-with-mes.md#overlay)|[是](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|圖形的覆疊|從影像來源|從影像和文字來源|
|多重音訊語言曲目|限制|[是](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>每個編碼器所使用的計費計量表
| 媒體處理器名稱 | 適用的價格 | 備忘稿 |
| --- | --- | --- |
| **Media Encoder Standard** |ENCODER |在 ENCODER 資料行底下，「編碼工作」會以[這裡][1]指定的費率，根據所有產生來輸出之媒體檔案的總持續時間 (以分鐘為單位) 計費。 |
| **媒體編碼器高階工作流程** |PREMIUM ENCODER |在 PREMIUM ENCODER 資料行底下，「編碼工作」會以[這裡][1]指定的費率，根據所有產生來輸出之媒體檔案的總持續時間 (以分鐘為單位) 計費。 |

## <a name="input-containerfile-formats"></a>輸入容器/檔案格式
| 輸入容器/檔案格式 | Media Encoder Standard | 媒體編碼器高階工作流程 |
| --- | --- | --- |
| Adobe &reg; Flash &reg; F4V |是 |是 |
| MXF/SMPTE 377M |是 |是 |
| GXF |是 |是 |
| MPEG-2 傳輸資料流 |是 |是 |
| MPEG-2 程式資料流 |是 |是 |
| MPEG-4/MP4 |是 |是 |
| Windows Media/ASF |是 |是 |
| AVI (未壓縮 8 位元/10 位元) |是 |是 |
| 3GPP/3GPP2 |是 |否 |
| Smooth Streaming 檔案格式 (PIFF 1.3) |是 |否 |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |是 |否 |
| Matroska/WebM |是 |否 |
| QuickTime (.mov) |是 |否 |

## <a name="input-video-codecs"></a>輸入視訊轉碼器
| 輸入視訊轉碼器 | Media Encoder Standard | 媒體編碼器高階工作流程 |
| --- | --- | --- |
| AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |8 位元 4:2:0 和 4:2:2 |是 |
| Avid DNxHD (使用 MXF) |是 |是 |
| DVCPro/DVCProHD (使用 MXF) |是 |是 |
| JPEG2000 |是 |是 |
| MPEG-2 （最多422的設定檔和高層級，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs 和 D10 等變體 &reg; ） |最高 422 設定檔 |是 |
| MPEG-1 |是 |是 |
| Windows Media 視訊/VC-1 |是 |是 |
| Canopus HQ/HQX |否 |否 |
| Mpeg-4 第 2 部分 |是 |否 |
| [Theora](https://en.wikipedia.org/wiki/Theora) |是 |否 |
| Apple ProRes 422 |是 |否 |
| Apple ProRes 422 LT |是 |否 |
| Apple ProRes 422 HQ |是 |否 |
| Apple ProRes Proxy |是 |否 |
| Apple ProRes 4444 |是 |否 |
| Apple ProRes 4444 XQ |是 |否 |
| HEVC/H.265|主要設定檔|主要和主要 10 設定檔|

## <a name="input-audio-codecs"></a>輸入音訊轉碼器
| 輸入音訊轉碼器 | Media Encoder Standard | 媒體編碼器高階工作流程 |
| --- | --- | --- |
| AES (SMPTE 331M 和 302M，AES3-2003) |否 |是 |
| 杜比 &reg; E |否 |是 |
| 杜比 &reg; 數位（AC3） |否 |是 |
| 杜比 &reg; 數位 Plus （E-AC3） |否 |是 |
| AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1) |是 |是 |
| MPEG Layer 2 |是 |是 |
| MP3 (MPEG-1 音訊層 3) |是 |是 |
| Windows Media 音訊 |是 |是 |
| WAV/PCM |是 |是 |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |是 |否 |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |是 |否 |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |是 |否 |

## <a name="output-containerfile-formats"></a>輸出容器/檔案格式
| 輸出容器/檔案格式 | Media Encoder Standard | 媒體編碼器高階工作流程 |
| --- | --- | --- |
| Adobe &reg; Flash &reg; F4V |否 |是 |
| MXF (OP1a、XDCAM 和 AS02) |否 |是 |
| DPP (包括 AS11) |否 |是 |
| GXF |否 |是 |
| MPEG-4/MP4 |是 |是 |
| MPEG-TS |是 |是 |
| Windows Media/ASF |否 |是 |
| AVI (未壓縮 8 位元/10 位元) |否 |是 |
| Smooth Streaming 檔案格式 (PIFF 1.3) |否 |是 |

## <a name="output-video-codecs"></a>輸出視訊轉碼器
| 輸出視訊轉碼器 | Media Encoder Standard | 媒體編碼器高階工作流程 |
| --- | --- | --- |
| AVC (H.264；8 位元；高達 High Profile、Level 5.2；4K Ultra HD；AVC Intra) |僅限 8 位元 4:2:0 |是 |
| HEVC (H.265；8 位元與 10 位元；)  |否 |是 |
| Avid DNxHD (使用 MXF) |否 |是 |
| MPEG-2 （最多422的設定檔和高層級，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs 和 D10 等變體 &reg; ） |否 |是 |
| MPEG-1 |否 |是 |
| Windows Media 視訊/VC-1 |否 |是 |
| JPEG 縮圖建立 |是 |是 |
| PNG 縮圖建立 |是 |是 |
| BMP 縮圖建立 |是 |否 |

## <a name="output-audio-codecs"></a>輸出音訊轉碼器
| 輸出音訊轉碼器 | Media Encoder Standard | 媒體編碼器高階工作流程 |
| --- | --- | --- |
| AES (SMPTE 331M 和 302M，AES3-2003) |否 |是 |
| 杜比 &reg; 數位（AC3） |否 |是 |
| &reg;高達7.1 的杜比數位 Plus （E-AC3） |否 |是 |
| AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1) |是 |是 |
| MPEG Layer 2 |否 |是 |
| MP3 (MPEG-1 音訊層 3) |否 |是 |
| Windows Media 音訊 |否 |是 |

>[!NOTE]
>如果您編碼為杜比 &reg; 數位（AC3），則只能將輸出寫入 ISO 檔案中的檔案。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相關文章
* [透過自訂 Media Encoder Standard 預設值來執行進階編碼工作](media-services-custom-mes-presets-with-dotnet.md)
* [配額和限制](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
