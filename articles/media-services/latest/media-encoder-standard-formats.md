---
title: 標準編碼器格式和編解碼器-Azure
description: 本文件包含您可以藉由 StandardEncoderPreset 使用的最常見匯入和匯出檔案格式列出的清單。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: f6e9c0a3d9100bc447314f5874107363c6bf5d8a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289438"
---
# <a name="standard-encoder-formats-and-codecs"></a>標準編碼器格式和編解碼器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文件包含您可以藉由 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 使用的最常見匯入和匯出檔案格式列出的清單。 如需如何使用 **StandardEncoderPreset** 建立自訂預設設定的詳細資訊，請參閱[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)。

## <a name="input-containerfile-formats"></a>輸入容器/檔案格式

| 檔案格式 (副檔名) | 支援 |
| --- | --- |
| FLV (使用 H.264 和 AAC 轉碼器) (.flv) |是 |
| MXF    (.mxf) |是 |
| GXF    (.gxf) |是 |
| MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、.3gpp、.mpg) |是 |
| Windows Media 視訊 (WMV)/ASF (.wmv、.asf) |是 |
| AVI (未壓縮 8 位元/10 位元) (.avi) |是 |
| MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |是 |
| [Microsoft Digital Video Recording (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |是 |
| Matroska/WebM (.mkv) |是 |
| WAVE/WAV (.wav) |是 |
| QuickTime (.mov) |是 |

### <a name="audio-formats-in-input-containers"></a>輸入容器中的音訊格式

標準編碼器支援在輸入容器中攜帶下列音訊格式：

* MXF、GXF 和 QuickTime 檔案，其具有交錯立體聲或 5.1 範例的音訊音軌

或

* MXF、GXF 及 QuickTime 檔案，其中該音訊當做個別的 PCM 曲目攜帶，但可從檔案中繼資料推算通道對應 (立體聲或 5.1)

## <a name="input-video-codecs"></a>輸入視訊轉碼器
| 輸入視訊轉碼器 | 支援 |
| --- | --- |
| AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |8 位元 4:2:0 和 4:2:2 |
| Avid DNxHD (使用 MXF) |是 |
| DVCPro/DVCProHD (使用 MXF) |是 |
| 數位視訊 (DV) (使用 AVI 檔案) |是 |
| JPEG 2000 |是 |
| MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種) |最高 422 設定檔 |
| MPEG-1 |是 |
| VC-1/WMV9 |是 |
| Canopus HQ/HQX |否 |
| Mpeg-4 第 2 部分 |是 |
| [Theora](https://en.wikipedia.org/wiki/Theora) |是 |
| YUV420 未壓縮或夾層 |是 |
| Apple ProRes 422 |是 |
| Apple ProRes 422 LT |是 |
| Apple ProRes 422 HQ |是 |
| Apple ProRes Proxy |是 |
| Apple ProRes 4444 |是 |
| Apple ProRes 4444 XQ |是 |
| HEVC/H.265| 主要設定檔|

## <a name="input-audio-codecs"></a>輸入音訊轉碼器
| 輸入音訊轉碼器 | 支援 |
| --- | --- |
| AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1) |是 |
| MPEG Layer 2 |是 |
| MP3 (MPEG-1 音訊層 3) |是 |
| Windows Media 音訊 |是 |
| WAV/PCM |是 |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |是 |
| [作品](https://go.microsoft.com/fwlink/?LinkId=822667) |是 |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |是 |
| AMR (可變多速率) |是 |
| AES (SMPTE 331M 和 302M，AES3-2003) |否 |
| Dolby® E |否 |
| Dolby® Digital (AC3) |否 |
| Dolby® Digital Plus (E-AC3) |否 |

## <a name="output-formats-and-codecs"></a>輸出格式和轉碼器
下表會列出支援匯出的轉碼器和檔案格式清單。

| 檔案格式 | 視訊轉碼器 | 音訊轉碼器 |
| --- | --- | --- |
| MP4 <br/><br/>(包括多位元速率 MP4 容器) |H.264 (高、主要和基準設定檔) |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264 (高、主要和基準設定檔) |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="next-steps"></a>接下來的步驟

[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)
