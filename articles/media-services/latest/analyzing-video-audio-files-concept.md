---
title: 分析視頻和音訊檔
titleSuffix: Azure Media Services
description: 瞭解如何在 Azure 媒體服務中使用音訊Analyzer預設和視頻分析預設分析音訊和視頻內容。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269883"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>使用 Azure 媒體服務分析視頻和音訊檔

Azure 媒體服務 v3 允許您使用視頻索引子從視頻和音訊檔中獲取見解。 本文介紹了用於提取這些見解的媒體服務 v3 分析器預設。 若您希望獲得更多詳細深入解析，請直接使用影片索引器。 要瞭解何時使用視頻索引子與媒體服務分析器預設，請查看[比較文檔](../video-indexer/compare-video-indexer-with-media-services-presets.md)。

要使用媒體服務 v3 預設分析內容，請創建**一個轉換**並提交使用這些預設之一的**作業**：[視頻分析器預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)或**音訊分析預設**。 有關演示如何使用**視頻分析器預設**的教程，請參閱[使用 Azure 媒體服務分析視頻](analyze-videos-tutorial-with-api.md)。

> [!NOTE]
> 使用視訊或音訊分析程式的預設時，可使用 Azure 入口網站將帳戶設定為擁有 10 個 S3 編碼保留單元。 如需詳細資訊，請參閱[調整媒體處理](media-reserved-units-cli-how-to.md)。

## <a name="compliance-privacy-and-security"></a>合規性、隱私權和安全性

重要提醒是，在使用影片索引子時，您必須遵守所有適用的法律，且您不得以違反他人權利或可能會對他人有害的方式使用影片索引子或任何其他 Azure 服務。 將任何影片 (包括任何生物特徵辨識資料) 上傳至影片索引子服務以進行處理和儲存之前，您必須擁有所有適當的權限，包括向影片中的個人徵得所有必要的同意。 若要了解影片索引子中的合規性、隱私權和安全性，請參閱 Microsoft [認知服務條款](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)。 如需 Microsoft 的隱私權義務和您的資料處理方式，請參閱 Microsoft 的 [隱私權聲明](https://privacy.microsoft.com/PrivacyStatement)、[線上服務條款](https://www.microsoft.com/licensing/product-licensing/products) ("OST") 和 [資料處理增補](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")。 其他隱私權資訊 (包括資料保留、刪除/銷毀) 可在 OST 中和[這裡](../video-indexer/faq.md)取得。 一旦使用影片索引子，即表示您同意受到認知服務條款、OST、DPA 和隱私權聲明的規範。

## <a name="built-in-presets"></a>內建預設

媒體服務目前支援下列內建的分析器預設：  

|**預設名稱**|**案例**|**詳細資料**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|分析音訊|此預設會套用一組預先定義的 AI 型分析作業，包括語音轉譯。 此預設目前支援處理具有單一音訊播放軌 (包含單一語言的語音) 的內容。 您可以使用「語言標記-區域」的 BCP-47 格式，為輸入中的音訊承載指定語言。 支援的語言包括英語（"en-US"和"en-GB"）、西班牙文（"es-ES"和"es-MX"）、法語（"fr-FR"）、義大利文（"it-IT"）、日語（"ja-JP"）、葡萄牙文（"pt-BR"）、中文（"zh-CN"）、德語（"de-DE"）、阿拉伯文（"ar-EG"和"ar-SY"）、俄語（"ar-A"和"ar-SY"）。俄語（"ar-A"）和"ar-SY"。印度文（"HI-IN"）和韓語（"ko-KR"）。<br/><br/> 如果未指定語言或設置為 null，則自動語言檢測將選擇檢測到的第一語言，並在檔持續時間內繼續使用所選語言。 自動語言偵測功能目前支援英文、簡體中文、法文、德文、義大利文、日文、西班牙文、俄文和葡萄牙文。 它不支援在檢測到第一語言後在語言之間動態切換。 搭配語音清晰的錄音時，自動語言偵測功能的效果最好。 如果自動語言檢測找不到語言，則轉錄將回退到英語。|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|分析音訊和視訊|從音訊和視訊擷取見解 (豐富的中繼資料)，並輸出 JSON 格式檔案。 您可以指定在處理視訊檔案時，是否只想擷取音訊見解。 如需詳細資訊，請參閱[分析視訊](analyze-videos-tutorial-with-api.md)。|
|[人臉檢測器預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|檢測視頻中存在的人臉|描述分析視頻以檢測存在的所有面時要使用的設置。|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

預設可讓您從音訊檔案或視訊檔案擷取多個音訊深入資訊。 輸出會包含 JSON 檔案 (包含所有的深入資訊) 和音訊文字記錄的 VTT 檔案。 此預設接受的屬性會指定輸入檔案的語言 ([BCP47](https://tools.ietf.org/html/bcp47) 字串形式)。 音訊的深入資訊包括：

* **音訊轉錄**：帶時間戳記的口語的腳本。 支援多種語言。
* **揚聲器索引**：揚聲器和相應口語的映射。
* **言語情緒分析**：對音訊轉錄進行情緒分析的輸出。
* **關鍵字**：從音訊轉錄中提取的關鍵字。

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

預設可讓您從影片檔案擷取多個音訊和影片深入資訊。 輸出包含 JSON 檔案 (包含所有的深入資訊)、視訊文字記錄的 VTT 檔案，以及縮圖的集合。 此預設也接受 [BCP47](https://tools.ietf.org/html/bcp47) 字串 (表示視訊的語言) 當作屬性。 視訊深入資訊包含上述的音訊深入資訊和下列其他項目：

* **人臉跟蹤**：視頻中存在人臉的時間。 每個面都有一個面 ID 和相應的縮略圖集合。
* **可視文本**：通過光學字元辨識檢測到的文本。 文本是時間戳記，也用於提取關鍵字（除了音訊腳本）。
* **關鍵幀**：從視頻中提取的關鍵幀的集合。
* **視覺內容適度**：在本質上標記為成人或花哨的視頻部分。
* **注釋**：基於預定義物件模型對視頻進行批註的結果

## <a name="insightsjson-elements"></a>insights.json 元素

輸出包括 JSON 檔 （insights.json），其中包含視頻或音訊中的所有見解。 JSON 可能包含以下元素：

### <a name="transcript"></a>文字記錄

|名稱|描述|
|---|---|
|id|行識別碼。|
|text|文字記錄本身。|
|語言|文字記錄語言。 用於支援文字記錄，其中每一行可以有不同的語言。|
|執行個體|這一行曾出現的時間範圍清單。 如果執行個體是文字記錄，它只能有 1 個執行個體。|

範例：

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|名稱|描述|
|---|---|
|id|OCR 行識別碼。|
|text|OCR 文字。|
|信賴度|辨識信賴。|
|語言|OCR 語言。|
|執行個體|此 OCR 曾出現的時間範圍清單 (相同的 OCR 可以出現多次)。|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>臉部

|名稱|描述|
|---|---|
|id|臉部識別碼。|
|NAME|臉部名稱。 它可以是"未知#0"、身份識別的名人或客戶培訓的人員。|
|信賴度|臉部識別信賴。|
|description|名人的描述。 |
|thumbnailId|該臉部的縮圖識別碼。|
|knownPersonId|內部 ID（如果是已知人員）。|
|referenceId|必應 ID（如果是必應名人）。|
|referenceType|目前只是 Bing。|
|title|標題（如果是名人，例如"微軟的首席執行官"）。|
|imageUrl|圖像 URL，如果是名人。|
|執行個體|人臉出現在給定時間範圍內的實例。 每個執行個體也會有 thumbnailsId。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>擷取畫面

|名稱|描述|
|---|---|
|id|擷取畫面識別碼。|
|keyFrames|擷取畫面的主要畫面清單 (每個主要畫面都有一個識別碼和執行個體的時間範圍清單)。 主要畫面格執行個體中有縮圖識別碼欄位，其中包含主要畫面格的縮圖識別碼。|
|執行個體|此擷取畫面的時間範圍清單 (擷取畫面只能有 1 個執行個體)。|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>統計資料

|名稱|描述|
|---|---|
|CorrespondenceCount|影片中的對應數目。|
|WordCount|每個說話者的字數。|
|SpeakerNumberOfFragments|說話者在影片中的片段數量。|
|SpeakerLongestMonolog|說話者最長的獨白。 如果揚聲器在單聲道內有靜音，則包含它。 獨白開頭和結尾的無聲部分則會被移除。|
|SpeakerTalkToListenRatio|將說話者獨白的時間 (不含無聲的部分) 除以影片的總時間長度。 時間會四捨五入至小數點第三位。|


### <a name="sentiments"></a>人氣

人氣會依據其 sentimentType 欄位 (Positive/Neutral/Negative) 加以彙總。 例如：0-0.1、0.1-0.2。

|名稱|描述|
|---|---|
|id|人氣識別碼。|
|averageScore |所有該人氣類型執行個體的總分平均值 - Positive/Neutral/Negative|
|執行個體|此人氣曾出現的時間範圍清單。|
|sentimentType |類型可以是「正面」、「中性」或「負面」。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>標籤

|名稱|描述|
|---|---|
|id|標籤識別碼。|
|NAME|標籤名稱 (例如，電腦、電視)。|
|語言|標籤名稱語言 (轉譯時)。 BCP-47|
|執行個體|此標籤曾出現的時間範圍清單 (同一個標籤可以出現多次)。 每個執行個體都有一個信賴度欄位。 |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>關鍵字

|名稱|描述|
|---|---|
|id|關鍵字識別碼。|
|text|關鍵字。|
|信賴度|關鍵字的辨識信賴。|
|語言|關鍵字語言 (轉譯時)。|
|執行個體|此關鍵字曾出現的時間範圍清單 (同一個關鍵字可以出現多次)。|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 區塊包含影片索引器偵測到可能含有成人內容的時間範圍。 如果視覺化內容適度為空，則沒有標識成人內容。

經發現含有成人或猥褻內容的影片，只能供私人檢視。 使用者可以提交內容人工審閱請求，在這種情況下，`IsAdult`屬性將包含人工審閱的結果。

|名稱|描述|
|---|---|
|id|視覺內容仲裁識別碼。|
|adultScore|成人分數 (由內容仲裁提供)。|
|racyScore|辛辣分數 (由內容仲裁提供)。|
|執行個體|視覺內容仲裁出現的時間範圍清單。|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>後續步驟

[教學課程：使用 Azure 媒體服務分析視訊](analyze-videos-tutorial-with-api.md)
