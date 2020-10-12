---
title: 錄製播放-Azure
description: 您可以在 IoT Edge 上使用即時影片分析進行持續錄製影片，讓您可以將影片記錄到雲端，以獲得數周或數個月的時間。 您也可以透過以事件為基礎的錄製，將錄製限制為感興趣的剪輯。 本文討論如何播放錄製。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260381"
---
# <a name="playback-of-recordings"></a>播放錄製 

## <a name="pre-read"></a>預先讀取  

* [播放影片](video-playback-concept.md)
* [錄製連續影片](continuous-video-recording-concept.md)
* [發生事件時錄製影片](event-based-video-recording-concept.md)

## <a name="background"></a>背景  

您可以在 IoT Edge 上使用即時影片分析 [ (CVR) ，讓您](continuous-video-recording-concept.md) 可以將影片記錄到雲端中的數周或數個月。 您也可以透過以 [事件為基礎的影片錄製](event-based-video-recording-concept.md) (EVR) ，將錄製限制為感興趣的剪輯。 

您的媒體服務帳戶會連結至 Azure 儲存體帳戶，當您將影片記錄到雲端時，內容會寫入 [媒體服務資產](terminology.md#asset)。 媒體服務可讓您在錄製完成或正在進行錄製時， [串流該內容](terminology.md#streaming)。 媒體服務提供您必要的功能，可透過 HLS 或 MPEG 虛線通訊協定來傳遞串流， (用戶端) 的播放裝置。 如需詳細資訊，請參閱 [影片播放](video-playback-concept.md) 文章。 您可以使用媒體服務 Api 來產生必要的串流 Url，用戶端會使用這些 Url 來播放影片 & 音訊。 若要建立資產的串流 URL，請參閱 [建立串流定位器和組建 url](../latest/create-streaming-locator-build-url.md)。 建立資產的串流 URL 之後，您就可以儲存 URL 與影片來源的關聯， (也就是在內容管理系統中的攝影機) 。

例如，透過 HLS 串流處理時，串流 URL 看起來會像這樣 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` 。 若為 MPEG 虛線，則看起來會像這樣 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` 。

這會傳回資訊清單檔案，在其他專案中，會描述正在傳遞之資料流程的整體持續時間，以及它是否代表預先錄製的內容，或是正在進行的「即時」摘要。

### <a name="live-vs-vod"></a>Live 與 VoD  

HLS 和 MPEG 這類串流通訊協定的撰寫是為了處理即時影片的串流，以及串流處理隨選/預先錄製的內容，例如電視節目與電影。 針對即時影片，HLS 和 MPEG 用戶端是設計來開始從「最近」開始播放的時間。 不過，使用影片時，檢視器預期能夠從頭開始，並在選擇時跳到。 HLS 和 MPEG 虛線資訊清單具有旗標，可向用戶端指出影片是否代表即時串流，或是預先錄製的內容。
此概念也適用于在 IoT Edge 上使用即時影片分析錄製影片的資產中的 HLS 和 MPEG 虛線串流。

## <a name="browsing-and-selective-playback-of-recordings"></a>流覽和選擇性播放錄製  

請考慮您在 IoT Edge 上使用即時影片分析的案例，只會在學校開啟時，將影片從上午8點記錄到10AM，以供整個學術年度之用。 或者，您可能只是從排列7AM-7PM 到7AM-7PM 的國家假日錄製影片。 在這兩種情況下，當您嘗試流覽和觀看影片錄影時，您需要：

* 判斷錄製中有哪些日期/小時影片的方式。
* 一種選取部分 (的方法，例如，以上午 9 11AM 在錄製播放的新年份) 。

媒體服務可為您提供查詢 API (availableMedia) 來解決第一個問題和時間範圍篩選 (startTime，endTime) 以解決第二個問題。

## <a name="query-api"></a>查詢 API 

使用 CVR 時， (用戶端的播放裝置) 無法要求涵蓋播放整個錄影的資訊清單。  多年份錄製會產生太大而無法播放的資訊清單檔案，而且無法在用戶端上剖析成可用的部分。  用戶端必須知道哪些 datetime 範圍在錄製中有資料，讓它可以提出有效的要求，而不會有很多猜測工作。 這是新的查詢 API 的來源，用戶端現在可以使用此伺服器端 API 來探索內容。

其中，精確度值可以是下列其中一個： year、month、day 或 full (，如下所示) 。 

|Precision|year|月|day|完整|
|---|---|---|---|---|
|查詢|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|回應|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|完整的精確度回應。 如果沒有間距，則開始會是 startTime，而 end 會是 endTime。|
|約束|&#x2022;startTime <= endTime<br/>&#x2022;兩者都應該採用 YYYY 格式，否則會傳回錯誤。<br/>&#x2022;的值可能會有數年的間隔。<br/>&#x2022;值為內含。|&#x2022;startTime <= endTime<br/>&#x2022;兩者都應該採用 YYYY-MM-DD 格式，否則會傳回錯誤。<br/>&#x2022;值的間隔最多可達12個月。<br/>&#x2022;值為內含。|&#x2022;startTime <= endTime<br/>&#x2022;兩者都應該採用 YYYY-MM-DD 格式，否則會傳回錯誤。<br/>&#x2022;值的間隔最多可達31天。<br/>值為內含。|&#x2022;startTime < endTime<br/>&#x2022;值的間隔最多可達25小時。<br/>&#x2022;值為內含。|

#### <a name="additional-request-format-considerations"></a>其他要求格式考慮

* 所有時間都是 UTC 格式
* 需要精確度查詢字串參數。  
* Month、day 和 full precision 值都需要 startTime 和 endTime 查詢字串參數。  
* StartTime 和 endTime 查詢字串參數對於年份有效位數值是選擇性的， (none、or 或 both 都支援) 。  

    * 如果省略 startTime，則會假設為錄製中的第一年。
    * 如果省略 endTime，則會假設為錄製的最後一年。
    * 例如，如果您的錄製在2011中開始，且一直到2020，則：

        * /availableMedia？ precision = year 與/availableMedia？ precision = year&startTime = 2011&endTime = 2020
        * /availableMedia？ precision = year&startTime = 2015 等同于/availableMedia？ precision = year&startTime = 2015&endTime = 2020
        * /availableMedia？ precision = year&endTime = 2018 等同于/availableMedia？ precision = year&startTime = 2011&endTime = 2018

如果時間範圍內沒有任何可用的媒體資料，則會傳回空的清單。

如果資產未包含媒體圖形中的記錄，則會傳回 HTTP 400 回應，並顯示錯誤訊息，說明這項功能僅適用于透過 media graph 錄製的內容。

如果參數所指定的時間長度超過指定查詢類型的最大時間範圍所允許的時間，則會傳回 HTTP 400，並顯示錯誤訊息，說明所要求查詢類型的最大允許時間範圍。

假設時間範圍適用于指定的參數，則不會針對記錄中資料時間範圍以外的查詢傳回錯誤。  這表示，如果錄製的時間為7小時前，但客戶要求從 {UtcNow –24小時} 到 UtcNow 的可用媒體，則只會傳回 {UtcNow – 7} 小時的資料。

### <a name="response-format"></a>回應格式  

AvailableMedia 呼叫會傳回一組時間值。

回應將會是 JSON 主體，其中 timeRanges 的值是年份的 ISO 8601 UTC 日期陣列 (格式為) 、以 YYYY-MM-DD 格式) 的月份 (，或根據所要求的有效位數 (YYYY-MM-DD) 的日期。  完整 timeRanges 將包含格式化為 ISO 8601 UTC 日期時間的開始和結束值 (DDThh： MM： ss. sssZ 格式) 。

針對 availableMedia 查詢，API 將會關閉影片時間軸的索引鍵。 時間軸中的任何不連續都會顯示為回應中的間隙。

#### <a name="response-example-for-availablemedia"></a>AvailableMedia 的回應範例

##### <a name="example-1-live-recording-with-no-gaps"></a>範例1：沒有間距的即時錄製

以下是顯示2019年12月21日所有可用媒體的回應，其中記錄是100% 的完成。 回應只有一個開始/結束配對。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>範例2：具有2秒間距的即時錄製

假設基於某個原因，相機無法在一天2秒的間隔內傳送有效的影片。 以下是顯示2019年12月21日所有可用媒體的回應：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>範例3：具有8小時間距的即時錄製

假設相機和/或內部部署設備在一天內的8小時內遺失電源，從上午4點 UTC 到中午 UTC，而且沒有備份電源來源。 以下是顯示這一天所有可用媒體的回應

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>範例4：不錄製任何影片的即時錄製（非夏季假日）

假設您只在學校參與課程時錄製影片，並在6月17日到9月6日停止錄製。 可用月份的查詢如下所示：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

如果您接著要求取得六月的可用天數，您會看到：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>範例5：在週末或假日沒有錄製任何影片的即時錄製

假設您只在上班時間錄製影片。 可用天數的查詢看起來像這樣

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>時間範圍篩選準則

如先前所述，這些篩選器可協助您選取部分錄製 (例如，從上午9點到11AM，在新的一年中) 播放。 透過 HLS 串流處理時，串流 URL 看起來會像這樣 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` 。 若要選取記錄的一部分，您可以新增 startTime 和 endTime 參數，例如： `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` 。 因此，時間範圍篩選器是 URL 修飾詞，用來描述串流資訊清單中包含的錄製時間軸部分：

* `starttime` 是 ISO 8601 日期時間戳記，可描述所傳回之資訊清單中影片時間軸的預期開始時間。
* `endtime` 是 ISO 8601 日期時間戳記記，描述資訊清單中所傳回影片時間軸的預期結束時間。

此類資訊清單 (時間長度上限) 不能超過24小時。

以下是篩選準則的限制。

|startTime| EndTime |結果|
|---|---|---|
|Absent |Absent |傳回資產中最新部分的影片，最大長度為4小時。<br/><br/>如果尚未將資產寫入 (最近沒有任何新的影片資料進入) ，則會傳回隨選 (VoD) 資訊清單。 否則會傳回即時資訊清單。|
|存在|Absent|    如果這類的資訊清單小於24小時，則傳回具有任何可用影片的資訊清單。<br/>如果資訊清單的長度超過24小時，則會傳回錯誤。<br/>如果尚未將資產寫入 (最近沒有任何新的影片資料進入) ，則會傳回隨選 (VoD) 資訊清單。 否則會傳回即時資訊清單。
|Absent|存在 |錯誤–如果 startTime 存在，則會強制 endTime。|
|存在|存在|傳回 VoD 資訊清單，其中包含 startTime 和 endTime 之間的任何可用影片。<br/>Span (startTime，endTime) 不能超過24小時。|

### <a name="response-examples"></a>回應範例  

#### <a name="example-1-live-recording-with-no-gaps"></a>範例1：沒有間距的即時錄製

以下是顯示2019年12月21日所有可用媒體的回應，其中記錄是100% 的完成。 回應只有一個開始/結束配對。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

當記錄是連續的時，您可以在該開始/結束配對內的任何時間部分要求 HLS 或虛線資訊清單，只要範圍是24小時或更少。 上述4小時 HLS 資訊清單要求的範例如下：

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>範例2：具有2秒間距的即時錄製

假設基於某個原因，相機無法在一天2秒的間隔內傳送有效的影片。 以下是顯示2019年12月21日可用媒體的回應：

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

使用上述的錄製時，您可以使用任何 startTime/endTime 組要求 HLS 和虛線資訊清單（只要範圍低於24小時）即可。 如果這些值 straddled 在04：01： 08AM UTC 的間隔，則傳回的資訊清單會根據這些通訊協定的相關規格來發出媒體時間軸中的不連續信號。

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>範例3：具有8小時間距的即時錄製

假設相機和/或內部部署設備在一天內的8小時內遺失電源，從上午4點 UTC 到中午 UTC，而且沒有備份電源來源。 以下是顯示這一天所有可用媒體的回應。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

有這類記錄：

* 如果您要求一個資訊清單，其中 startTime/endTime 範圍篩選器位於時間軸的「可用」部分（也就是從午夜到4AM，或從中午到午夜），則服務會傳回包含 startTime 到 endTime 時間的資訊清單，例如：

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* 如果您要求的資訊清單中的 startTime 和 endTime 位於中間的 ' 洞 ' 內（例如從上午8點到 10AM UTC），則服務的行為會與資產篩選器產生空白結果的方式相同。

    [這是取得空白回應的要求] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* 如果您要求資訊清單，其中只有一個 startTime 或 endTime 是在 ' 洞 ' 內，則傳回的資訊清單只會包含該時間範圍內的一部分。 它會將 startTime 或 endTime 值貼齊最接近的有效界限。 例如，如果您要求從10AM 到下午3小時的串流，回應將會包含1小時的媒體12，下午12點

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    傳回的資訊清單會在 2019-12-21T12：00： 00.000 Z 開始，即使要求要求開始10AM 的要求也是一樣。 使用播放程式外掛程式建立影片管理系統時，請小心處理檢視器的指示。 不知道的檢視器會與播放時間軸從中午開始的原因混淆，且未依要求10AM

## <a name="recording-and-playback-latencies"></a>錄製和播放延遲

在 IoT Edge 上使用即時影片分析來記錄資產時，您將會指定 segmentLength 屬性，此屬性會告知模組匯總影片的最小持續時間 (（以秒為單位) ），然後再將其記錄到雲端。 例如，如果 segmentLength 設定為300，則模組會在上傳 1 5 分鐘的「區塊」之前累積5分鐘的影片，然後進入累積模式（在接下來5分鐘），然後再上傳一次。 增加 segmentLength 的優點是降低 Azure 儲存體的交易成本，因為讀取和寫入的數目將不會比每個 segmentLength 秒一次更頻繁。

因此，從媒體服務串流處理的影片至少會延遲一段時間。 

判斷播放延遲的另一個因素 (在相機前方發生事件的時間之間的延遲時間，到可在播放裝置上查看的時間) 是一組相片的 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 持續時間。 藉 [由使用3個簡單的技巧來減少即時資料流的延遲](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) ，則會有更長的 GOP 持續時間，較長的延遲。 在監視和安全性案例中，使用的 Gop 超過30秒的時間，通常會使用 IP 攝影機。 這對整體延遲會有很大的影響。

## <a name="next-steps"></a>接下來的步驟

[持續錄製影片教學課程](continuous-video-recording-tutorial.md)
