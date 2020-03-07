---
title: 檢查 v2 API 所產生的影片索引子輸出-Azure
titleSuffix: Azure Media Services
description: 本主題會檢查 v2 API 所產生的 Azure 媒體服務影片索引子輸出。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: 2fac5e07f9646c4fc0fac7b1be53b5a5ac1ea803
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363923"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>檢查 API 所產生的影片索引子輸出

當您呼叫**取得影片索引** API 時，若回應狀態為「確定」，您取得的回應內容會是詳細的 JSON 輸出。 JSON 內容包含指定影片深入解析的詳細資料。 見解包括：文字記錄、Ocr、臉部、主題、區塊等。每個深入解析類型都包含時間範圍的實例，顯示影片中的深入解析出現的時間。 

1. 若要取出 JSON 檔案，請呼叫[取得影片索引 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. 如果您也對特定構件感興趣，請呼叫[取得影片成品下載 URL API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    在 API 呼叫中，指定要求的成品類型（OCR、臉部、主要畫面等等）。

您也可以透過視覺化方式檢查影片的摘要深入解析，只要在[影片索引子](https://www.videoindexer.ai/)網站上按影片的 [播放] 按鈕即可。 如需詳細資訊，請參閱[檢視和編輯影片的深入解析](video-indexer-view-edit.md)。

![深入解析](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

本文將審視**取得影片索引** API 所傳回的 JSON 內容。 

> [!NOTE]
> 影片索引子中所有存取權杖的到期時間皆為一小時。


## <a name="root-elements"></a>根元素

|名字|描述|
|---|---|
|accountId|播放清單的 VI 帳戶識別碼。|
|id|播放清單的識別碼。|
|name|播放清單的名稱。|
|description|播放清單的描述。|
|userName|建立播放清單的使用者名稱。|
|created|播放清單的建立時間。|
|privacyMode|播放清單的隱私模式 (私人/公用)。|
|state|播放清單 (已上傳、處理中、已處理、失敗、已隔離)。|
|isOwned|指出播放清單是否由目前的使用者所建立。|
|isEditable|指出目前的使用者是否有權編輯播放清單。|
|isBase|指出播放清單是基礎播放清單 (影片) 還是以其他影片組成的播放清單 (衍生)。|
|durationInSeconds|播放清單的持續時間總計。|
|summarizedInsights|包含一個 [summarizedInsights](#summarizedinsights)。
|videos|建構播放清單的[影片](#videos)清單。<br/>如果此播放清單是由其他影片的時間範圍建構而成 (衍生)，則此清單中的影片將只會包含所含時間範圍內的資料。|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

本節說明深入解析的摘要。

|屬性 | 描述|
|---|---|
|name|影片的名稱。 例如 Azure 監視器。|
|id|影片的識別碼。 例如 63c6d532ff。|
|privacyMode|您的明細可具有下列其中一個模式：**私人**、**公用**。 **公用** - 您帳戶中的所有人和具有影片連結的任何人都可看到影片。 **私人** - 只有您帳戶中的所有人可看到影片。|
|duration|包含一個持續時間，用以說明深入解析發生的時間。 持續時間以秒為單位。|
|thumbnailVideoId|從中取得縮圖的影片識別碼。
|thumbnailId|影片的縮圖識別碼。 若要取得實際的縮圖，請呼叫[get-縮圖](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail)，並將它傳遞給 ThumbnailVideoId 和 thumbnailId。|
|臉部|可包含零個或多個臉部。 如需詳細資訊，請參閱[臉部](#faces)。|
|關鍵字|可包含零個或多個關鍵字。 如需詳細資訊，請參閱[關鍵字](#keywords)。|
|人氣|可包含零個或多個情緒。 如需詳細資訊，請參閱[情緒](#sentiments)。|
|audioEffects| 可包含零個或多個 audioEffects。 如需詳細資訊，請參閱 [audioEffects](#audioEffects)。|
|標籤| 可包含零個或多個標籤。 如需詳細資訊，請參閱[標籤](#labels)。|
|brands| 可包含零個或多個品牌。 如需詳細資訊，請參閱[品牌](#brands)。|
|統計資料 | 如需詳細資訊，請參閱[統計資料](#statistics)。|
|emotions| 可包含零個或多個表情。 如需詳細資訊，請參閱[表情](#emotions)。|
|topics|可包含零個或多個主題。 [主題](#topics)深入解析。|

## <a name="videos"></a>videos

|名字|描述|
|---|---|
|accountId|影片的 VI 帳戶識別碼。|
|id|影片的識別碼。|
|name|影片的名稱。
|state|影片的狀態 (已上傳、處理中、已處理、失敗、已隔離)。|
|processingProgress|處理期間的處理進度 (例如 20%)。|
|failureCode|無法處理時顯示的失敗碼 (例如 'UnsupportedFileType')。|
|failureMessage|無法處理時顯示的失敗訊息。|
|externalId|影片的外部識別碼 (如果使用者已指定)。|
|externalUrl|影片的外部 URL (如果使用者已指定)。|
|中繼資料|影片的外部中繼資料 (如果使用者已指定)。|
|isAdult|指出是否已手動檢閱影片並識別為成人影片。|
|insights|深入解析物件。 如需詳細資訊，請參閱[深入解析](#insights)。|
|thumbnailId|影片的縮圖識別碼。 若要取得實際的縮圖，請呼叫[取得縮圖](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail)，並將影片識別碼和 thumbnailId 傳遞給它。|
|publishedUrl|串流影片的 URL。|
|publishedUrlProxy|影片串流來源的 URL (適用於 Apple 裝置)。|
|viewToken|用來串流影片的短期檢視權杖。|
|sourceLanguage|影片的來源語言。|
|語言|影片的實際語言 (翻譯)。|
|indexingPreset|用來編製影片索引的預設值。|
|streamingPreset|用來發佈影片的預設值。|
|linguisticModelId|用來謄寫影片的 CRIS 模型。|
|統計資料 | 如需詳細資訊，請參閱[統計資料](#statistics)。|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

每個深入解析（例如，文字記錄行、臉部、品牌等等）都包含唯一元素的清單（例如，face1、face2、face3），而每個元素都有自己的中繼資料和其實例清單（這是具有其他選擇性中繼資料的時間範圍）。

臉部可以有識別碼、名稱、縮圖、其他中繼資料，以及其時態性執行個體的清單 (例如：00:00:05 – 00:00:10、00:01:00 - 00:02:30 和 00:41:21 – 00:41:49)。每個時態性執行個體都可以有額外的中繼資料。 例如，臉部的矩形座標 (20,230,60,60)。

|版本|程式碼版本|
|---|---|
|sourceLanguage|影片的來源語言 (採用一個主要語言)。 其格式為 [BCP-47](https://tools.ietf.org/html/bcp47) 字串。|
|語言|深入解析語言 (從來源語言翻譯)。 其格式為 [BCP-47](https://tools.ietf.org/html/bcp47) 字串。|
|文字記錄|文字[記錄](#transcript)深入解析。|
|ocr|[OCR](#ocr)深入解析。|
|關鍵字|[關鍵字](#keywords)深入解析。|
|blocks|可包含一或多個[區塊](#blocks)|
|臉部|[臉部](#faces)深入解析。|
|標籤|[標籤](#labels)深入解析。|
|擷取畫面|[照片](#shots)深入解析。|
|brands|[品牌](#brands)見解。|
|audioEffects|[AudioEffects](#audioEffects)深入解析。|
|人氣|[情緒](#sentiments)深入解析。|
|visualContentModeration|[VisualContentModeration](#visualcontentmoderation)深入解析。|
|textualContentModeration|[TextualContentModeration](#textualcontentmoderation)深入解析。|
|emotions| [表情](#emotions)深入解析。|
|topics|[主題](#topics)深入解析。|

範例：

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

屬性 | 描述
---|---
id|區塊的識別碼。|
執行個體|此區塊的時間範圍清單。|

#### <a name="transcript"></a>文字記錄

|名字|描述|
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

#### <a name="ocr"></a>ocr

|名字|描述|
|---|---|
|id|OCR 行識別碼。|
|text|OCR 文字。|
|信賴度|辨識信賴。|
|語言|OCR 語言。|
|執行個體|此 OCR 曾出現的時間範圍清單 (相同的 OCR 可以出現多次)。|
|height|OCR 矩形的高度|
|top|Px 中的最上層位置|
|left| Px 中的左邊位置|
|width|OCR 矩形的寬度|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>關鍵字

|名字|描述|
|---|---|
|id|關鍵字識別碼。|
|text|關鍵字。|
|信賴度|關鍵字的辨識信賴。|
|語言|關鍵字語言 (轉譯時)。|
|執行個體|此關鍵字曾出現的時間範圍清單 (同一個關鍵字可以出現多次)。|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>臉部

|名字|描述|
|---|---|
|id|臉部識別碼。|
|name|臉部的名稱。 這可以是 'Unknown #0、已識別的名人或客戶培訓人員。|
|信賴度|臉部識別信賴。|
|description|名人的描述。 |
|thumbnailId|該臉部的縮圖識別碼。|
|knownPersonId|如果是已知人物，則為其內部識別碼。|
|referenceId|若為 Bing 名人，則為其 Bing 識別碼。|
|referenceType|目前只有 Bing。|
|title|若為名人，則為其職稱 (例如 "Microsoft CEO")。|
|imageUrl|若為名人，則為其影像 URL。|
|執行個體|這些是臉部出現在指定時間範圍中的執行個體。 每個執行個體也會有 thumbnailsId。 |

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

#### <a name="labels"></a>標籤

|名字|描述|
|---|---|
|id|標籤識別碼。|
|name|標籤名稱 (例如，電腦、電視)。|
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

#### <a name="scenes"></a>場景

|名字|描述|
|---|---|
|id|場景識別碼。|
|執行個體|此場景的時間範圍清單（場景只能有1個實例）。|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>擷取畫面

|名字|描述|
|---|---|
|id|擷取畫面識別碼。|
|keyFrames|一段期間內的主要畫面格清單（每個都有一個識別碼和實例時間範圍的清單）。 每個主要畫面格實例都有一個 thumbnailId 欄位，其中包含主要畫面格的縮圖識別碼。|
|執行個體|這個快照的時間範圍清單（一次只能有1個實例）。|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>brands

在語音轉換文字的文字記錄和/或影片 OCR 中偵測到的商務和產品品牌名稱。 這不包括品牌或標誌的影像辨識偵測。

|名字|描述|
|---|---|
|id|品牌識別碼。|
|name|品牌名稱。|
|referenceId | 品牌 Wikipedia URL 的尾碼。 例如，"Target_Corporation" 是 [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) 的尾碼。
|referenceUrl | 品牌的 Wikipedia URL (如果存在)。 例如：[https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)。
|description|品牌描述。|
|tags|與此品牌相關聯的預先定義標記清單。|
|信賴度|影片索引子品牌偵測器的信賴值 (0-1)。|
|執行個體|此品牌的時間範圍清單。 每個執行個體都有 brandType，用以指出此品牌會出現在文字記錄還是 OCR 中。|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>統計資料

|名字|描述|
|---|---|
|CorrespondenceCount|影片中的對應數目。|
|SpeakerWordCount|每個說話者的字數。|
|SpeakerNumberOfFragments|說話者在影片中的片段數量。|
|SpeakerLongestMonolog|說話者最長的獨白。 若說話在獨白中有無聲的部分，也會包含在其中。 獨白開頭和結尾的無聲部分則會被移除。| 
|SpeakerTalkToListenRatio|將說話者獨白的時間 (不含無聲的部分) 除以影片的總時間長度。 時間會四捨五入至小數點第三位。|

#### <a name="a-idaudioeffectsaudioeffects"></a><a id="audioEffects"/>audioEffects

|名字|描述|
|---|---|
|id|音訊效果識別碼。|
|type|音訊效果類型 (例如，拍手聲、說話、無聲)。|
|執行個體|此音訊效果曾出現的時間範圍清單。|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>人氣

人氣會依據其 sentimentType 欄位 (Positive/Neutral/Negative) 加以彙總。 例如：0-0.1、0.1-0.2。

|名字|描述|
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

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 區塊包含影片索引器偵測到可能含有成人內容的時間範圍。 若 visualContentModeration 是空的，表示未識別到任何成人內容。

經發現含有成人或猥褻內容的影片，只能供私人檢視。 使用者可以要求人工審核影片內容，在此情況下，IsAdult 屬性將包含人工審核的結果。

|名字|描述|
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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|名字|描述|
|---|---|
|id|文字內容仲裁識別碼。|
|bannedWordsCount |禁用文字數目。|
|bannedWordsRatio |總字數的比例。|

#### <a name="emotions"></a>emotions

影片索引子會根據語音和音訊提示來識別表情。可能的表情為：快樂、悲傷、憤怒或恐懼。

|名字|描述|
|---|---|
|id|表情識別碼。|
|type|系統會根據語音和音訊提示來識別感性時刻。表情包括：快樂、悲傷、憤怒或恐懼。|
|執行個體|這一個表情出現的時間範圍清單。|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>topics

影片索引子會從文字記錄中推斷主要主題。 可能的話，會包含第二層級的[IPTC](https://iptc.org/standards/media-topics/)分類。 

|名字|描述|
|---|---|
|id|主題識別碼。|
|name|主題名稱，例如："Pharmaceuticals"。|
|referenceId|反映主題階層的階層連結。 例如: 「健康與福利 / 醫藥與醫療保健 / 藥品」。|
|信賴度|範圍內 [0,1] 的信賴分數。 值越高，信賴程度就越高。|
|語言|主題中使用的語言。|
|iptcName|IPTC 媒體程式碼名稱 (如果偵測到)。|
|執行個體 |目前，影片索引子並不會編製時間間隔主題的索引，因此會以整個影片作為間隔。|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>後續步驟

[影片索引子開發人員入口網站](https://api-portal.videoindexer.ai)

如需如何在應用程式中內嵌小工具的詳細資訊，請參閱[將影片索引子小工具內嵌到應用程式中](video-indexer-embed-widgets.md)。 

