---
title: Azure 媒體服務輸出中繼資料結構描述 | Microsoft Docs
description: 本文提供 Azure 媒體服務輸出中繼資料架構的總覽。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2020
ms.author: juliako
ms.openlocfilehash: ce3d0a5beb5903d29b1deec345cf4673e3492e5d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080919"
---
# <a name="output-metadata"></a>輸出中繼資料

編碼作業會與您要在其上執行一些編碼工作的輸入資產相關聯。 例如，將 MP4 檔案編碼為 H.264 MP4 自動調整位元速率集、建立縮圖、建立疊加層。 完成工作時，就會產生輸出資產。  輸出資產包含影片、音訊、縮圖和其他檔案。 輸出資產也包含隨附關於輸出資產中繼資料的檔案。 中繼資料 JSON 檔案的名稱具有下列格式：（例如 `<source_file_name>_manifest.json` ， `BigBuckBunny_manifest.json` ）。 您應該掃描上的任何 * _metadata.js，並查詢中的 filepath 字串，以尋找來原始檔案名（不含截斷）。

媒體服務不會事先掃描輸入資產以產生中繼資料。 輸入中繼資料只會在工作中處理輸入資產時，產生為成品。 因此，此成品會寫入至輸出資產。 使用不同的工具來產生輸入資產和輸出資產的中繼資料。 因此，輸入中繼資料與輸出中繼資料會有稍微不同的結構描述。

本文討論 JSON 架構的元素和類型，其上的輸出中繼資料（ &lt; source_file_name &gt;_manifest.js）是以其為基礎。 <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

您可以在本文結尾處找到完整的架構程式碼和 JSON 範例。  

## <a name="assetfile"></a>AssetFile

編碼作業的 AssetFile 項目集合。  

| Name | 描述 |
| --- | --- |
| **根源** |為了產生此 AssetFile 所處理之輸入/來源媒體檔案的集合。<br />範例： `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|每個實體 AssetFile 都可以內含零或多個交錯形成適當容器格式的視訊播放軌。 <br />請參閱[VideoTracks](#videotracks)。 |
| **AudioTracks**|每個實體資產檔案可以內含零個或多個交錯形成適當容器格式的音訊播放軌。 這是所有音訊播放軌的集合。<br /> 如需詳細資訊，請參閱[AudioTracks](#audiotracks)。 |
| **名稱**<br />必要 |媒體資產檔案名稱。 <br /><br />範例： `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **大小**<br />必要 |資產檔案大小 (以位元組為單位)。 <br /><br />範例： `"Size": 32414631`|
| **有效期間**<br />必要 |內容播放持續時間。 如需詳細資訊，請參閱[ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html)格式。 <br /><br />範例： `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

每個實體 AssetFile 都可以內含零或多個交錯形成適當容器格式的視訊播放軌。 **VideoTracks** 元素代表所有視訊播放軌集合。  

| Name | 說明 |
| --- | --- |
| **識別碼**<br /> 必要 |此影片播放軌以零為起始的索引。**注意：** 此**識別碼**不一定是在用於 trackid 檔案中使用的。 <br /><br />範例： `"Id": 1`|
| **FourCC**<br />必要 | Ffmpeg 所報告的影片編解碼器 FourCC 程式碼。  <br /><br />範例： `"FourCC": "avc1"`|
| **設定檔** |H264 設定檔 (僅適用於 H264 轉碼器)。  <br /><br />範例： `"Profile": "High"` |
| **Level** |H264 層級 (僅適用於 H264 轉碼器)。  <br /><br />範例： `"Level": "3.2"`|
| **寬度**<br />必要 |編碼的視訊寬度 (以像素為單位)。  <br /><br />範例： `"Width": "1280"`|
| **高寬比**<br />必要 |編碼的視訊高度 (以像素為單位)。  <br /><br />範例： `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />必要|視訊顯示長寬比的分子。  <br /><br />範例： `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />必要 |視訊顯示長寬比的分母。  <br /><br />範例： `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />必要 |測量的視訊畫面格速率 (採用 .3f 格式)。  <br /><br />範例： `"Framerate": 29.970`|
| **Bitrate**<br />必要 |每秒的平均影片位元速率（以位為單位），如 AssetFile 所計算。 只會計算基本串流承載，而不會納入封裝負荷。  <br /><br />範例： `"Bitrate": 3551567`|
| **TargetBitrate**<br />必要 |此影片播放軌的目標平均位元速率（以每秒位數為單位的編碼預設值）。 <br /><br />範例： `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

每個實體資產檔案可以內含零個或多個交錯形成適當容器格式的音訊播放軌。 **AudioTracks** 元素代表所有這些音訊播放軌集合。  

| Name  | 說明 |
| --- | --- |
| **識別碼**<br />必要  |此音訊播放軌以零為起始的索引。**注意：** 這不一定是在用於 trackid 檔案中使用的。  <br /><br />範例： `"Id": 2`|
| **轉碼器**  |音訊播放軌轉碼器字串。  <br /><br />範例： `"Codec": "aac"`|
| **語言**|範例： `"Language": "eng"`|
| **聲道**<br />必要|音訊聲道數目。  <br /><br />範例： `"Channels": 2`|
| **SamplingRate**<br />必要 |音訊取樣率 (每秒或每 Hz 的樣本數)。  <br /><br />範例： `"SamplingRate": 48000`|
| **Bitrate**<br />必要 |從 AssetFile 計算出來的平均音訊位元速率 (位元 / 秒)。 只會計算基本串流承載，而不會納入封裝負荷。  <br /><br />範例： `"Bitrate": 128041`|

## <a name="json-schema-example"></a>JSON 結構描述範例

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

[從 HTTPS URL 建立工作輸入](job-input-from-http-how-to.md)
