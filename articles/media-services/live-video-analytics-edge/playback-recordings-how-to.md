---
title: 播放錄製-Azure
description: 您可以在 IoT Edge 上使用即時影片分析來進行連續的影片錄製，讓您可以將影片錄製到雲端中數周或數個月。 您也可以透過以事件為基礎的錄製，將您的錄製限制為感對的剪輯。 本文討論如何播放錄製。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260381"
---
# <a name="playback-of-recordings"></a>播放錄製 

## <a name="pre-read"></a>預先讀取  

* [播放影片](video-playback-concept.md)
* [錄製連續影片](continuous-video-recording-concept.md)
* [發生事件時錄製影片](event-based-video-recording-concept.md)

## <a name="background"></a>背景  

您可以在 IoT Edge 上使用即時影片分析來進行[連續錄影](continuous-video-recording-concept.md)（CVR），讓您可以將影片錄製到雲端中數周或數個月。 您也可以透過以[事件為基礎的影片錄影](event-based-video-recording-concept.md)（EVR），將您的錄製限制為感對的剪輯。 

您的媒體服務帳戶會連結到 Azure 儲存體帳戶，而當您將影片錄製到雲端時，內容會寫入至[媒體服務資產](terminology.md#asset)。 媒體服務可讓您在錄製完成後或錄製正在進行時，[串流該內容](terminology.md#streaming)。 媒體服務提供您必要的功能，透過 HLS 或 MPEG-2 通訊協定將串流傳遞至播放裝置（用戶端）。 如需詳細資訊，請參閱[影片播放](video-playback-concept.md)文章。 您會使用媒體服務 Api 來產生必要的串流 Url –供用戶端播放影片 & 音訊。 若要建立資產的串流 URL，請參閱[建立串流定位器和建立 url](../latest/create-streaming-locator-build-url.md)。 建立資產的串流 URL 之後，您就可以，而且應該在內容管理系統中儲存 URL 與影片來源（也就是相機）的關聯。

例如，透過 HLS 串流處理時，串流 URL 看起來會像這樣 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` 。 對於 MPEG-破折號，它看起來會像這樣 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` 。

這會傳回資訊清單檔案，在其他專案中，會描述正在傳遞之資料流程的整體持續時間，以及它是否代表預先記錄的內容，或是持續的「即時」摘要。

### <a name="live-vs-vod"></a>Live 與 VoD 的比較  

串流通訊協定（例如 HLS 和 MPEG-2）是為了處理即時影片串流之類的案例，以及電視節目和電影等隨選/預先錄製內容的串流。 針對即時影片，HLS 和 MPEG-2 用戶端是設計用來從「最近最新」的時間開始播放。 不過，使用電影時，檢視器預期能夠從頭開始，並在他們選擇的情況下跳躍。 HLS 和 MPEG-2 資訊清單具有旗標，可向用戶端指出影片是否代表即時串流，或其為預先錄製的內容。
此概念也適用于資產中的 HLS 和 MPEG 破折號串流，其中包含使用即時影片分析在 IoT Edge 上錄製的影片。

## <a name="browsing-and-selective-playback-of-recordings"></a>流覽和選擇性播放錄製  

請考慮您在 IoT Edge 上使用即時影片分析的案例，只會將影片從上午8點到10AM，並在學校開啟時，在整個學術年度中錄製。 或者，您可能只是從排列7AM-7PM 到7AM-7PM 的國家/地區假日錄影。 在這兩種情況下，當您嘗試流覽並觀看影片錄影時，您需要：

* 一種決定記錄中的日期/小時影片的方式。
* 一種方式，可選取該記錄要播放的部分（例如，在新的一天上午9點到11AM）。

媒體服務提供查詢 API （availableMedia）來處理第一個問題，以及處理時間範圍篩選（startTime，endTime）來解決第二個問題。

## <a name="query-api"></a>查詢 API 

使用 CVR 時，播放裝置（用戶端）無法要求資訊清單，涵蓋播放整份錄影。  多年來的錄製會產生太大而無法播放的資訊清單檔案，而且在用戶端上剖析成可用的部分會很困難。  用戶端必須知道哪些日期時間範圍在記錄中有資料，如此才能提出有效的要求，而不會有太多猜測的工作。 這是新查詢 API 的來源-用戶端現在可以使用此伺服器端 API 來探索內容。

其中，精確度值可以是下列其中一個： year、month、day 或 full （如下所示）。 

|準確率|year|月|day|完整|
|---|---|---|---|---|
|查詢|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|回應|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|完整精確度回應。 如果完全沒有間距，則 start 會是 startTime，而 end 會是 endTime。|
|限定|&#x2022;startTime <= endTime<br/>&#x2022;兩者都應該是 YYYY 格式，否則會傳回錯誤。<br/>&#x2022;值可以是任何相隔的年數。<br/>&#x2022;值包含在內。|&#x2022;startTime <= endTime<br/>&#x2022;兩者都應該採用 YYYY-MM-DD 格式，否則會傳回錯誤。<br/>&#x2022;值最多可以有12個月的距離。<br/>&#x2022;值包含在內。|&#x2022;startTime <= endTime<br/>&#x2022;兩者都應該採用 YYYY-MM-DD 格式，否則會傳回錯誤。<br/>&#x2022;值最多可以有31天的距離。<br/>值包含在內。|&#x2022;startTime < endTime<br/>&#x2022;值最多可以有25小時的距離。<br/>&#x2022;值包含在內。|

#### <a name="additional-request-format-considerations"></a>其他要求格式考慮

* 所有時間都是 UTC 格式
* 需要有效位數查詢字串參數。  
* Month 和 endTime 查詢字串參數在月份、日期和完整的精確度值中是必要的。  
* StartTime 和 endTime 查詢字串參數對於年份有效位數值而言是選擇性的（none、or 或 both 都支援）。  

    * 如果省略 startTime，則會假設為記錄中的第一年。
    * 如果省略 endTime，則會假設為記錄中的最後一年。
    * 例如，如果您的錄製在2011開始，並持續到2020，則：

        * /availableMedia？ precision = year 等同于/availableMedia？ precision = year&startTime = 2011&endTime = 2020
        * /availableMedia？有效位數 = year&startTime = 2015 與/availableMedia 相同？有效位數 = 年&startTime = 2015&endTime = 2020
        * /availableMedia？ precision = year&endTime = 2018 等同于/availableMedia？ precision = year&startTime = 2011&endTime = 2018

如果時間範圍內沒有可用的媒體資料，則會傳回空白清單。

如果資產不包含來自媒體圖形的記錄，則會傳回 HTTP 400 回應，其中會顯示一則錯誤訊息，說明這項功能僅適用于透過 media graph 記錄的內容。

如果參數所指定的時間長度大於給定查詢類型的最大時間範圍所允許，則會傳回 HTTP 400，並顯示錯誤訊息，說明所要求之查詢類型的最大允許時間範圍。

假設時間範圍對指定的參數有效，則不會針對記錄中資料的時間範圍外的查詢傳回任何錯誤。  也就是說，如果錄製已在7小時前完成，但客戶要求從 {UtcNow –24小時} 到 UtcNow 的可用媒體，則我們只會傳回 {UtcNow – 7} 小時的資料。

### <a name="response-format"></a>回應格式  

AvailableMedia 呼叫會傳回一組時間值。

回應會是 JSON 主體，其中的 timeRanges 值是年份的 ISO 8601 UTC 日期（YYYY 格式）、月份（YYYY-MM-DD 格式）或日（YYYY-MM-DD）的陣列，視所要求的精確度而定。  完整 timeRanges 將包含格式為 ISO 8601 UTC 日期時間的開始和結束值（以 YYYY-MM-DD DDThh： MM： ss. sssZ 格式）。

針對 availableMedia 查詢，API 將會關閉影片時間軸的索引鍵。 時間軸中的任何不連續都會顯示為回應中的間距。

#### <a name="response-example-for-availablemedia"></a>AvailableMedia 的回應範例

##### <a name="example-1-live-recording-with-no-gaps"></a>範例1：沒有間距的即時錄製

以下是顯示2019年12月21日的所有可用媒體的回應，其中記錄為100% 完成。 回應只有一個開始/結束配對。

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

基於某些原因，相機無法在一天的2秒間隔內傳送有效的影片。 以下是顯示2019年12月21日所有可用媒體的回應：

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

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>範例3：使用8小時間距的即時錄製

假設攝影機和/或內部部署設施在一天內的8小時內斷電，從上午4點 UTC 到中午12：00，而且沒有備份電源來源。 此回應會顯示此一天的所有可用媒體

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

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>範例4：在夏季假日未錄製任何影片的實況錄影

假設您只有在學校的授課時才錄製影片，且記錄是從6月17日到9月6日停止。 可用月份的查詢如下所示：

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

如果您接著要求提供6月份的可用天數，您會看到：

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

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>範例5：在週末或假日未錄製任何影片的即時錄製

假設您只在上班時間錄製了影片。 可用天數的查詢看起來會像這樣

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

## <a name="time-range-filters"></a>時間範圍篩選

如上所述，這些篩選器可協助您選取錄製的部分（例如，在新的一天上午9點到11AM）進行播放。 透過 HLS 串流處理時，串流 URL 看起來會像這樣 `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` 。 若要選取部分記錄，您可以加入 startTime 和 endTime 參數，例如： `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` 。 因此，時間範圍篩選器是 URL 修飾詞，用來描述串流資訊清單中所包含之錄製時間軸的部分：

* `starttime`是 ISO 8601 日期時間戳記記，會在傳回的資訊清單中描述影片時間軸的所需開始時間。
* `endtime`是 ISO 8601 日期時間戳記記，描述資訊清單中傳回之影片時間軸的所需結束時間。

這類資訊清單的最大長度（以時間為單位）不能超過24小時。

以下是篩選準則的限制。

|startTime| EndTime |結果|
|---|---|---|
|Absent |Absent |傳回資產中影片的最新部分，最大長度為4小時。<br/><br/>如果尚未將資產寫入（目前未傳入任何新的影片資料），則會傳回隨選（VoD）資訊清單。 否則，會傳回即時資訊清單。|
|存在|Absent|    傳回資訊清單，其中包含比 startTime 更新的任何可用影片（如果這類資訊清單會縮短24小時）。<br/>如果資訊清單的長度超過24小時，則會傳回錯誤。<br/>如果尚未將資產寫入（目前未傳入任何新的影片資料），則會傳回隨選（VoD）資訊清單。 否則，會傳回即時資訊清單。
|Absent|存在 |錯誤–如果有 startTime，則 endTime 是強制的。|
|存在|存在|傳回 VoD 資訊清單，其中包含 startTime 和 endTime 之間可用的任何影片。<br/>Span （startTime、endTime）不能超過24小時。|

### <a name="response-examples"></a>回應範例  

#### <a name="example-1-live-recording-with-no-gaps"></a>範例1：沒有間距的即時錄製

以下是顯示2019年12月21日的所有可用媒體的回應，其中記錄為100% 完成。 回應只有一個開始/結束配對。

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

當錄製持續進行時，只要範圍是24小時或更少，您就可以在該開始/結束配對內的任何時間部分要求 HLS 或破折號資訊清單。 針對上述的4小時 HLS 資訊清單要求，其範例為：

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>範例2：具有2秒間距的即時錄製

基於某些原因，相機無法在一天的2秒間隔內傳送有效的影片。 以下是顯示2019年12月21日可用媒體的回應：

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

使用上述的錄音時，只要範圍低於24小時，您就可以使用任何 startTime/endTime 配對來要求 HLS 和破折號資訊清單。 如果這些值 straddled 為04：01： 08AM UTC 的間隙，則傳回的資訊清單會根據這些通訊協定的相關規格，對媒體時間軸中的不中斷發出信號。

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>範例3：具有8小時間距的即時錄製

假設攝影機和/或內部部署設施在一天內的8小時內斷電，從上午4點 UTC 到中午12：00，而且沒有備份電源來源。 這是一項回應，顯示這一天所有可用的媒體。

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

使用這類記錄：

* 如果您要求的資訊清單中的 startTime/endTime 範圍篩選器位於時間軸的「可用」部分內（也就是從午夜到4AM，或是從中午到午夜），則服務會傳回資訊清單，其中涵蓋 startTime 到 endTime 的時間，例如：

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* 如果您要求的資訊清單中的 startTime 和 endTime 位於中間的 ' 洞 ' 內（例如從上午8點到 10AM UTC），則服務的行為會與資產篩選準則相同，因此會產生空的結果。

    [這是取得空白回應的要求]`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* 如果您要求資訊清單，其中只有其中一個 startTime 或 endTime 位於 ' 洞 ' 內，則傳回的資訊清單只會包含該時間範圍的一部分。 它會將 startTime 或 endTime 值貼齊最接近的有效界限。 例如，如果您要求從10AM 到下午3小時的串流，則回應會包含1小時內12中午到下午的媒體

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    傳回的資訊清單會從 2019-12-21T12：00： 00.000 Z 開始，即使要求開始10AM 也一樣。 建立具有播放程式外掛程式的影片管理系統時，應該小心處理檢視器的動作。 不知道的檢視器會與播放時間軸從中午開始的原因混淆，而不會如要求10AM

## <a name="recording-and-playback-latencies"></a>錄音和播放延遲

在 IoT Edge 上使用即時影片分析來記錄資產時，您會指定 segmentLength 屬性，告知模組在將影片記錄到雲端之前，先匯總最小持續時間（以秒為單位）。 例如，如果 segmentLength 設定為300，則在上傳5分鐘「區塊」之前，模組會累積5分鐘的影片，然後在接下來的5分鐘內進入累積模式，然後再上傳一次。 增加 segmentLength 的優點是降低 Azure 儲存體的交易成本，因為讀取和寫入的數目將不會超過每個 segmentLength 秒一次。

因此，媒體服務的影片串流將會延遲至少很長的時間。 

另一個因素是決定播放延遲（在相機前方發生事件的時間與播放裝置上的時間之間的延遲）是一組圖片的[GOP](https://en.wikipedia.org/wiki/Group_of_pictures)持續時間。 藉[由使用3個簡單的技巧來減少即時串流的延遲](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)，將會更長的 GOP 持續時間，延遲時間就愈長。 監視和安全性案例中使用的 IP 相機通常會設定為使用超過30秒的 Gop。 這對整體延遲有很大的影響。

## <a name="next-steps"></a>後續步驟

[連續影片錄製教學課程](continuous-video-recording-tutorial.md)
