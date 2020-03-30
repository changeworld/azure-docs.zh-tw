---
title: Azure 媒體服務 - 即時流中信號時點中繼資料
description: 此規範概述了在引入和資料流到 Azure 媒體服務時發出發信號的時令中繼資料的方法。 這包括支援通用時令中繼資料信號 （ID3），以及用於廣告插入和拼接狀態信令的 SCTE-35 信令。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137317"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>針對即時串流中的計時中繼資料傳送訊號 

上次更新時間： 2019-08-22

### <a name="conformance-notation"></a>一致性標記法

此文件中「必須」、「不得」、「必要」、「應」、「不應」、「應該」、「不應該」、「建議」、「可能」與「選擇性」需依 RFC 2119 所述來解釋

## <a name="1-introduction"></a>1. 簡介 

為了在用戶端播放機上插入廣告或自訂中繼資料事件，廣播公司通常使用嵌入在視頻中的定時中繼資料。 為了啟用這些方案，Media Services 支援將時時中繼資料從即時流道的引入點傳輸到用戶端應用程式。
此規範概述了媒體服務支援的即時流信號中時點中繼資料的幾種模式。

1. [SCTE-35] 符合 [SCTE-35]、[SCTE-214-1]、[SCTE-214-3] 和 [RFC8216] 概述的標準的信號

2. [SCTE-35] 符合 RTMP 廣告信令的傳統 [Adobe-Primetime] 規範的信號。
   
3. 泛型定時中繼資料信令模式，用於**非**[SCTE-35] 並且可以攜帶應用程式開發人員定義的其他自訂架構的消息。

## <a name="11-terms-used"></a>1.1 使用的術語

| 詞彙                | 定義                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 廣告中斷            | 可以安排一個或多個廣告投放的地點或時間點;與使用和安置機會相同。                                                                                                                     |
| 廣告決策服務 | 決定向使用者展示哪些廣告和持續時間的外部服務。 這些服務通常由合作夥伴提供，並且不在此文檔範圍內。                                                                    |
| 提示                 | 指示即將發佈的廣告中斷的時間和參數。 請注意，提示可以指示即將切換到廣告中斷，在廣告中斷期間切換到下一個廣告，以及從廣告中斷切換到主要內容。           |
| 封裝程式            | Azure 媒體服務"流端點"為 DASH 和 HLS 提供動態打包功能，在媒體行業中稱為"打包器"。                                                                              |
| 呈現時間   | 事件呈現給檢視者的時間。 此時間代表使用者會看到事件的媒體時間軸位置。 例如，SCTE-35 splice_info() 命令訊息的呈現時間是 splice_time()。 |
| 抵達時間        | 事件訊息抵達的時間。 此時間通常與事件的呈現時間截然不同，因為事件訊息是在事件的呈現時間之前傳送。                                                    |
| 疏鬆資料軌        | 不連續且時間已與父代或控制播放軌同步的媒體播放軌。                                                                                                                                                  |
| 來源              | Azure 媒體串流服務                                                                                                                                                                                                             |
| 通道接收        | Azure 媒體即時串流服務                                                                                                                                                                                                        |
| HLS                 | Apple HTTP 即時串流通訊協定                                                                                                                                                                                                            |
| DASH                | HTTP 上的動態自適性串流                                                                                                                                                                                                          |
| Smooth              | Smooth Streaming 通訊協定                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG 2 傳輸串流                                                                                                                                                                                                                      |
| RTMP                | 即時多媒體通訊協定                                                                                                                                                                                                                 |
| uimsbf              | 不帶正負號的整數，最顯著位元在前。                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 規範性參考

下列檔載有一些規定，通過本案文的參考，這些條款構成了本檔的規定。 所有檔均需經標準機構修訂，並鼓勵讀者調查是否可能應用下面列出的文檔的最新版本。 還提醒讀者，引用文檔的較新版本可能與 Azure 媒體服務的定時中繼資料規範的此版本不相容。


| 標準          | 定義                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-黃金時間] | [黃金時段數位程式插入信號規範 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-快閃記憶體-AS]  | [FLASH 操作指令碼語言參考](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["操作消息格式 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH 行業論壇互通指導 v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | 用於 HTTP 即時流式處理的時點中繼資料 -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [通用媒體應用程式格式 （CMAF） 中的時點中繼資料](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 標籤版本 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12：第12部分 ISO 基本介質檔案格式，第四版 2012-07-15                                                                                                                                 |
| [米佩達什]        | 資訊技術 -- -- 通過 HTTP （DASH） 進行動態自我調整流流 -- 第 1 部分：媒體演示描述和分段格式。 2014 年 5 月。 發表。 URL： https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | 資訊技術 -- -- 多媒體應用格式 （MPEG-A） -- -- 第 19 部分：分段媒體的共同媒體應用格式 （CMAF）。 2018 年 1 月。 發表。 URL： https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 資訊技術 -- -- MPEG 系統技術 -- -- 第 7 部分：ISO 基礎媒體檔案格式檔中的常見加密。 2016 年 2 月。 發表。 URL： https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["微軟平滑流式處理協定"，2014 年 5 月 15 日](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-ingest]  | [Azure 媒體服務 碎片 MP4 即時引入規範](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. 潘托斯，埃德;W. 五月。 HTTP 即時流式處理。 2017 年 8 月。 資訊。 [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Base16、Base32 和 Base64 資料編碼 -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Adobe 的即時消息協定"，2012 年 12 月 21 日](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35： 2019 - 數位程式插入提示消息電纜 -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – 基於 IP 的電纜服務 MPEG DASH 第 1 部分：MPD 約束和擴展                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH，適用于基於 IP 的有線服務第 3 部分：DASH/FF 設定檔                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 = 事件調度和通知介面                                                                                                                                                  |
| [SCTE-250]        | 事件和信令管理 API （ESAM）                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. 時令中繼資料引入

Azure 媒體服務支援 [RTMP] 和平滑流式處理 [MS-SSTR-Ingest] 協定的即時帶內中繼資料。 即時中繼資料可用於定義自訂事件，使用您自己的唯一自訂架構（JSON、二進位、XML），以及行業定義的格式（如 ID3 或 SCTE-35）來定義廣播流中的廣告信令。 

本文詳細介紹了如何使用 Azure 媒體服務的支援引入協定發送自訂定時中繼資料信號。 本文還介紹了 HLS、DASH 和平滑流的清單如何用時時中繼資料信號進行修飾，以及在使用 HLS 的 CMAF （MP4 片段） 或傳輸流 （TS） 段交付內容時，如何在帶內傳輸。 

時時中繼資料的常見用例方案包括：

 - SCTE-35 廣告信號，用於觸發現場事件或線性廣播中的廣告中斷
 - 自訂 ID3 中繼資料，可在用戶端應用程式（瀏覽器、iOS 或 Android）觸發事件
 - 自訂定義的 JSON、二進位或 XML 中繼資料，以在用戶端應用程式觸發事件
 - 來自即時編碼器、IP 攝像機或無人機的遙測
 - 來自 IP 攝像機的事件，如運動、人臉檢測等。
 - 來自行動攝像機、無人機或行動裝置的地理位置資訊
 - 歌詞
 - 線性即時源上的程式邊界
 - 要顯示在即時源上的圖像或增強中繼資料
 - 運動比分或遊戲時鐘資訊
 - 瀏覽器中視頻旁邊顯示的互動式廣告包
 - 測驗或投票
  
Azure 媒體服務即時事件和打包程式能夠接收這些時時中繼資料信號並將其轉換為中繼資料流，這些中繼資料流可以使用基於標準的協定（如 HLS 和 DASH）到達用戶端應用程式。


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 時時中繼資料

[RTMP] 協定允許為各種方案（包括自訂中繼資料和 SCTE-35 廣告信號）發送定時中繼資料信號。 

廣告信號（提示消息）作為嵌入在 [RTMP] 流中的 [AMF0] 提示消息發送。 提示消息可能在實際事件或 [SCTE35] 廣告拼接信號需要發生之前的某個時間發送。 為了支援此方案，事件的實際表示時間戳記將在提示消息中發送。 如需詳細資訊，請參閱 [AMF0]。

Azure 媒體服務支援以下 [AMF0] 命令，用於 RTMP 攝用：

- **onUserDataEvent** - 用於自訂中繼資料或 [ID3v2] 定時中繼資料
- **onAdCue** - 主要用於在即時流中發出廣告放置機會的信號。 支援兩種形式的提示，一種是簡單的模式和一種"SCTE-35"模式。 
- **onCuePoint** - 某些本地硬體編碼器（如元素即時編碼器）支援，用於向 [SCTE35] 消息發出信號。 
  

下表描述了媒體服務將同時為"簡單"和 [SCTE35] 消息模式引入的 AMF 消息負載的格式。

[AMF0] 消息的名稱可用於區分同一類型的多個事件流。  對於 [SCTE-35] 消息和"簡單"模式，AMF 消息的名稱必須按照 [Adobe-Primetime] 規範的要求"onAdCue"。。  下面未列出的任何欄位在引入時都會被 Azure 媒體服務忽略。

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP，使用"onUserDataEvent"自訂中繼資料

如果要使用 RTMP 協定從上游編碼器、IP 攝像機、無人機或設備提供自訂中繼資料源，請使用"onUserDataEvent"[AMF0] 資料來源命令類型。

**"onUserDataEvent"** 資料消息命令必須攜帶消息負載，其定義如下，由媒體服務捕獲並打包到帶內檔案格式以及 HLS、DASH 和平滑流的清單中。
建議每 0.5 秒（500 毫秒）發送一次定時中繼資料消息，否則可能會出現即時流的穩定性問題。 如果需要提供幀級中繼資料，每條消息都可以聚合來自多個幀的中繼資料。 如果要發送多位元率流，建議您也僅在單個位元速率上提供中繼資料，以減少頻寬並避免干擾視頻/音訊處理。 

**"onUserDataEvent"** 的有效負載應為 [MPEGDASH] 事件流 XML 格式消息。 這使得很容易傳遞自訂定義的架構，這些架構可以攜帶在 CMAF [MPEGCMAF] 內容的"emsg"有效負載中，這些內容通過 HLS 或 DASH 協定交付。 每個 DASH 事件流消息都包含一個 schemeIdUri，該方案充當 URN 消息方案識別碼並定義消息的有效負載。 某些方案，如https://aomedia.org/emsg/ID3[ID3v2] 或**urn：scte：scte：scte35：2013：bin** [SCTE-35] 由行業聯合體標準化，實現互通性。 任何應用程式提供程式都可以使用他們控制的 URL（擁有域）定義自己的自訂方案，如果選擇，則可以在該 URL 上提供規範。 如果播放機具有已定義方案的處理常式，則這是需要瞭解有效負載和協定的唯一元件。

[MPEG-DASH] 事件流 XML 負載的架構定義為（摘自 DASH ISO-IEC-23009-1-3 版）。 請注意，此時僅支援每個"事件流"一個"事件種類"。 如果**事件流**中提供了多個事件，則僅處理第一個**事件**元素。

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>具有 ID3 架構 ID 和 base64 編碼資料負載的 XML 事件流示例。  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>具有自訂架構 ID 和 base64 編碼二進位資料的事件流示例  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>具有自訂架構 ID 和自訂 JSON 的示例事件流  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>內置支援的方案 ID URI
| 方案 ID URI                 | 描述                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HTTPs：\//aomedia.org/emsg/ID3 | 描述如何在相容 CMAF 的 [MPEGCMAF] 碎片 MP4 中作為定時中繼資料進行 [ID3v2] 中繼資料。 有關詳細資訊，請參閱[通用媒體應用程式格式 （CMAF） 中的時時中繼資料](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>事件處理和清單信令

收到有效的 **"onUserDataEvent"** 事件後，Azure 媒體服務將查找與事件StreamType（在 [MPEGDASH] 中定義）匹配的有效 XML 負載，解析 XML 負載並將其轉換為 [MPEGCMAF] MP4 片段"emsg"版本 1 框，用於存儲在即時存檔中並傳輸到媒體服務打包器。   打包程式將檢測即時流中的"emsg"框，並：

- （a） 將其"動態打包"到 TS 段，以便按照 HLS 時時中繼資料規範 [HLS-TMD] 交付給 HLS 用戶端，或
- （b） 通過 HLS 或 DASH 以 CMAF 片段傳遞，或 
- （c） 將其轉換為稀疏軌道信號，以便通過平滑流式處理 [MS-SSTR] 傳遞。

除了用於 HLS 的帶內"emsg"格式 CMAF 或 TS PES 資料包外，DASH （MPD） 和平滑流的清單將包含對帶內事件流的引用（在平滑流中也稱為稀疏流跟蹤）。 

單個事件或其資料負載不會直接在 HLS、DASH 或平滑清單中輸出。 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>onUserDataEvent 事件的其他資訊約束和預設值

- 如果未在 EventStream 元素中設置時間刻度，則預設情況下使用 RTMP 1 kHz 時間刻度
- onUserDataEvent 消息的傳遞限制為最多每 500 毫秒一次。如果更頻繁地發送事件，則可能會影響即時饋送的頻寬和穩定性

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP 廣告提示信號與"onAdCue"

Azure 媒體服務可以偵聽和回應多個 [AMF0] 訊息類型，這些訊息類型可用於在即時流中發出各種即時同步中繼資料的信號。  [Adobe-Primetime] 規範定義了兩種稱為"簡單"和"SCTE-35"模式的提示類型。 對於"簡單"模式，媒體服務支援單個 AMF 提示消息，稱為"onAdCue"，使用與下面為"簡單模式"信號定義的表匹配的有效負載。  

以下部分顯示 RTMP"簡單"模式"有效負載，可用於發出基本"拼接"廣告信號的信號，該信號將傳送到 HLS、DASH 和 Microsoft 平滑流道的用戶端清單。 這對於客戶沒有複雜的基於 SCTE-35 的廣告信號部署或插入系統，並且使用基本的本地編碼器通過 API 發送提示消息的情況非常有用。 通常，本地編碼器將支援基於 REST 的 API 來觸發此信號，該信號還將通過將 IDR 幀插入到視頻中並啟動新的 GOP 來"拼接條件"視頻流。

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP 廣告提示信號與"onAdCue" - 簡單模式

| 欄位名稱 | 欄位類型 | 必要項？ | 說明                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | String     | 必要  | 事件訊息。  應該是 "SpliceOut" 以指定簡單模式拼接。                                                                                                                                                                                                         |
| id         | String     | 必要  | 描述拼接或區段的唯一識別碼。 識別訊息的此執行個體                                                                                                                                                                                       |
| duration   | Number     | 必要  | 拼接的持續時間。 單位是小數秒。                                                                                                                                                                                                                           |
| elapsed    | Number     | 選用  | 當訊號重複以支援收聽時，此欄位應該是開始拼接後經過的呈現時間量。 單位是小數秒。 當使用簡單模式時，此值不應該超過拼接的原始時間長度。 |
| time       | Number     | 必要  | 應該是拼接的時間 (以呈現時間表示)。 單位是小數秒。                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>使用 Adobe RTMP 簡單模式時，示例 MPEG DASH 清單輸出

使用[Adobe 簡單模式查看示例 3.3.2.1 MPEG DASH .mpd 事件流](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

請參閱[示例 3.3.3.1 具有單週期和 Adobe 簡單模式](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)的 DASH 清單

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>使用 Adobe RTMP 簡單模式時的示例 HLS 清單輸出

請參閱[使用 Adobe 簡單模式和 EXT-X-CUE 標記的示例 3.2.2 HLS 清單](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP 廣告提示信號與"onAdCue" - SCTE-35 模式

當您使用更高級的廣播製作工作流時，該工作流需要將完整的 SCTE-35 有效負載消息傳遞到 HLS 或 DASH 清單，則最好使用 [Adobe-Primetime] 規範的"SCTE-35 模式"。  此模式支援直接發送到本地即時編碼器的帶內 SCTE-35 信號，然後使用 [Adobe-Primetime] 規範中指定的"SCTE-35 模式"將信號編碼到 RTMP 流中。 

通常 SCTE-35 消息只能出現在本地編碼器上的 MPEG-2 傳輸流 （TS） 輸入中。 有關如何配置包含 SCTE-35 的傳輸流引入並啟用它在 Adobe SCTE-35 模式下傳遞到 RTMP 的詳細資訊，請諮詢編碼器製造商。

在這種情況下，必須使用 **"onAdCue"** [AMF0] 訊息類型從本地編碼器發送以下有效負載。

| 欄位名稱 | 欄位類型 | 必要項？ | 說明                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 提示        | String     | 必要  | 事件訊息。  對於 [SCTE-35] 消息，這必須是基64編碼的 [RFC4648] 二進位splice_info_section（），以便消息發送到 HLS、平滑和 Dash 用戶端。                                                                                                                                                                                                                               |
| type       | String     | 必要  | 可識別訊息配置的 URN 或 URL。 對於 [SCTE-35] 消息 **，這應該為****"scte35"，** 以便按照 [Adobe-Primetime] 將消息發送到 HLS、平滑和 Dash 用戶端。 或者，URN"urn：scte：scte35：2013：bin"也可用於發出 [SCTE-35] 消息的信號。                                                                                                        |
| id         | String     | 必要  | 描述拼接或區段的唯一識別碼。 識別訊息的此執行個體。  具有同等語意的訊息應該有相同的值。                                                                                                                                                                                                                                                       |
| duration   | Number     | 必要  | 事件或廣告拼接區段的期間 (若已知)。 如果未知，則值**應**為 0。                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Number     | 選用  | 當 [SCTE-35] 廣告訊號重複以收聽時，此欄位應該是開始拼接後經過的呈現時間量。 單位是小數秒。 在 [SCTE-35] 模式中，此值可能會超過原本指定的拼接或區段持續時間。                                                                                                                   |
| time       | Number     | 必要  | 事件或拼接的呈現時間。  演示時間和持續時間**應**與 [ISO-14496-12] 附件一中定義的類型 1 或 2 的流存取點 （SAP） 一致。對於 HLS 出口，時間和持續時間**應**與段邊界對齊。 呈現時間與相同事件串流中不同事件訊息的期間不能重疊。 單位是小數秒。 |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>示例 MPEG DASH .mpd 清單與 SCTE-35 模式
參見[第 3.3.3.2 節示例 DASH 清單與 SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>示例 HLS 清單 .m3u8 與 SCTE-35 模式信號
參見[第 3.2.1.1 節示例 HLS 清單，以及 SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP 廣告信號與"onCuePoint"元素直播

元素即時本地編碼器支援 RTMP 信號中的廣告標記。 Azure 媒體服務目前僅支援 RTMP 的"OnCuePoint"廣告標記類型。  通過將 **"ad_markers"** 設置為"onCuePoint"，可以在元素媒體即時編碼器設置或 API 中的 Adobe RTMP 組設置中啟用此功能。  有關詳細資訊，請參閱元素即時文檔。 在 RTMP 組中啟用此功能會將 SCTE-35 信號傳遞給由 Azure 媒體服務處理的 Adobe RTMP 輸出。

"onCuePoint"訊息類型在 [Adobe-Flash-AS] 中定義，並且從元素即時 RTMP 輸出發送時具有以下有效負載結構。


| 屬性   | 描述                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NAME       | 名稱應為元素直播的 **"scte35"。**                                                                                                                                                                              |
| time       | 提示點在時間軸期間出現在視頻檔中的時間（以秒為單位）                                                                                                                                           |
| type       | 提示點的類型應設置為"**事件**"。                                                                                                                                                                             |
| 參數 | 名稱/值對字串的關聯陣列，其中包含來自 SCTE-35 消息的資訊，包括 Id 和持續時間。 這些值由 Azure 媒體服務解析，並包含在清單修飾標記中。 |


使用此廣告標記模式時，HLS 清單輸出類似于 Adobe"簡單"模式。


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>示例 MPEG DASH MPD，單週期，Adobe 簡單模式信號

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>示例 HLS 播放清單，Adobe 簡單模式信號使用 EXT-X-CUE 標記（截斷的"..."簡潔）

下面的示例顯示了使用 Adobe"簡單"模式信號和舊版 [Adobe-Primetime] EXT-X-CUE 標記的 RTMP 引入流的媒體服務動態打包器的輸出。  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 取消和更新

您可以透過傳送具有相同呈現時間與 ID 的多個訊息來取消或更新訊息。 呈現時間與 ID 可唯一識別事件，而且針對符合預滾動限制式之特定呈現時間所接收的最後一個訊息是針對其採取動作的訊息。 更新的事件會取代先前接收的任何訊息。 預滾動限制式是四秒。 系統將會針對在呈現時間前最後四秒接收的訊息採取動作。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 分散的 MP4 內嵌 (Smooth Streaming)

有關即時流攝閱的要求，請參閱 [MS-SSTR-Ingest]。 以下各節提供有關計時呈現中繼資料內嵌的詳細資料。  定時演示中繼資料被引入為稀疏軌道，在即時伺服器清單盒（請參閱 MS-SSTR）和影片盒（"moov"）中定義。  

每個稀疏片段由影片片段框（"moof"）和媒體資料框（"mdat"）組成，其中"mdat"框是二進位消息。

為了實現廣告的幀精確插入，編碼器必須在需要插入提示的演示時拆分片段。  必須創建一個新片段，該片段以 [ISO-14496-12] 附件 I 中定義的新創建的 IDR 幀或類型 1 或 2 的流存取點 （SAP） 開頭。這允許 Azure 媒體打包程式正確生成 HLS 清單和 DASH 多週期清單，其中新週期以幀精確拼接條件表示時間開始。

### <a name="221-live-server-manifest-box"></a>2.2.1 即時伺服器資訊清單方塊

稀疏軌道**必須在**包含**\<文本流\>** 條目的即時伺服器清單框中**聲明，並且必須**設置以下屬性：

| **屬性名稱** | **欄位類型** | **必填？** | **描述**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Number         | 必要      | **必須**為"0"，指示具有未知變數位元速率的軌道。                                                                                                                                                          |
| parentTrackName    | String         | 必要      | **必須是**父軌道的名稱，稀疏軌道時間代碼與父軌道的時間刻度對齊。 父系播放軌不能是疏鬆播放軌。                                                                             |
| manifestOutput     | Boolean        | 必要      | **必須**為"true"，以指示稀疏軌道將嵌入到平滑用戶端清單中。                                                                                                                        |
| Subtype            | String         | 必要      | **必須是**四個字元代碼"DATA"。                                                                                                                                                                                  |
| 配置             | String         | 必要      | **必須是**標識消息方案的 URN 或 URL。 對於 [SCTE-35] 消息，這**必須**為"urn：scte：scte35：2013：bin"，以便按照 [SCTE-35] 將消息發送到 HLS、平滑和 Dash 用戶端。 |
| trackName          | String         | 必要      | **必須是**稀疏軌道的名稱。軌道名稱可用於使用相同的方案區分多個事件流。 每個唯一事件流**都必須**有一個唯一的軌道名稱。                                |
| timescale          | Number         | 選用      | **必須**為父軌道的時間刻度。                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 影片方塊

影片盒（"moov"）遵循即時伺服器清單框作為稀疏軌道的流標頭的一部分。

"moov"框**應**包含 [ISO-14496-12] 中定義的**TrackHeaderBox （'tkhd'）** 框，其中包含以下約束：

| **欄位名稱** | **欄位類型**          | **必填？** | **描述**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 64 位元不帶正負號的整數 | 必要      | **應**為 0，因為軌道框的樣本為零，軌道框中的樣本總持續時間為 0。 |

---

"moov"框**應**包含 [ISO-14496-12] 中定義的**處理常式框 （"hdlr"），** 其中包含以下約束：

| **欄位名稱** | **欄位類型**          | **必填？** | **描述**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32 位元不帶正負號的整數 | 必要      | **應該是**"元"。 |

---

"stsd"框**應**包含一個 MetaDataSampleentry 框，其中包含 [ISO-14496-12] 中定義的編碼名稱。  例如，對於 SCTE-35 消息，編碼名稱**應**為"scte"。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 影片片段方塊與媒體資料方塊

稀疏軌道片段由影片片段框（"moof"）和媒體資料框（"mdat"）組成。

> [!NOTE]
> 為了實現廣告的幀精確插入，編碼器必須在需要插入提示的演示時拆分片段。  必須創建一個新的片段，該片段以 [ISO-14496-12] 附件 I 中定義的新創建的 IDR 幀或類型 1 或 2 的流存取點 （SAP） 開頭
> 

"電影片段框"（"moof"）框**必須**包含在 [MS-SSTR] 中定義的**軌道片段擴展標題框 （"uuid"）** 框，其中包含以下欄位：

| **欄位名稱**         | **欄位類型**          | **必填？** | **描述**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64 位元不帶正負號的整數 | 必要      | **必須是**活動到達時間。 此值與父代播放軌的訊息一致。           |
| fragment_duration      | 64 位元不帶正負號的整數 | 必要      | **必須是**事件的持續時間。 持續時間可以是零，指出持續時間未知。 |

---


MediaDataBox （"mdat"） 框**必須**具有以下格式：

| **欄位名稱**          | **欄位類型**                   | **必填？** | **描述**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32 位元不帶正負號的整數 (uimsbf) | 必要      | 確定"mdat"框內容的格式。 將會忽略無法辨識的版本。 目前唯一支援的版本是 1。                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 位元不帶正負號的整數 (uimsbf) | 必要      | 識別訊息的此執行個體。 具有對等語意的訊息應該有相同的值；亦即，處理具有相同 id 的任一事件訊息就已足夠。                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 位元不帶正負號的整數 (uimsbf) | 必要      | 在 Track片段擴展標題框中指定的fragment_absolute_time和presentation_time_delta**必須**為事件的表示時間。 演示時間和持續時間**應**與 [ISO-14496-12] 附件一中定義的類型 1 或 2 的流存取點 （SAP） 一致。對於 HLS 出口，時間和持續時間**應**與段邊界對齊。 同一事件流中不同事件消息的表示時間和持續時間**不得**重疊。 |
| message                 | 位元組陣列                       | 必要      | 事件訊息。 對於 [SCTE-35] 消息，消息是二進位splice_info_section。" 對於 [SCTE-35] 消息，這**必須是**splice_info_section（），以便按照 [SCTE-35] 將消息發送到 HLS、平滑和 Dash 用戶端。 對於 [SCTE-35] 消息，二進位splice_info_section（） 是"mdat"框的有效負載，**它不是**基於 64 編碼的。                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 取消與更新

您可以透過傳送具有相同呈現時間與 ID 的多個訊息來取消或更新訊息。  呈現時間與 ID uniquely 可唯一識別事件。 針對符合預滾動限制式之特定呈現時間所接收的最後一個訊息是針對其採取動作的訊息。 更新的訊息會取代先前接收的任何訊息。  預滾動限制式是四秒。 系統將會針對在呈現時間前最後四秒接收的訊息採取動作。 


## <a name="3-timed-metadata-delivery"></a>3 計時中繼資料傳遞

事件串流資料對「媒體服務」而言是不透明的。 「媒體服務」只會在內嵌端點與用戶端端點之間傳送三小部分的資訊。 以下屬性根據 [SCTE-35] 和/或用戶端的流式處理協定交付給用戶端：

1.  配置 – 可識別訊息配置的 URN 或 URL。
2.  呈現時間 – 媒體時間軸上之事件的呈現時間。
3.  持續時間 – 事件的持續時間。
4.  ID – 選擇性的事件唯一識別碼。
5.  訊息 – 事件資料。

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 微軟平滑流式處理清單  

有關如何格式化稀疏消息軌道的詳細資訊，請參閱稀疏軌道處理 [MS-SSTR]。對於 [SCTE35] 消息，平滑流將輸出基64編碼的splice_info_section（） 到稀疏片段。
流索引**必須**具有"DATA"的子類型，並且自訂屬性**必須**包含名稱="Schema"和 Value="urn：scte：scte：scte35：2013：bin"的屬性。

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>顯示基本 64 編碼 [SCTE35] splice_info_section（） 的平滑用戶端清單示例
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 蘋果HLS清單裝飾

Azure 媒體服務支援以下 HLS 清單標籤，用於在即時或點播事件期間發出廣告可用性資訊信號。 

- 在蘋果 HLS [RFC8216] 中定義的 EXT-X-DATERANGE
- EXT-X-CUE 在 [Adobe-Primetime] 中定義 - 此模式被視為"舊版"。 客戶應盡可能採用 EXT-X-DATERANGE 標記。

每個標記的資料輸出將因所使用的引入信號模式而異。 例如，使用 Adobe Simple 模式的 RTMP 攝錄不包含完整的 SCTE-35 基64 編碼有效負載。

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 蘋果HLS與EXT-X-DATERANGE（推薦）

Apple HTTP 即時流式處理 [RFC8216] 規範允許對 [SCTE-35] 消息發出信號。 消息將插入到分段播放清單中，每個 [RFC8216] 部分的 EXT-X-DATERANGE 標籤中，標題為"將 SCTE-35 映射到 EXT-X-DATERANGE"。  用戶端應用程式層可以解析 M3U 播放清單並處理 M3U 標記，或通過 Apple 播放機框架接收事件。  

Azure 媒體服務（版本 3 API）中的**推薦**方法是遵循 [RFC8216]，並在清單中使用 [SCTE35] 廣告可用性修飾的 EXT-X_DATERANGE 標記。

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 示例 HLS 清單 .m3u8 顯示 SCTE-35 的 EXT-X-DATERANGE 信令

以下示例來自媒體服務動態打包器的 HLS 清單輸出顯示了使用來自 [RFC8216] 的 EXT-X-DATERANGE 標記，以指示流中的 SCTE-35 事件。 此外，此流包含 [Adobe-Primetime] 的"舊版"EXT-X-CUE 標記。

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 蘋果 HLS 與 Adobe 黃金時段 EXT-X-CUE（舊版）

Azure 媒體服務（版本 2 和 3 API）中還提供"遺留"實現，該實現使用 [Adobe-Primetime] "SCTE-35 模式"中定義的 EXT-X-CUE 標記。 在此模式下，Azure 媒體服務將在 EXT-X-CUE 標記中嵌入基64 編碼的 [SCTE-35] splice_info_section（）。  

"遺留"EXT-X-CUE 標記定義如下，也可以在 [Adobe-Primetime] 規範中進行規范引用。 這只能用於在需要時的舊式 SCTE35 信令，否則建議的標記在 [RFC8216] 中定義為 EXT-X-DATERANGE。 

| **屬性名稱** | **類型**                      | **必填？**                             | **描述**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 提示                | 加上括號的字串                 | 必要                                  | 消息編碼為 base64 編碼字串，如 [RFC4648] 中所述。 對於 [SCTE-35] 消息，這是基本編碼的splice_info_section（）。                                                                                                                                      |
| TYPE               | 加上括號的字串                 | 必要                                  | 可識別訊息配置的 URN 或 URL。 對於 [SCTE-35] 消息，類型採用特殊值"scte35"。                                                                                                                                                                          |
| ID                 | 加上括號的字串                 | 必要                                  | 事件的唯一識別碼。 如果內嵌訊息時未指定 ID，「Azure 媒體服務」將會產生一個唯一 ID。                                                                                                                                              |
| 持續時間           | 十進位浮點數數字 | 必要                                  | 事件的持續時間。 如果未知，則值**應**為 0。 單位是小數秒。                                                                                                                                                                                           |
| ELAPSED            | 十進位浮點數數字 | 選擇性，但對滑動視窗而言是必要的 | 當重複信號以支援滑動表示視窗時，此欄位**必須是**事件開始以來的表示時間量。 單位是小數秒。 此值可能會超過原本指定的拼接或區段持續時間。 |
| TIME               | 十進位浮點數數字 | 必要                                  | 事件的呈現時間。 單位是小數秒。                                                                                                                                                                                                                        |


HLS 播放程式應用程式層將會使用 TYPE 來識別訊息格式、將訊息解碼、套用必要時間轉換，以及處理事件。  根據事件時間戳記，事件時間在父代播放軌的區段播放清單中是同步的。  它們是插入在最接近的區段 (#EXTINF tag) 前面。

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 使用"傳統"Adobe PrimeTime EXT-X-CUE 的清單示例

下面的示例顯示了使用 Adobe Primetime EXT-X-CUE 標記進行 HLS 清單裝飾。  "CUE"參數僅包含 TYPE 和持續時間屬性，這意味著這是使用 Adobe"簡單"模式信令的 RTMP 源。  如果這是 SCTE-35 模式信號，則標籤將包括 base64 編碼的二進位 SCTE-35 有效負載，如[3.2.1.1 示例](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)所示。

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 HLS 消息處理"傳統" Adobe 黃金時段 EXT-X-CUE

事件在每個視頻和音軌的段播放清單中發出信號。EXT-X-CUE 標記的位置**必須始終**在第一個 HLS 段之前（對於拼接或段啟動）之前，或者緊接其時間和 DURATION 屬性引用的最後一個 HLS 段（對於中段或段端的拼接），這是 [Adobe-Primetime] 的要求。

啟用滑動表示視窗時，**必須**經常重複 EXT-X-CUE 標記，以便在段播放清單中始終完整地描述接頭或段，並且必須使用"條形 **"屬性來**指示接頭或段處於活動狀態的時間量（[Adobe-Primetime]]）。

當滑動呈現視窗啟用時，若它們所指的媒體時間已從滑動呈現視窗滾出，標籤會從區段播放清單移除。

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH 清單裝飾 （MPD）

[MPEGDASH] 提供了三種向事件發出信號的方法：

1.  MPD 事件流中發出信號的事件
2.  使用事件訊息方塊（"emsg"）在帶內發出信號的事件
3.  1 與 2 兩者的組合

MPD 事件流中發出信號的事件對於 VOD 流非常有用，因為用戶端在下載 MPD 時可以立即訪問所有事件。 它還可用於 SSAI 信令，下游 SSAI 供應商需要分析來自多週期 MPD 清單的信號，並動態插入廣告內容。  帶內 （'emsg'） 解決方案可用於即時流式處理，其中用戶端不需要再次下載 MPD，或者用戶端和源之間沒有 SSAI 清單操作。 

DASH 的 Azure 媒體服務預設行為是使用事件訊息方塊 （"emsg"） 在 MPD 事件流和帶內發出信號。

通過 [RTMP] 或 [MS-SSTR-Ingest] 引入的提示消息使用帶內"emsg"框和/或 MPD 事件流映射到 DASH 事件。 

DASH 的帶內 SCTE-35 信令遵循 [SCTE-214-3] 和 [DASH-IF-IOP] 第 13.12.2 節（"SCTE35 事件"）中定義和要求。 

對於帶內 [SCTE-35] 滑車，事件訊息方塊（"emsg"）使用方案 Id = "urn：scte：scte35：2013：bin"。 對於 MPD 清單裝飾，EventStream 方案Id使用"urn：scte：scte35：2014：xml_bin"。  此格式是事件的 XML 表示形式，其中包括到達引入的完整 SCTE-35 消息的二進位 base64 編碼輸出。 

DASH 中 [SCTE-35] 提示消息的運輸的規範參考定義可在 [SCTE-214-1] 秒 6.7.4 （MPD） 和 [SCTE-214-3] 秒 7.3.2（SCTE 35 提示消息的傳送） 中提供。

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH （MPD） 事件流信號

事件清單 （MPD） 修飾將使用事件流元素在 MPD 中發出信號，該元素顯示在"期間"元素中。 使用的 schemeId 是"urn：scte：scte35：2014：xml_bin"。

> [!NOTE]
> 為簡潔起見 [SCTE-35] 允許在 Signal.Binary 元素（而不是 Signal.SpliceInfoSection 元素）中使用 base64 編碼部分作為完全解析提示消息的傳遞的替代方法。
> Azure 媒體服務使用此"xml_bin"方法在 MPD 清單中發出信號。
> 這也是 [DASH-IF-IOP] 中使用的推薦方法 - 請參閱 DASH [IF IOP 指南標題為"廣告插入事件流"的部分](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

EventStream 元素具有下列屬性：

| **屬性名稱** | **類型**                | **必填？** | **描述**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | 字串                  | 必要      | 識別訊息的配置。 配置是設定為即時伺服器資訊清單方塊中 Scheme 屬性的值。 該值**應**為標識消息方案的 URN 或 URL;支援的輸出方案Id應為[SCTE-214-1] 秒 6.7.4 （MPD） 的"urn：scte：scte：scte：scte35：2014：xml_bin"，因為服務此時僅支援"xml_bin"，以便在 MPD 中簡潔。 |
| value              | 字串                  | 選用      | 由配置擁有者用來自訂訊息語意的額外字串值。 為了區分具有相同方案的多個事件流，**必須**將值設置為事件流的名稱（[MS-SSTR-Ingest] 的跟蹤名稱或 [RTMP] 引入的 AMF 消息名稱）。                                                                         |
| 時幅          | 32 位元不帶正負號的整數 | 必要      | 時間刻度，以每秒刻度為單位。                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 MPEG DASH 的示例事件流

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 使用 Adobe 簡單模式 RTMP 流的示例 MPEG DASH .mpd 清單信令

下面的示例顯示了使用 Adobe"簡單"模式信令的 RTMP 流的媒體服務動態打包器的摘錄事件流。

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 使用 Adobe SCTE-35 模式的 RTMP 流示例 MPEG DASH .mpd 清單信令

下面的示例顯示了使用 Adobe SCTE-35 模式信令的 RTMP 流的媒體服務動態打包器的摘錄事件流。

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> 請注意，演示時間是 [SCTE-35] 事件的演示時間，翻譯為相對於"期間開始時間"，而不是消息的到達時間。
> [MPEGDASH] 將Event@presentationTime定義為"指定事件相對於期間開始的表示時間。
> 以秒為單位的表示時間值是此屬性的值和EventStream@timescale屬性值的劃分。
> 如果不存在，則表示時間的值為 0。

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 使用 Adobe 簡單模式信號，使用單週期事件流示例 MPEG DASH 清單 （MPD）

下面的示例顯示了使用 Adobe"簡單"模式廣告信號方法源 RTMP 流的媒體服務動態打包器的輸出。 輸出是單個期間清單，顯示事件流使用 schemeId Uri 設置為"urn：com：adobe：DPI：簡單：2015"，value 屬性設置為"簡單信號"。
每個簡單信號都位於事件元素中@presentationTime，該元素具有@duration、@id和 基於傳入的簡單信號填充的屬性。

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 使用 Adobe SCTE35 模式信令的具有多週期事件流的 MPEG DASH 清單 （MPD） 示例

下面的示例顯示了使用 Adobe SCTE35 模式信令源 RTMP 流的媒體服務動態打包器的輸出。
在這種情況下，輸出清單是具有 EventStream 元素的多週期 DASH .mpd，@schemeIdUri屬性設置為"urn：scte：scte：scte35：2014：xml_bin"，@value屬性設置為"scte35"。 事件流中的每個事件元素都包含完整的基點64編碼二進位 SCTE35 信號 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH 帶內事件訊息方塊信號

頻內事件串流要求 MPD 在「自適性集合」層必須有 InbandEventStream 元素。  此元素具有強制性 schemeIdUri 屬性和可選的時間刻度屬性，該屬性也顯示在事件訊息方塊 （"emsg"） 中。  MPD 中未定義的方案識別碼的事件訊息方塊**不應**存在。

對於帶內 [SCTE-35] 滑車，信號**必須**使用 schemeId = "urn：scte：scte35：2013：bin"。
[SCTE-35] 帶內消息的運輸的規範定義在 [SCTE-214-3] 秒 7.3.2（SCTE 35 提示消息的傳送）中定義。

以下詳細資訊概述了用戶端在 "emsg"中應期望的特定值，符合 [SCTE-214-3]：

| **欄位名稱**          | **欄位類型**          | **必填？** | **描述**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | 字串                  | 必要      | 識別訊息的配置。 配置是設定為即時伺服器資訊清單方塊中 Scheme 屬性的值。 該值**必須是**標識消息方案的 URN。 對於 [SCTE-35] 消息，此**消息必須**為 "urn：scte：scte：scte35：2013：bin"，符合 [SCTE-214-3]          |
| 值                   | 字串                  | 必要      | 由配置擁有者用來自訂訊息語意的額外字串值。 若要區分具有相同配置的多個事件串流，值必須設定為事件串流的名稱 (適用於 Smooth 內嵌的 trackName，或適用於 RTMP 內嵌的 AMF 訊息名稱)。 |
| 時幅               | 32 位元不帶正負號的整數 | 必要      | 時間刻度（以每秒刻度為單位）的時間刻度，時間刻度和持續時間欄位位於"emsg"框中。                                                                                                                                                                                                            |
| presentation_time_delta | 32 位元不帶正負號的整數 | 必要      | 事件呈現時間與此區段中最早的呈現時間之間的媒體呈現時間差異。 演示時間和持續時間**應**與 [ISO-14496-12] 附件一中定義的類型 1 或 2 的流存取點 （SAP） 一致。                                  |
| event_duration          | 32 位元不帶正負號的整數 | 必要      | 事件的持續時間，或指定 0xFFFFFFFF 以指出未知的持續時間。                                                                                                                                                                                                                              |
| Id                      | 32 位元不帶正負號的整數 | 必要      | 識別訊息的此執行個體。 具有同等語意的訊息應該有相同的值。 如果內嵌訊息時未指定 ID，「Azure 媒體服務」將會產生一個唯一 ID。                                                                                        |
| Message_data            | 位元組陣列              | 必要      | 事件訊息。 對於 [SCTE-35] 消息，消息資料是符合 [SCTE-214-3] 的二進位splice_info_section（）                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Adobe 簡單模式的 InBandevenStream 實體示例
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 DASH 消息處理

事件在"emsg"框中為視頻和音訊軌道在帶內發出信號。  針對 presentation_time_delta 為 15 秒或低於 15 秒的所有區段要求，都會發生訊號處理。 

當滑動呈現視窗啟用時，若事件訊息的時間與持續時間總和小於資訊清單中媒體資料的時間，事件訊息會從 MPD 移除。  換句話說，當其所指之媒體時間已滾出滑動呈現視窗之外，就會從資訊清單移除事件訊息。

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 編碼器供應商的引入實施指南

以下準則是可能影響編碼器供應商實現此規範的常見問題。  以下準則是根據實際合作夥伴回饋收集的，以便更輕鬆地為其他人實施此規範。 

[SCTE-35] 消息使用方案 **"urn：scte：scte：scte35：2013：bin"** 為 [MS-SSTR-ingest] 和類型 **"scte35"** 為 [RTMP] 攝錄。 為促進 [SCTE-35] 計時的轉換 (以 MPEG-2 傳輸串流呈現時間戳記 (PTS) 為基礎)，PTS (pts_time + pts_adjustment of the splice_time()) 與媒體時間軸之間的對應是由事件呈現時間 (適用於 Smooth 內嵌的 fragment_absolute_time 欄位，或適用於 RTMP 內嵌的時間欄位) 所提供。 對應是必要的，因為 33 位元 PTS 值大約每隔 26.5 小時就會滾動一次。

平滑流式處理 （MS-SSTR-inge） 要求媒體資料框 （"mdat"）**必須**包含 [SCTE-35] 中定義的**splice_info_section（）。** 

對於 RTMP 攝取，AMF 消息的提示屬性設置為 [SCTE-35] 中定義的基64 編碼**splice_info_section（）。**  

當消息具有上述格式時，它們將發送到上述定義的 HLS、平滑和 DASH 用戶端。  

使用 Azure 媒體服務平臺測試實現時，請先使用"直通"LiveEvent 開始測試，然後再開始在編碼 LiveEvent 上進行測試。

---

## <a name="change-history"></a>變更記錄

| Date     | 變更                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | 為 SCTE35 支援添加了 RTMP 攝取，添加了 RTMP"OnCuePoint"，用於元素直播                                  |
| 08/22/19 | 已更新以將 OnUserDataEvent 添加到 RTMP 以用於自訂中繼資料                                                          |
| 1/08/20  | 修復了 RTMP 簡單和 RTMP SCTE35 模式下的錯誤。 從"上提示"更改為"上ACue"。 更新了簡單模式表。 |

## <a name="next-steps"></a>後續步驟
檢視媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
