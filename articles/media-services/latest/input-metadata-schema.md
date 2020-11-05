---
title: Azure 媒體服務 v3 輸入中繼資料架構
description: 本文概述 Azure 媒體服務 v3 輸入中繼資料架構。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1ca526b7ecbe20a54ec115521cdfbc93c713e0da
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360049"
---
# <a name="input-metadata"></a>輸入中繼資料

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

編碼作業會與您要在其上執行一些編碼工作的輸入資產相關聯。  完成工作時，就會產生輸出資產。 輸出資產包含影片、音訊、縮圖、資訊清單和其他檔案。 

輸出資產也包含隨附關於輸入資產中繼資料的檔案。 中繼資料 JSON 檔案的名稱具有隨機識別碼，請勿用來識別輸出資產所屬的輸入資產。 若要識別其所屬的輸入資產，請使用 `Uri` 欄位 (如需詳細資訊，請參閱) 的 [其他子項目](#other-child-elements) 。  

媒體服務不會事先掃描輸入資產來產生中繼資料。 在工作中處理輸入資產時，只會以成品的形式產生輸入中繼資料。 因此，此成品會寫入至輸出資產。 使用不同的工具來產生輸入資產和輸出資產的中繼資料。 因此，輸入中繼資料與輸出中繼資料會有稍微不同的結構描述。

本文討論 ) 所依據之輸入中繼資料 (asset_id_metadata.js的 JSON 架構元素和類型 &lt; &gt; 。 如需包含輸出資產相關中繼資料之檔案的相關資訊，請參閱 [輸出中繼資料](output-metadata-schema.md)。  

您可以在本文結尾處找到 JSON 架構範例。  

## <a name="assetfile"></a>AssetFile  

包含編碼作業的 AssetFile 元素集合。  

> [!NOTE]
> 下列 4 個子元素必須循序出現。  
> 
> 

| Name  | 描述 |
| --- | --- | 
| **VideoTracks**|每個實體資產檔案可以包含零個或多個交錯形成適當容器格式的視訊播放軌。 如需詳細資訊，請參閱 [VideoTracks](#videotracks)。 |
| **AudioTracks**|每個實體資產檔案可以包含零個或多個交錯形成適當容器格式的音訊播放軌。 如需詳細資訊，請參閱 [AudioTracks](#audiotracks) |
| **中繼資料**  |資產檔案的中繼資料 (以索引鍵\值字串表示)。 <br />例如：`<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>其他子項目

| Name | 描述 |
| --- | --- |
| **名稱**<br />必要 |資產檔案名稱。 <br /><br />範例： `"Name": "Ignite-short.mp4"` |
| **Uri**<br />必要 |輸入資產所在的 URL。 若要識別輸出資產所屬的輸入資產，請使用 `Uri` 欄位而非識別碼。|
| **大小**<br />必要 |資產檔案大小 (以位元組為單位)。  <br /><br />範例： `"Size": 75739259`|
| **有效期間**<br />必要 |內容播放持續時間。 <br /><br />範例： `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />必要 |資產檔案中的資產數目。  <br /><br />範例： `"NumberOfStreams": 2`|
| **FormatNames**<br />必要 |Format names.  <br /><br />範例： `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> 必要 |格式詳細資訊名稱。 <br /><br />範例： `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |內容開始時間。  <br /><br />範例： `"StartTime": "PT0S"` |
| **OverallBitRate** |資產檔案的平均位元速率（以位/秒為單位）。  <br /><br />範例： `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Name | 描述 |
| --- | --- |
| **FourCC**<br />必要 |Ffmpeg 所報告的影片編解碼器 FourCC 程式碼。<br /><br />範例： `"FourCC": "avc1"` |
| **設定檔** |視訊播放軌的設定檔。 <br /><br />範例： `"Profile": "Main"`|
| **Level** |視訊播放軌的層級。 <br /><br />範例： `"Level": "3.2"`|
| **PixelFormat** |視訊播放軌的像素格式。 <br /><br />範例： `"PixelFormat": "yuv420p"`|
| **寬度**<br />必要 |編碼的視訊寬度 (以像素為單位)。 <br /><br />範例： `"Width": "1280"`|
| **高度**<br />必要 |編碼的視訊高度 (以像素為單位)。<br /><br />範例： `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />必要 |視訊顯示長寬比的分子。<br /><br />範例： `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />必要 |視訊顯示長寬比的分母。 <br /><br />範例： `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |視訊樣本長寬比的分子。 <br /><br />範例： `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|範例： `"SampleAspectRatioDenominator": 1.0`|
| **頻**<br />必要 |測量的視訊畫面格速率 (採用 .3f 格式)。 <br /><br />範例： `"FrameRate": 29.970`|
| **Bitrate** |從資產檔案計算出來的平均影片位速率（以位/秒為單位）。 只會計算基本串流承載，而不會納入封裝負荷。 <br /><br />範例： `"Bitrate": 8421583`|
| **HasBFrames** |B 畫面格的視訊播放軌數目。 <br /><br />範例： `"HasBFrames": 2`|
| **中繼資料** |可以用來保存各種資訊的泛型索引鍵/值字串。 <br />請參閱本文結尾的完整範例。 |
| **識別碼**<br />必要 |此音訊或視訊播放軌之以零為起始的索引。<br /><br /> 此 **識別碼** 不一定是用於 MP4 檔案中的 TrackID。 <br /><br />範例： `"Id": 2`|
| **轉碼器** |視訊播放軌轉碼器字串。 <br /><br />範例： `"Codec": "h264"`|
| **CodecLongName** |音訊或視訊播放軌轉碼器長名稱。 <br /><br />範例： `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **轉碼器** |視訊播放軌轉碼器字串。 <br /><br />範例： `"Codec": "h264"`|
| **TimeBase**<br />必要 |時間基準。<br /><br />範例： `"TimeBase": "1/30000"`|
| **NumberOfFrames** |畫面格數 (針對視訊播放軌呈現)。 <br /><br />範例： `"NumberOfFrames": 2107`|
| **StartTime** |播放軌開始時間。<br /><br />範例： `"StartTime": "PT0.033S"` |
| **有效期間** |播放軌持續時間。 <br /><br />範例： `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Name  | 描述 |
| --- | --- | 
| **SampleFormat** |樣本格式。 <br /><br />範例： `"SampleFormat": "fltp"`|
| **ChannelLayout** |聲道配置。 <br /><br />範例： `"ChannelLayout": "stereo"`|
| **聲道**<br />必要 |音訊聲道數目 (0 個或多個)。 <br /><br />範例： `"Channels": 2`|
| **SamplingRate**<br />必要 |音訊取樣率 (每秒或每 Hz 的樣本數)。 <br /><br />範例： `"SamplingRate": 48000`|
| **Bitrate** |從資產檔案計算出來的平均音訊位元速率 (位元 / 秒)。 只會計算基本串流承載，而封裝負荷不會納入此計數中。 <br /><br />範例： `"Bitrate": 192080`|
| **中繼資料** |可以用來保存各種資訊的泛型索引鍵/值字串。  <br />請參閱本文結尾的完整範例。 |
| **識別碼**<br />必要 |此音訊或視訊播放軌之以零為起始的索引。<br /><br /> 這不一定使用於 MP4 檔中的 TrackID。 <br /><br />範例： `"Id": 1`|
| **轉碼器** |視訊播放軌轉碼器字串。 <br /><br />範例： `"Codec": "aac"`|
| **CodecLongName** |音訊或視訊播放軌轉碼器長名稱。 <br /><br />範例： `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />必要 |時間基準。<br /><br />範例： `"TimeBase": "1/48000"` |
| **NumberOfFrames** |畫面格數 (針對視訊播放軌呈現)。 <br /><br />範例： `"NumberOfFrames": 3294`|
| **StartTime** |播放軌開始時間。 如需詳細資訊，請參閱 [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html)。 <br /><br />範例： `"StartTime": "PT0S"` |
| **有效期間** |播放軌持續時間。 <br /><br />範例： `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>中繼資料

| 名稱 | 描述 |
| --- | --- |
| **key**<br />必要 |索引鍵/值組配對中的索引鍵。 |
| **value**<br /> 必要 |索引鍵/值組配對中的值。 |

## <a name="schema-example"></a>架構範例

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

[輸出中繼資料](output-metadata-schema.md)
