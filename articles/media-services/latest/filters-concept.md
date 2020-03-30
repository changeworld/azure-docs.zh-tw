---
title: 在 Azure 媒體服務中定義篩選條件
description: 本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。 媒體服務會建立動態資訊清單來完成此選擇性資料流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251462"
---
# <a name="filters"></a>篩選器

向客戶交付內容（即時流式處理事件或視頻點播）時，用戶端可能需要比預設資產清單檔中所述的靈活性。 Azure 媒體服務基於預定義的篩選器提供[動態清單](filters-dynamic-manifest-overview.md)。 

篩選器是伺服器端規則，可讓您的客戶執行下列動作： 

- 僅播放視訊的某個區段 (而非播放整個視訊)。 例如：
  - 縮小資訊清單以顯示即時事件的子剪輯 (「子剪輯篩選」)，或者
  - 修剪視訊開頭 (「修剪視訊」)。
- 只傳遞用來播放內容的裝置所支援的指定轉譯和/或指定的語言資料軌 (轉譯篩選)。 
- 調整簡報視窗 (DVR)，以在播放程式中提供長度有限的 DVR 視窗 (「調整簡報視窗」)。

媒體服務使您能夠為內容創建 **"帳戶"篩選器**和**資產篩選器**。 此外，您可以將預先創建的篩選器與**流式處理器**相關聯。

## <a name="defining-filters"></a>定義篩選器

有兩種類型的篩選器： 

* [帳戶篩選器](https://docs.microsoft.com/rest/api/media/accountfilters) (全域) - 可以套用到 Azure 媒體服務帳戶中的任何資產，存留期和帳戶的相同。
* [資產篩選器](https://docs.microsoft.com/rest/api/media/assetfilters) (本機) - 只能套用篩選器建立時與其相關聯的資產，存留期和資產的相同。 

**帳戶篩選器**和**資產篩選器**類型具有完全相同的屬性來定義/描述篩選器。 除了在建立**資產篩選器**的時候，因為您需要指定要與篩選器相關聯之資產的名稱。

視您的案例而定，您會決定哪個類型的篩選器較合適 (資產篩選器或帳戶篩選器)。 帳戶篩選器適合裝置設定檔 (轉譯篩選)，資產篩選器可用於修剪特定資產。

您可以使用下列屬性來描述篩選器。 

|名稱|描述|
|---|---|
|firstQuality|篩選器的首次品質位元速率。|
|presentationTimeRange|簡報時間範圍。 此屬性用於篩選資訊清單起始/結束點、簡報視窗長度，以及即時起始位置。 <br/>如需詳細資訊，請參閱 [PresentationTimeRange](#presentationtimerange)。|
|tracks|資料軌選取條件。 如需詳細資訊，請參閱[資料軌](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

將此屬性與**資產篩選器**搭配使用。 不建議搭配**帳戶篩選器**設定此屬性。

|名稱|描述|
|---|---|
|**endTimestamp**|適用於點播視訊 (VoD)。<br/>對於即時流式處理演示文稿，當演示文稿結束並且流變為 VoD 時，將靜默忽略並應用它。<br/>這是一個長值，表示演示文稿的絕對端點，四捨五入到最接近的下一個 GOP 開始。 該單位是時間刻度，因此 180000000 的尾時間戳記為 3 分鐘。<br/>使用 startTimestamp 和結束時間戳記修剪將在播放清單（清單）中片段。<br/>例如，startTimestamp=40000000 和使用預設時間刻度的結束時間戳記=10000000將生成包含 VoD 演示文稿 4 秒到 10 秒之間的片段的播放清單。 如果片段跨越界限，則整個片段都會包含在資訊清單中。|
|**forceEndTimestamp**|僅適用于即時流式處理。<br/>指示結束時間戳記屬性是否必須存在。 如果為 true，則必須指定結束時間戳記或返回錯誤的請求代碼。<br/>允許的值：false、true。|
|**liveBackoffDuration**|僅適用于即時流式處理。<br/> 此值定義用戶端可以尋求的最新即時位置。<br/>使用此屬性，您可以延遲即時播放位置並為播放機創建伺服器端緩衝區。<br/>此屬性的單位是時間刻度（見下文）。<br/>最大即時回退持續時間為 300 秒（300000000）。<br/>例如，值 2000000000 表示最新的可用內容從實際即時邊緣延遲 20 秒。|
|**presentationWindowDuration**|僅適用于即時流式處理。<br/>使用演示文稿視窗持續時間應用要包含在播放清單中的片段的滑動視窗。<br/>此屬性的單位是時間刻度（見下文）。<br/>例如，設定 presentationWindowDuration=1200000000 以套用兩分鐘長的滑動視窗。 在即時邊緣 2 分鐘內的媒體都會包含在播放清單中。 如果片段跨越界限，則整個片段都會包含在播放清單中。 簡報視窗持續時間的最小值是 60 秒。|
|**startTimestamp**|適用于視頻點播 （VoD） 或即時流式處理。<br/>這是表示流的絕對起始點的長值。 系統會將此值四捨五入到最接近的下一個 GOP 起始。 該單位是時間刻度，因此啟動時間戳記為 1500000000 為 15 秒。<br/>使用 startTimestamp 和 endTimestampp 修剪將在播放清單（清單）中片段。<br/>例如，startTimestamp=40000000 和使用預設時間刻度的結束時間戳記=10000000將生成包含 VoD 演示文稿 4 秒到 10 秒之間的片段的播放清單。 如果片段跨越界限，則整個片段都會包含在資訊清單中。|
|**timescale**|應用於演示時間範圍內的所有時間戳記和持續時間，指定為一秒的增量數。<br/>預設值為 10000000 - 一秒內為 1000 萬次增量，其中每個增量為 100 納秒長。<br/>例如，如果要將 startTimestamp 設置為 30 秒，則使用預設時間刻度時將使用 300000000 的值。|

### <a name="tracks"></a>資料軌

您可以指定篩選器跟蹤屬性條件（FilterTrack 屬性條件）的清單，根據該條件，流的曲目（即時流式流或視頻點播）應包含在動態創建的清單中。 篩選器是使用邏輯 **AND** 和 **OR** 運算。

篩選器資料軌屬性條件描述資料軌類型、值 (下表中所述) 和運算 (Equal、NotEqual)。 

|名稱|描述|
|---|---|
|**Bitrate**|使用資料軌的位元速率來篩選。<br/><br/>建議的值是位元速率範圍 (以每秒位元數為單位)。 例如，"0-2427000"。<br/><br/>注意：雖然您可以使用特定的位元速率值，如 250000 (每秒位元數)，但不建議使用此方法，因為資產之間的確切位元速率可能會變動。|
|**FourCC**|將資料軌的 FourCC 值用於篩選。<br/><br/>該值是轉碼器格式的第一個元素，如 [RFC 6381](https://tools.ietf.org/html/rfc6381) \(英文\) 中所指定。 目前支援下列轉碼器： <br/>視訊："avc1"、"hev1"、"hvc1"<br/>音訊："mp4a"、"ec-3"<br/><br/>若要判斷 Asset 中資料軌的 FourCC 值，請取得並檢查資訊清單檔案。|
|**Language**|使用資料軌的語言來篩選。<br/><br/>此值是您要包含之語言的標籤，如 RFC 5646 中所指定。 例如，"en"。|
|**名稱**|使用資料軌的名稱來篩選。|
|**類型**|使用資料軌的類型來篩選。<br/><br/>允許下列值："video"、"audio" 或 "text"。|

### <a name="example"></a>範例

以下示例定義即時流式處理篩選器： 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>將篩選器與流式處理器關聯

您可以在[流式處理器](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)上指定[資產或帳戶篩選器](filters-concept.md)的清單。 [動態打包程式](dynamic-packaging-overview.md)將此篩選器清單與用戶端在 URL 中指定的篩選器一起應用。 此組合生成[動態清單](filters-dynamic-manifest-overview.md)，該清單基於您在流式處理器上指定的 URL 和篩選器中的篩選器。 

請參閱下列範例：

* [將篩選器與流式處理器關聯 - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [將篩選器與流式處理器關聯 - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>更新篩選器
 
**流式處理器**不可更新，而篩選器可以更新。 

不建議更新與主動發佈的**流式處理器**關聯的篩選器的定義，尤其是在啟用 CDN 時。 流式伺服器和 CDN 可以具有內部緩存，可能導致要返回的陳舊緩存資料。 

如果需要更改篩選器定義，請考慮創建新篩選器並將其添加到**流式處理器**URL 或發佈直接引用篩選器的新**流式處理器。**

## <a name="next-steps"></a>後續步驟

下列文章說明如何以程式設計方式建立篩選器。  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

