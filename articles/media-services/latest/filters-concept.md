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
ms.openlocfilehash: 2e188a0e8ee8b5f2037c07c3f15fd78a42852ce9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023223"
---
# <a name="filters"></a>篩選器

將您的內容傳遞給客戶（即時串流事件或隨選影片）時，您的用戶端可能需要比預設資產資訊清單檔案中所述更多的彈性。 Azure 媒體服務根據預先定義的篩選器提供[動態資訊清單](filters-dynamic-manifest-overview.md)。 

篩選器是伺服器端規則，可讓您的客戶執行下列動作： 

- 僅播放視訊的某個區段 (而非播放整個視訊)。 例如：
  - 縮小資訊清單以顯示即時事件的子剪輯 (「子剪輯篩選」)，或者
  - 修剪視訊開頭 (「修剪視訊」)。
- 只傳遞用來播放內容的裝置所支援的指定轉譯和/或指定的語言資料軌 (轉譯篩選)。 
- 調整簡報視窗 (DVR)，以在播放程式中提供長度有限的 DVR 視窗 (「調整簡報視窗」)。

媒體服務可讓您建立內容的**帳戶篩選器**和**資產篩選器**。 此外，您可以將預先建立的篩選器與**串流定位器**建立關聯。

## <a name="defining-filters"></a>定義篩選器

篩選器有兩種類型： 

* [帳戶篩選器](/rest/api/media/accountfilters) (全域) - 可以套用到 Azure 媒體服務帳戶中的任何資產，存留期和帳戶的相同。
* [資產篩選器](/rest/api/media/assetfilters) (本機) - 只能套用篩選器建立時與其相關聯的資產，存留期和資產的相同。 

**帳戶篩選器**和**資產篩選**器類型與定義/描述篩選器的屬性完全相同。 除了在建立**資產篩選器**的時候，因為您需要指定要與篩選器相關聯之資產的名稱。

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
|**endTimestamp**|適用於點播視訊 (VoD)。<br/>針對即時串流簡報，當簡報結束且資料流程變成 VoD 時，會以無訊息方式略過並套用。<br/>這是很長的值，表示簡報的絕對結束點，舍入到最接近的下一個 GOP 開始。 單位是時間刻度，因此1800000000的 endTimestamp 會是3分鐘。<br/>使用 startTimestamp 和 endTimestamp 修剪播放清單（資訊清單）中的片段。<br/>例如，startTimestamp = 40000000 和 endTimestamp = 100000000 使用預設的時間刻度，會產生一個播放清單，其中包含介於 VoD 簡報的4秒和10秒之間的片段。 如果片段跨越界限，則整個片段都會包含在資訊清單中。|
|**forceEndTimestamp**|僅適用于即時串流。<br/>指出是否必須要有 endTimestamp 屬性。 若為 true，則必須指定 endTimestamp，否則會傳回不正確的要求碼。<br/>允許的值：false、true。|
|**liveBackoffDuration**|僅適用于即時串流。<br/> 這個值會定義用戶端可搜尋的最新即時位置。<br/>您可以使用這個屬性來延遲即時播放位置，並為播放程式建立伺服器端緩衝區。<br/>此屬性的單位為「時間刻度」（如下所示）。<br/>即時回復持續時間的最大值為300秒（3000000000）。<br/>例如，值2000000000表示最新的可用內容是從真實的即時邊緣延遲20秒。|
|**presentationWindowDuration**|僅適用于即時串流。<br/>使用 presentationWindowDuration 來套用要包含在播放清單中的片段滑動視窗。<br/>此屬性的單位為「時間刻度」（如下所示）。<br/>例如，設定 presentationWindowDuration=1200000000 以套用兩分鐘長的滑動視窗。 在即時邊緣 2 分鐘內的媒體都會包含在播放清單中。 如果片段跨越界限，則整個片段都會包含在播放清單中。 簡報視窗持續時間的最小值是 60 秒。|
|**startTimestamp**|適用于隨選影片（VoD）或即時串流。<br/>這是代表資料流程絕對起點的長數值。 系統會將此值四捨五入到最接近的下一個 GOP 起始。 單位是時間刻度，因此150000000的 startTimestamp 會是15秒。<br/>使用 startTimestamp 和 endTimestampp 修剪播放清單（資訊清單）中的片段。<br/>例如，startTimestamp = 40000000 和 endTimestamp = 100000000 使用預設的時間刻度，會產生一個播放清單，其中包含介於 VoD 簡報的4秒和10秒之間的片段。 如果片段跨越界限，則整個片段都會包含在資訊清單中。|
|**timescale**|適用于呈現時間範圍內的所有時間戳記和持續期間，指定為一秒內的增量數目。<br/>預設值為 10000000-10000000 遞增一秒，其中每個增量會是100毫微秒長。<br/>例如，如果您想要將 startTimestamp 設定為30秒，則使用預設的時間刻度時，會使用300000000的值。|

### <a name="tracks"></a>資料軌

您可以根據您的串流追蹤（即時串流或隨選影片）應包含在動態建立的資訊清單中，指定篩選追蹤屬性條件（FilterTrackPropertyConditions）的清單。 篩選器是使用邏輯 **AND** 和 **OR** 運算。

篩選器資料軌屬性條件描述資料軌類型、值 (下表中所述) 和運算 (Equal、NotEqual)。 

|名稱|描述|
|---|---|
|**Bitrate**|使用資料軌的位元速率來篩選。<br/><br/>建議的值是位元速率範圍 (以每秒位元數為單位)。 例如，"0-2427000"。<br/><br/>注意：雖然您可以使用特定的位元速率值，如 250000 (每秒位元數)，但不建議使用此方法，因為資產之間的確切位元速率可能會變動。|
|**FourCC**|將資料軌的 FourCC 值用於篩選。<br/><br/>該值是轉碼器格式的第一個元素，如 [RFC 6381](https://tools.ietf.org/html/rfc6381) \(英文\) 中所指定。 目前支援下列轉碼器： <br/>視訊："avc1"、"hev1"、"hvc1"<br/>音訊："mp4a"、"ec-3"<br/><br/>若要判斷 Asset 中資料軌的 FourCC 值，請取得並檢查資訊清單檔案。|
|**語言**|使用資料軌的語言來篩選。<br/><br/>此值是您要包含之語言的標籤，如 RFC 5646 中所指定。 例如，"en"。|
|**名稱**|使用資料軌的名稱來篩選。|
|**類型**|使用資料軌的類型來篩選。<br/><br/>允許下列值："video"、"audio" 或 "text"。|

### <a name="example"></a>範例

下列範例會定義即時串流篩選： 

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

## <a name="associating-filters-with-streaming-locator"></a>建立篩選與串流定位器的關聯

您可以在[串流定位器](/rest/api/media/streaminglocators/create#request-body)上指定[資產或帳戶篩選器](filters-concept.md)的清單。 [動態](dynamic-packaging-overview.md)封裝程式會套用此篩選器清單，以及您的用戶端在 URL 中指定的篩選準則。 這個組合會產生[動態資訊清單](filters-dynamic-manifest-overview.md)，這是以 URL 中的篩選器，以及您在串流定位器上指定的篩選器為基礎。 

請參閱下列範例：

* [將篩選器與串流定位器建立關聯-.NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [將篩選器與串流定位器建立關聯-CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>正在更新篩選
 
當篩選器可以更新時，**資料流程定位器**不是可更新的。 

不建議更新與主動發佈的**串流定位器**相關聯的篩選器定義，特別是在啟用 CDN 時。 串流伺服器和 Cdn 可以有內部快取，可能會導致傳回過時的快取資料。 

如果必須變更篩選定義，請考慮建立新的篩選器，並將它新增至**串流定位器**URL，或直接發行參考篩選器的新**串流定位器**。

## <a name="next-steps"></a>接下來的步驟

下列文章說明如何以程式設計方式建立篩選器。  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)
