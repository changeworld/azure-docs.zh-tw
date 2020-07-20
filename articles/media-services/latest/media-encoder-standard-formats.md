---
title: 標準編碼器格式和編解碼器-Azure
description: 本文件包含您可以藉由 StandardEncoderPreset 使用的最常見匯入和匯出檔案格式列出的清單。
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
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79251358"
---
# <a name="standard-encoder-formats-and-codecs"></a>標準編碼器格式和編解碼器

本文件包含您可以藉由 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 使用的最常見匯入和匯出檔案格式列出的清單。 如需如何使用 **StandardEncoderPreset** 建立自訂預設設定的詳細資訊，請參閱[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)。

## <a name="input-containerfile-formats"></a>輸入容器/檔案格式

| 檔案格式 (副檔名) | 支援 |
| --- | --- |
| FLV (使用 H.264 和 AAC 轉碼器) (.flv) |Yes |
| MXF    (.mxf) |Yes |
| GXF    (.gxf) |Yes |
| MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、.3gpp、.mpg) |Yes |
| Windows Media 視訊 (WMV)/ASF (.wmv、.asf) |Yes |
| AVI (未壓縮 8 位元/10 位元) (.avi) |Yes |
| MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |Yes |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

### <a name="audio-formats-in-input-containers"></a>輸入容器中的音訊格式

標準編碼器支援在輸入容器中攜帶下列音訊格式：

* MXF、GXF 和 QuickTime 檔案，其具有交錯立體聲或 5.1 範例的音訊音軌

或

* MXF、GXF 及 QuickTime 檔案，其中該音訊當做個別的 PCM 曲目攜帶，但可從檔案中繼資料推算通道對應 (立體聲或 5.1)

## <a name="input-video-codecs"></a>輸入視訊轉碼器
| 輸入視訊轉碼器 | 支援 |
| --- | --- |
| AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |8 位元 4:2:0 和 4:2:2 |
| Avid DNxHD (使用 MXF) |Yes |
| DVCPro/DVCProHD (使用 MXF) |Yes |
| 數位視訊 (DV) (使用 AVI 檔案) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種) |最高 422 設定檔 |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |No |
| Mpeg-4 第 2 部分 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 未壓縮或夾層 |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H.265| 主要設定檔|

## <a name="input-audio-codecs"></a>輸入音訊轉碼器
| 輸入音訊轉碼器 | 支援 |
| --- | --- |
| AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1) |Yes |
| MPEG Layer 2 |Yes |
| MP3 (MPEG-1 音訊層 3) |Yes |
| Windows Media 音訊 |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (可變多速率) |Yes |
| AES (SMPTE 331M 和 302M，AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>輸出格式和轉碼器
下表會列出支援匯出的轉碼器和檔案格式清單。

| 檔案格式 | 視訊轉碼器 | 音訊轉碼器 |
| --- | --- | --- |
| MP4 <br/><br/>(包括多位元速率 MP4 容器) |H.264 (高、主要和基準設定檔) |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264 (高、主要和基準設定檔) |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="next-steps"></a>後續步驟

[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)
