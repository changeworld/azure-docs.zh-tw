---
title: 即時串流中的 Azure 媒體服務信號計時中繼資料
description: 此規格概述內嵌和串流至 Azure 媒體服務時，用來發出計時中繼資料信號的方法。 這包括對一般計時中繼資料信號（ID3）的支援，以及用於廣告插入和接合條件信號的 SCTE-35 信號。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79137317"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>針對即時串流中的計時中繼資料傳送訊號 

上次更新日期：2019-08-22

### <a name="conformance-notation"></a>一致性標記法

此文件中「必須」、「不得」、「必要」、「應」、「不應」、「應該」、「不應該」、「建議」、「可能」與「選擇性」需依 RFC 2119 所述來解釋

## <a name="1-introduction"></a>1. 簡介 

為了通知在用戶端播放機上插入廣告或自訂中繼資料事件，廣播者通常會使用在影片內內嵌的計時中繼資料。 若要啟用這些案例，媒體服務提供從即時串流通道的內嵌點傳輸計時中繼資料至用戶端應用程式的支援。
此規格概述即時串流信號中計時中繼資料媒體服務所支援的數種模式。

1. [SCTE-35] 符合 [SCTE-35]、[SCTE-214-1]、[SCTE-214-3] 和 [RFC8216] 所述標準的信號

2. [SCTE-35] 符合 RTMP 廣告信號的舊版 [Adobe-Primetime] 規格的信號。
   
3. 一般計時中繼資料信號模式，適用于**不**是 [SCTE-35] 的訊息，而且可能會包含應用程式開發人員所定義的 [ID3v2] 或其他自訂架構。

## <a name="11-terms-used"></a>1.1 使用的詞彙

| 詞彙                | 定義                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 廣告中斷            | 一或多個廣告可能排程傳遞的位置或時間點。與「可用」和「放置」商機相同。                                                                                                                     |
| Ad 決策服務 | 決定將對使用者顯示哪些 ad 和持續時間的外部服務。 服務通常是由合作夥伴提供，並超出本檔的範圍。                                                                    |
| 提示                 | 未來廣告中斷的時間和參數的指示。 請注意，提示可能表示擱置切換至廣告中斷、擱置切換到廣告中斷內的下一個 ad，以及從廣告中斷切換至主要內容。           |
| 封裝程式            | Azure 媒體服務「串流端點」提供了破折號和 HLS 的動態封裝功能，在媒體產業中稱為「包裝程式」。                                                                              |
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

## <a name="12-normative-references"></a>1.2 規範參考

下列檔包含布建，這份文字中的參考會構成本檔的條款。 所有檔都受限於標準本文的修訂，而讀者則建議您調查下列可能套用最新版本檔的可能性。 讀者也會提醒，較新版本的參考檔可能無法與此 Azure 媒體服務的計時中繼資料規格版本相容。


| 標準          | 定義                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime 數位程式插入信號規格1。2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [FLASH ActionScript 語言參考](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| AMF0            | [「動作訊息格式 AMF0」](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [破折號-IF-IOP]     | 虛線產業論壇 Interop 指導方針 v 4。2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | HTTP 即時串流的計時中繼資料-[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [一般媒體應用程式格式的計時中繼資料（CMAF）](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| ID3v2           | ID3 標記版本2.4。0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12：第12部分 ISO base media 檔案格式，FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | 資訊技術--透過 HTTP 的動態彈性串流（破折號）--第1部分：媒體簡報描述和區段格式。 5月2014。 發佈. URL： https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | 資訊技術--多媒體應用程式格式（MPEG）--第19部分：分段媒體的通用媒體應用程式格式（CMAF）。 2018年1月。 發佈. URL： https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 資訊技術--MPEG 系統技術--第7部分： ISO base media 檔案格式檔案中的一般加密。 2016年2月。 發佈. URL： https://www.iso.org/standard/68042.html                   |
| [MS-MS-SSTR]         | [「Microsoft Smooth Streaming Protocol」，2014的5月15日](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-內嵌]  | [Azure 媒體服務分散的未規範即時內嵌規格](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos，Ed.;W. 可能。 HTTP 即時串流。 2017年8月。 資訊。 [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| RFC4648         | Base16、Base32 和 Base64 資料編碼-[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | [「Adobe 的即時訊息通訊協定」，2012年12月21日](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35： 2019-適用于纜線的數位程式插入提示訊息-https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 –以 IP 為基礎的纜線服務的 MPEG 破折號第1部分： MPD 條件約束和延伸模組                                                                                                                 |
| [SCTE-214-3]      | 以 IP 為基礎的纜線服務的 SCTE 214-3 2015 MPEG 破折號第3部分：虛線/FF 設定檔                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 –事件排程和通知介面                                                                                                                                                  |
| [SCTE-250]        | 事件和信號管理 API （ESAM）                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. 計時中繼資料內嵌

Azure 媒體服務針對 [RTMP] 和 Smooth Streaming [MS-MS-SSTR] 通訊協定支援即時的頻內中繼資料。 即時中繼資料可用來定義自訂事件，包括您自己獨特的自訂架構（JSON、二進位、XML），以及產業定義的格式，例如 ID3，或 SCTE-35 用於廣播串流中的廣告信號。 

本文提供有關如何使用 Azure 媒體服務支援的內嵌通訊協定來傳送自訂計時中繼資料信號的詳細資訊。 本文也會說明如何使用計時中繼資料信號來裝飾 HLS、虛線和 Smooth Streaming 的資訊清單，以及如何在傳遞內容時，以 CMAF （片段）或傳輸資料流程（TS）區段進行 HLS。 

計時中繼資料的常見使用案例包括：

 - SCTE-35 廣告信號，以在實況活動或線性廣播中觸發廣告中斷
 - 可在用戶端應用程式（瀏覽器、iOS 或 Android）觸發事件的自訂 ID3 中繼資料
 - 自訂定義的 JSON、二進位或 XML 中繼資料，以在用戶端應用程式觸發事件
 - 來自即時編碼器、IP 攝影機或無人機的遙測
 - 來自 IP 攝影機的事件，例如動作、臉部偵測等等。
 - 來自行動相機、無人機或移動裝置的地理位置資訊
 - 歌曲歌詞
 - 線性即時摘要上的程式界限
 - 要顯示在即時摘要上的影像或增強的中繼資料
 - 運動分數或遊戲時鐘資訊
 - 要在瀏覽器中與影片一起顯示的互動式廣告套件
 - 測驗或投票
  
Azure 媒體服務即時事件和封裝程式能夠接收這些計時中繼資料信號，並將它們轉換成中繼資料的資料流程，以使用 HLS 和破折號之類的標準通訊協定來連接用戶端應用程式。


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 計時中繼資料

[RTMP] 通訊協定允許針對各種案例（包括自訂中繼資料和 SCTE-35 廣告信號）傳送計時中繼資料信號。 

廣告信號（提示訊息）會以內嵌于 [RTMP] 串流內的 [AMF0] 提示訊息形式傳送。 提示訊息可能會在實際事件或 [SCTE35] 廣告拼接信號必須發生之前傳送。 為了支援此案例，會在提示訊息內傳送事件的實際呈現時間戳記。 如需詳細資訊，請參閱 [AMF0]。

RTMP 內嵌的 Azure 媒體服務支援下列 [AMF0] 命令：

- **onUserDataEvent** -用於自訂中繼資料或 [ID3v2] 計時中繼資料
- **onAdCue** -主要用於向即時串流中的廣告放置機會發出信號。 支援兩種形式的提示，分別是簡單模式和 "SCTE-35" 模式。 
- **onCuePoint** -支援特定的內部部署硬體編碼器（例如 Elemental 即時編碼器）來發出信號 [SCTE35] 訊息。 
  

下表描述媒體服務將同時內嵌 "simple" 和 [SCTE35] 訊息模式的 AMF 訊息承載格式。

您可以使用 [AMF0] 訊息的名稱來區分相同類型的多個事件資料流程。  對於 [SCTE-35] 訊息和「簡單」模式，AMF 訊息的名稱必須是 [Adobe-Primetime] 規格中所需的 "onAdCue"。  在內嵌時，Azure 媒體服務應該忽略下面未列出的任何欄位。

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>透過使用 "onUserDataEvent" 的自訂中繼資料來 2.1.1 RTMP

如果您想要使用 RTMP 通訊協定，從上游編碼器、IP 攝影機、無人機或裝置提供自訂的中繼資料摘要，請使用 "onUserDataEvent" [AMF0] data message 命令類型。

**"OnUserDataEvent"** 資料訊息命令必須攜帶具有下列定義的訊息承載，以供媒體服務並封裝為頻內檔案格式，以及 HLS、虛線和 Smooth Streaming 的資訊清單。
建議您在每0.5 秒（500毫秒）或即時串流發生穩定性問題時，不常經常傳送計時中繼資料訊息。 如果您需要提供框架層級的中繼資料，每個訊息都可以匯總來自多個框架的中繼資料。 如果您要傳送多位元率串流，建議您也只提供單一位元速率的中繼資料，以減少頻寬並避免與影片/音訊處理產生干擾。 

**"OnUserDataEvent"** 的承載應為 [MPEGDASH] EventStream XML 格式訊息。 這可讓您輕鬆地傳入自訂定義的架構，以在 CMAF [MPEGCMAF] 透過 HLS 或破折號通訊協定傳遞的「emsg」承載中攜帶。 每個虛線事件資料流程訊息都包含一個 schemeIdUri，可做為 URN 訊息配置識別碼，並定義訊息的裝載。 適用于 https://aomedia.org/emsg/ID3 [scte-35] 的某些配置（例如 [ID3v2] 或**urn： scte： scte35：2013： bin** ）是由業界聯盟標準化，以達成互通性。 任何應用程式提供者都可以使用其控制的 URL （擁有的網域）來定義自己的自訂配置，並可在選擇時提供該 URL 的規格。 如果播放程式有定義之配置的處理常式，則這是唯一需要瞭解裝載和通訊協定的元件。

[MPEG 破折號] EventStream XML 承載的架構定義為（摘錄自破折號-IEC-23009-1-第3版）。 請注意，目前只支援每個 "EventStream" 一次「事件種類」。 只有在**EventStream**中提供多個事件時，才會處理第一個**事件**元素。

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


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>具有 ID3 架構識別碼和 base64 編碼資料裝載的 XML 事件資料流程範例。  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>含有自訂架構識別碼和 base64 編碼二進位資料的範例事件資料流程  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>具有自訂架構識別碼和自訂 JSON 的範例事件資料流程  
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

### <a name="built-in-supported-scheme-id-uris"></a>內建支援的配置識別碼 Uri
| 配置識別碼 URI                 | Description                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HTTPs： \/ /aomedia.org/emsg/ID3 | 描述如何將 [ID3v2] 中繼資料當做 CMAF 相容 [MPEGCMAF] 中的計時中繼資料來攜帶。 如需詳細資訊，請參閱[一般媒體應用程式格式的計時中繼資料（CMAF）](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>事件處理和資訊清單信號

收到有效的 **"onUserDataEvent"** 事件時，Azure 媒體服務會尋找符合 EventStreamType （定義于 [MPEGDASH]）中的有效 xml 承載、剖析 XML 裝載，並將它轉換成 [MPEGCMAF] 的 [的] 定順序片段 ' emsg ' 第1版，以即時封存並傳輸至媒體服務封裝工具。   封裝工具會偵測即時資料流中的 [emsg] 方塊，並：

- （a）「將它動態封裝」到 TS 區段，以傳遞至 HLS 用戶端，符合 HLS 計時中繼資料規格 [HLS-TMD]，或
- （b）傳遞給它，以透過 HLS 或破折號傳遞 CMAF 片段，或 
- （c）將它轉換成可透過 Smooth Streaming [MS-SSTR] 傳遞的稀疏軌信號。

除了適用于 HLS 的頻外 ' emsg ' 格式 CMAF 或 TS PE 封包之外，破折號（MPD）和 Smooth Streaming 的資訊清單會包含內建事件資料流程的參考（在 Smooth Streaming 中也稱為「稀疏資料流程追蹤」）。 

個別事件或其資料裝載不會直接在 HLS、虛線或流暢的資訊清單中輸出。 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>OnUserDataEvent 事件的其他資訊條件約束和預設值

- 如果未在 EventStream 元素中設定此時間刻度，則預設會使用 RTMP 1 kHz 的時間刻度
- OnUserDataEvent 訊息的傳遞限制為每個500毫秒的最大值一次。如果您更頻繁地傳送事件，可能會影響即時摘要的頻寬和穩定性

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>具有 "onAdCue" 的 2.1.2 RTMP ad 提示信號

Azure 媒體服務可以接聽並回應數個 [AMF0] 訊息類型，可用來對即時資料流中的各種即時同步處理中繼資料發出信號。  [Adobe Primetime] 規格會定義兩個稱為 "simple" 和 "SCTE-35" 模式的提示類型。 針對「簡單」模式，媒體服務支援名為 "onAdCue" 的單一 AMF 提示訊息，其使用符合下表所定義之「簡單模式」信號的裝載。  

下一節將說明 RTMP 「簡單」模式的承載，其可用來表示將會傳遞至 HLS、破折號和 Microsoft Smooth Streaming 的用戶端資訊清單的基本 "spliceOut" 廣告信號。 當客戶沒有以 SCTE 35 為基礎的複雜廣告信號部署或插入系統，而且使用基本內部部署編碼器透過 API 傳送提示訊息時，這非常有用。 一般來說，內部部署編碼器會支援 REST 型 API 來觸發此信號，這也會在影片中插入 IDR 框架並啟動新的 GOP，藉此「接合條件」影片串流。

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>使用 "onAdCue" 的 2.1.3 RTMP ad 提示信號-簡單模式

| 欄位名稱 | 欄位類型 | 必要項？ | 說明                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 類型       | String     | 必要  | 事件訊息。  應該是 "SpliceOut" 以指定簡單模式拼接。                                                                                                                                                                                                         |
| id         | String     | 必要  | 描述拼接或區段的唯一識別碼。 識別訊息的此執行個體                                                                                                                                                                                       |
| duration   | Number     | 必要  | 拼接的持續時間。 單位是小數秒。                                                                                                                                                                                                                           |
| elapsed    | Number     | 選擇性  | 當訊號重複以支援收聽時，此欄位應該是開始拼接後經過的呈現時間量。 單位是小數秒。 當使用簡單模式時，此值不應該超過拼接的原始時間長度。 |
| time       | Number     | 必要  | 應該是拼接的時間 (以呈現時間表示)。 單位是小數秒。                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>使用 Adobe RTMP simple 模式時的 MPEG 破折號資訊清單輸出範例

請參閱[使用 Adobe simple 模式的範例 3.3.2.1 MPEG 破折號. Mpd EventStream](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

請參閱[使用單一期間和 Adobe simple 模式的範例3.3.3.1 破折號資訊清單](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>使用 Adobe RTMP simple 模式時的範例 HLS 資訊清單輸出

請參閱[使用 Adobe 簡單模式和 EXT X 提示標記的範例 3.2.2 HLS 資訊清單](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>具有 "onAdCue"-SCTE-35 模式的 2.1.4 RTMP ad 提示信號

當您使用的「更先進的廣播生產」工作流程需要將完整的 SCTE-35 承載訊息傳遞至 HLS 或破折號資訊清單時，最好使用 [Adobe-Primetime] 規格的「SCTE-35 模式」。  此模式支援直接傳送到內部部署即時編碼器的頻內 SCTE-35 信號，然後使用 [Adobe-Primetime] 規格中指定的「SCTE-35 模式」，將信號編碼成 RTMP 串流。 

通常 SCTE-35 訊息只能出現在內部部署編碼器的 MPEG-2 傳輸串流（TS）輸入中。 請洽詢您的編碼器製造商，以取得如何設定包含 SCTE-35 之傳輸資料流程內嵌的詳細資料，並讓它能夠在 Adobe SCTE-35 模式下傳遞至 RTMP。

在此案例中，必須使用 **"onAdCue"** [AMF0] 訊息類型，從內部部署編碼器傳送下列承載。

| 欄位名稱 | 欄位類型 | 必要項？ | 說明                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 提示        | String     | 必要  | 事件訊息。  對於 [SCTE-35] 訊息，這必須是 base64 編碼的 [RFC4648] 二進位 splice_info_section （），才能將訊息傳送至 HLS、平滑和虛線用戶端。                                                                                                                                                                                                                               |
| 類型       | String     | 必要  | 可識別訊息配置的 URN 或 URL。 對於 [SCTE-35] 訊息，這**應該**是 **"scte35"** ，以便將訊息傳送至 HLS、平滑和虛線用戶端，以符合 [Adobe-Primetime]。 （選擇性） URN "urn： scte： scte35：2013： bin" 也可以用來表示 [SCTE-35] 訊息。                                                                                                        |
| id         | String     | 必要  | 描述拼接或區段的唯一識別碼。 識別訊息的此執行個體。  具有同等語意的訊息應該有相同的值。                                                                                                                                                                                                                                                       |
| duration   | Number     | 必要  | 事件或廣告拼接區段的期間 (若已知)。 如果未知，此值**應該**是0。                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Number     | 選擇性  | 當 [SCTE-35] 廣告訊號重複以收聽時，此欄位應該是開始拼接後經過的呈現時間量。 單位是小數秒。 在 [SCTE-35] 模式中，此值可能會超過原本指定的拼接或區段持續時間。                                                                                                                   |
| time       | Number     | 必要  | 事件或拼接的呈現時間。  簡報時間和持續時間**應該**與類型1或2的串流存取點（SAP）一致，如 [ISO-14496-12] 附錄 I 中所定義。針對 HLS 輸出，時間和持續時間**應該**與區段界限一致。 呈現時間與相同事件串流中不同事件訊息的期間不能重疊。 單位是小數秒。 |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>範例 MPEG 破折號. mpd 資訊清單與 SCTE-35 模式
請參閱[使用 SCTE 的3.3.3.2 範例破折號資訊清單-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>範例 HLS 資訊清單. m3u8 with SCTE-35 模式信號
請參閱[3.2.1.1 範例 HLS 資訊清單中的 SCTE-35 一節](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>針對 Elemental Live 以 "onCuePoint" 2.1.5 RTMP 廣告信號

Elemental Live 內部部署編碼器支援 RTMP 信號中的廣告標記。 Azure 媒體服務目前僅支援 RTMP 的 "onCuePoint" Ad 標記類型。  您可以在 Elemental 媒體即時編碼器設定或 API 的 Adobe RTMP 群組設定中啟用此功能，方法是將 "**ad_markers**" 設定為 "onCuePoint"。  如需詳細資訊，請參閱 Elemental Live 檔。 在 RTMP 群組中啟用這項功能，會將 SCTE-35 信號傳遞給要由 Azure 媒體服務處理的 Adobe RTMP 輸出。

"OnCuePoint" 訊息類型定義于 [Adobe-Flash-AS] 中，並在從 Elemental 即時 RTMP 輸出傳送時具有下列裝載結構。


| 屬性   | 描述                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NAME       | Elemental Live 的名稱應該是 '**scte35**'。                                                                                                                                                                              |
| time       | 在時間軸期間，影片檔案中出現提示點的時間（以秒為單位）                                                                                                                                           |
| 類型       | 提示點的類型應該設定為「**事件**」。                                                                                                                                                                             |
| 參數 | 名稱/值組字串的關聯陣列，其中包含來自 SCTE-35 訊息的資訊，包括識別碼和持續時間。 這些值會由 Azure 媒體服務剖析，並包含在資訊清單裝飾標記中。 |


使用此 ad 標記模式時，HLS 資訊清單輸出類似 Adobe "Simple" 模式。


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>範例 MPEG 破折號 MPD、單句點、Adobe Simple 模式信號

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

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>範例 HLS 播放清單、Adobe Simple 模式信號使用 EXT-提示標記（已截斷的 "..."為了簡潔起見）

下列範例示範如何使用 Adobe "simple" 模式信號和舊版 [Adobe-Primetime] EXT X 提示標記，從 RTMP 內嵌資料流程的媒體服務動態封裝輸出。  

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

如需即時資料流內嵌的需求，請參閱 [MS-MS-SSTR-內嵌]。 以下各節提供有關計時呈現中繼資料內嵌的詳細資料。  計時簡報中繼資料會內嵌為稀疏軌，這會在即時伺服器資訊清單方塊（請參閱 MS-SSTR）和電影方塊（' moov '）中定義。  

每個稀疏片段都是由一個影片片段方塊（' moof '）和 Media 資料箱（' mdat '）所組成，其中 ' mdat ' 方塊是二進位訊息。

為了達到框架正確插入廣告的效果，編碼器必須在需要插入提示的呈現時間分割片段。  必須建立新的片段，其開頭為新建立的 IDR 框架，或類型1或2的串流存取點（SAP），如 [ISO-14496-12] 附錄 I 中所定義。這可讓 Azure 媒體封裝程式正確產生 HLS 資訊清單和虛線多段式資訊清單，其中新期間是以框架精確的接合條件式呈現時間開始。

### <a name="221-live-server-manifest-box"></a>2.2.1 即時伺服器資訊清單方塊

稀疏播放軌**必須**在具有專案的即時伺服器資訊清單方塊中宣告 **\<textstream\>** ，而且**必須**設定下列屬性：

| **屬性名稱** | **欄位類型** | **必要？** | **描述**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Number         | 必要      | **必須**為 "0"，表示具有未知、變數位元速率的追蹤。                                                                                                                                                          |
| parentTrackName    | String         | 必要      | **必須**是父代播放軌的名稱，其中的稀疏追蹤時間代碼為對齊的時間。 父系播放軌不能是疏鬆播放軌。                                                                             |
| manifestOutput     | Boolean        | 必要      | **必須**是 "true"，表示稀疏播放軌將內嵌在平滑用戶端資訊清單中。                                                                                                                        |
| Subtype            | String         | 必要      | **必須**是四個字元代碼 "DATA"。                                                                                                                                                                                  |
| 配置             | String         | 必要      | **必須**是可識別訊息配置的 URN 或 URL。 對於 [SCTE-35] 訊息，這**必須**是 "urn： SCTE： scte35：2013： bin"，才能將訊息傳送至 HLS、平滑和虛線用戶端，以符合 [SCTE-35]。 |
| trackName          | String         | 必要      | **必須**是稀疏播放軌的名稱。Trackname 前可以用來區分多個具有相同配置的事件資料流程。 每個唯一的事件資料流程都**必須**有唯一的追蹤名稱。                                |
| timescale          | Number         | 選擇性      | **必須**是父軌的時間刻度。                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 影片方塊

電影方塊（' moov '）會遵循即時伺服器資訊清單方塊，做為稀疏軌的資料流程標頭的一部分。

' Moov ' 方塊**應該**包含**TrackHeaderBox （' tkhd '）** 方塊，如 [ISO-14496-12] 中所定義，並具有下列條件約束：

| **功能變數名稱** | **欄位類型**          | **必要？** | **描述**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 64 位元不帶正負號的整數 | 必要      | **應**為0，因為追蹤方塊的樣本數為零，且追蹤方塊中樣本的總持續時間為0。 |

---

' Moov ' 方塊**應該**包含**HandlerBox （' hdlr '）** ，如 [ISO-14496-12] 中所定義，並具有下列條件約束：

| **功能變數名稱** | **欄位類型**          | **必要？** | **描述**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32 位元不帶正負號的整數 | 必要      | **應該**是 ' meta '。 |

---

' Stsd ' 方塊**應該**包含具有編碼名稱的 MetaDataSampleEntry 方塊，如 [ISO-14496-12] 中所定義。  例如，對於 SCTE-35 訊息，編碼名稱**應該**是 ' SCTE '。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 影片片段方塊與媒體資料方塊

稀疏軌片段包含電影片段方塊（' moof '）和 Media 資料箱（' mdat '）。

> [!NOTE]
> 為了達到框架正確插入廣告的效果，編碼器必須在需要插入提示的呈現時間分割片段。  必須建立新的片段，其開頭為新建立的 IDR 框架，或類型1或2的串流存取點（SAP），如 [ISO-14496-12] 附錄 I 中所定義
> 

MovieFragmentBox （' moof '）方塊**必須**包含**TrackFragmentExtendedHeaderBox （' uuid '）** 方塊，如 [MS-ms-sstr] 中所定義，並具有下欄欄位：

| **功能變數名稱**         | **欄位類型**          | **必要？** | **描述**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64 位元不帶正負號的整數 | 必要      | **必須**是事件的抵達時間。 此值與父代播放軌的訊息一致。           |
| fragment_duration      | 64 位元不帶正負號的整數 | 必要      | **必須**是事件的持續時間。 持續時間可以是零，指出持續時間未知。 |

---


MediaDataBox （' mdat '）方塊的格式**必須**如下：

| **功能變數名稱**          | **欄位類型**                   | **必要？** | **描述**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32 位元不帶正負號的整數 (uimsbf) | 必要      | 決定 [mdat] 方塊內容的格式。 將會忽略無法辨識的版本。 目前唯一支援的版本是 1。                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 位元不帶正負號的整數 (uimsbf) | 必要      | 識別訊息的此執行個體。 具有對等語意的訊息應該有相同的值；亦即，處理具有相同 id 的任一事件訊息就已足夠。                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 位元不帶正負號的整數 (uimsbf) | 必要      | 在 TrackFragmentExtendedHeaderBox 中指定的 fragment_absolute_time 和 presentation_time_delta 的總和**必須**是事件的呈現時間。 簡報時間和持續時間**應該**與類型1或2的串流存取點（SAP）一致，如 [ISO-14496-12] 附錄 I 中所定義。針對 HLS 輸出，時間和持續時間**應該**與區段界限一致。 相同事件資料流程內不同事件訊息的呈現時間和持續時間**不得**重迭。 |
| message                 | 位元組陣列                       | 必要      | 事件訊息。 對於 [SCTE-35] 訊息，訊息是二進位 splice_info_section （）。 對於 [SCTE-35] 訊息，這**必須**是 splice_info_section （），才能將訊息傳送至 HLS、平滑和虛線用戶端，以符合 [SCTE-35]。 對於 [SCTE-35] 訊息，二進位 splice_info_section （）是 ' mdat ' 方塊的裝載，而且**不**是以 base64 編碼。                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 取消與更新

您可以透過傳送具有相同呈現時間與 ID 的多個訊息來取消或更新訊息。  呈現時間與 ID uniquely 可唯一識別事件。 針對符合預滾動限制式之特定呈現時間所接收的最後一個訊息是針對其採取動作的訊息。 更新的訊息會取代先前接收的任何訊息。  預滾動限制式是四秒。 系統將會針對在呈現時間前最後四秒接收的訊息採取動作。 


## <a name="3-timed-metadata-delivery"></a>3 計時中繼資料傳遞

事件串流資料對「媒體服務」而言是不透明的。 「媒體服務」只會在內嵌端點與用戶端端點之間傳送三小部分的資訊。 下列屬性會傳遞至用戶端，符合 [SCTE-35] 和/或用戶端的串流通訊協定：

1.  配置 – 可識別訊息配置的 URN 或 URL。
2.  呈現時間 – 媒體時間軸上之事件的呈現時間。
3.  持續時間 – 事件的持續時間。
4.  ID – 選擇性的事件唯一識別碼。
5.  訊息 – 事件資料。

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming 資訊清單  

如需如何格式化稀疏訊息追蹤的詳細資訊，請參閱 sparse 追蹤處理 [MS-SSTR]。對於 [SCTE35] 訊息，Smooth Streaming 會將 base64 編碼的 splice_info_section （）輸出到稀疏片段中。
StreamIndex**必須**具有 "DATA" 的子類型，而且 CustomAttributes**必須**包含 Name = "Schema" 和 Value = "urn： scte： scte35：2013： bin" 的屬性。

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>顯示 base64 編碼 [SCTE35] splice_info_section （）的平滑用戶端資訊清單範例
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

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS 資訊清單裝飾

Azure 媒體服務支援下列 HLS 資訊清單標記，以在即時或隨選事件期間通知廣告可用資訊。 

- 如 Apple HLS 中所定義的 EXT-X DATERANGE [RFC8216]
- 以 [Adobe-Primetime] 定義的 EXT X 提示-此模式會被視為「舊版」。 客戶應該盡可能採用 EXT X DATERANGE 標記。

每個標記的資料輸出會根據使用的內嵌信號模式而有所不同。 例如，使用 Adobe Simple 模式的 RTMP 內嵌並不包含完整的 SCTE-35 base64 編碼承載。

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 具有 EXT X DATERANGE 的 Apple HLS （建議）

Apple HTTP 即時串流 [RFC8216] 規格允許發出 [SCTE-35] 訊息的信號。 這些訊息會插入區段播放清單中，每個 [RFC8216] 區段的 DATERANGE 標記，標題為「將 SCTE-35 對應到 EXT-X-DATERANGE」。  用戶端應用層可以剖析 M3U 播放清單並處理 M3U 標記，或透過 Apple player 架構接收事件。  

Azure 媒體服務（第3版 API）中的**建議**方法是遵循 [RFC8216]，並在資訊清單中使用 [SCTE35] ad 可用裝飾的 EXT-X_DATERANGE 標記。

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 範例 HLS 資訊清單。 m3u8 顯示 SCTE-35 的 EXT X DATERANGE 信號

下列範例會從媒體服務動態封裝工具 HLS 資訊清單輸出，顯示如何使用 [RFC8216] 中的 EXT-DATERANGE 標記，以通知串流中的 SCTE-35 事件。 此外，此資料流程包含 [Adobe-Primetime] 的 "legacy" EXT X 提示標記。

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


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS with Adobe Primetime EXT X 提示（舊版）

Azure 媒體服務（版本2和 3 API）中提供了「舊版」的實作為，其使用 [Adobe-Primetime] "SCTE-35 模式" 中定義的 EXT X 提示標記。 在此模式中，Azure 媒體服務會在 EXT X 提示標記中內嵌 base64 編碼的 [SCTE-35] splice_info_section （）。  

「舊版」的 EXT X 提示標記定義如下，而且也可以在 [Adobe-Primetime] 規格中加以參考。 這應該只在需要時用於舊版 SCTE35 信號，否則建議的標記會在 [RFC8216] 中定義為 EXT-DATERANGE。 

| **屬性名稱** | **型別**                      | **必要？**                             | **描述**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 提示                | 加上括號的字串                 | 必要                                  | 編碼為 base64 編碼字串的訊息，如 [RFC4648] 中所述。 對於 [SCTE-35] 訊息，這是 base64 編碼的 splice_info_section （）。                                                                                                                                      |
| TYPE               | 加上括號的字串                 | 必要                                  | 可識別訊息配置的 URN 或 URL。 若為 [SCTE-35] 訊息，類型會採用特殊值 "scte35"。                                                                                                                                                                          |
| 識別碼                 | 加上括號的字串                 | 必要                                  | 事件的唯一識別碼。 如果內嵌訊息時未指定 ID，「Azure 媒體服務」將會產生一個唯一 ID。                                                                                                                                              |
| 持續時間           | 十進位浮點數數字 | 必要                                  | 事件的持續時間。 如果未知，此值**應該**是0。 單位是小數秒。                                                                                                                                                                                           |
| ELAPSED            | 十進位浮點數數字 | 選擇性，但對滑動視窗而言是必要的 | 當信號重複以支援滑動呈現視窗時，此欄位**必須**是自活動開始以來已經過的呈現時間量。 單位是小數秒。 此值可能會超過原本指定的拼接或區段持續時間。 |
| TIME               | 十進位浮點數數字 | 必要                                  | 事件的呈現時間。 單位是小數秒。                                                                                                                                                                                                                        |


HLS 播放程式應用程式層將會使用 TYPE 來識別訊息格式、將訊息解碼、套用必要時間轉換，以及處理事件。  根據事件時間戳記，事件時間在父代播放軌的區段播放清單中是同步的。  它們是插入在最接近的區段 (#EXTINF tag) 前面。

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>使用 "Legacy" Adobe Primetime EXT-提示的 3.2.3 HLS. m3u8 資訊清單範例

下列範例顯示使用 Adobe Primetime EXT--提示標記的 HLS 資訊清單裝飾。  "提示" 參數只包含 TYPE 和 Duration 屬性，這表示這是使用 Adobe 「簡單」模式信號的 RTMP 來源。  如果這是 SCTE-35 模式信號，標記會包含 base64 編碼的二進位 SCTE-35 內容，如[3.2.1.1 範例](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)中所示。

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

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>"Legacy" Adobe Primetime EXT-X 提示的 3.2.4 HLS 訊息處理

事件會在每個影片和音軌的區段播放清單中收到信號。EXT X 提示標籤的位置**必須**一律緊接在第一個 HLS 區段（用於 [拼接] 或 [區段開始]）之前，或緊接在最後一個 HLS 區段後面（用於 [拼接] 或 [區段結束]）到其 TIME 和 DURATION 屬性所參考的（如 [Adobe-Primetime] 所要求）。

當滑動呈現視窗啟用時，EXT X 提示標籤的重複頻率**必須**足以讓拼接或區段一律在區段播放清單中完整描述，而已耗用的屬性則**必須**用來表示接合或區段在使用中的時間量（如 [Adobe-Primetime] 所要求）。

當滑動呈現視窗啟用時，若它們所指的媒體時間已從滑動呈現視窗滾出，標籤會從區段播放清單移除。

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 虛線資訊清單裝飾（MPD）

[MPEGDASH] 提供三種通知事件的方式：

1.  在 MPD EventStream 中發出信號的事件
2.  使用事件訊息方塊（' emsg '）在範圍內發出的事件
3.  1 與 2 兩者的組合

在 MPD EventStream 中發出信號的事件適用于 VOD 串流，因為用戶端可以在下載 MPD 時立即存取所有事件。 它也適用于 SSAI 信號，其中下游 SSAI 廠商需要從多個期間的 MPD 資訊清單剖析信號，並動態插入廣告內容。  頻內（' emsg '）解決方案適用于即時串流，其中用戶端不需要再次下載 MPD，或在用戶端與來源之間不會發生 SSAI 資訊清單操作。 

Azure 媒體服務虛線的預設行為是使用事件訊息方塊（' emsg '），在 MPD EventStream 和頻內發出信號。

透過 [RTMP] 或 [MS-MS-SSTR] 內嵌的提示訊息會對應到虛線事件，並使用內建的 ' emsg ' 方塊和/或 in-MPD Eventstream。 

[頻外 SCTE-35 信號] 會遵循 [SCTE-214-3] 中定義的定義和需求，以及 [虛線-IF-IOP] 區段13.12.2 （' SCTE35 Events '）。 

若為頻外 [SCTE-35]，事件訊息方塊（' emsg '）會使用 schemeId = "urn： SCTE： scte35：2013： bin"。 針對 MPD 資訊清單裝飾，EventStream schemeId 會使用 "urn： scte： scte35：2014： xml + bin"。  此格式是事件的 XML 表示，其中包含抵達內嵌之完整 SCTE-35 訊息的二進位 base64 編碼輸出。 

在 [SCTE-214-1] sec 6.7.4 （MPD）和 [SCTE-214-3] sec 7.3.2 （SCTE 35 提示訊息的托架）中，有 [SCTE-35] 的提示訊息的標準化參考定義。

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG 破折號（MPD） EventStream 信號

事件的資訊清單（MPD）裝飾會使用 EventStream 專案在 MPD 中發出信號，這會出現在 Period 元素中。 使用的 schemeId 是 "urn： scte： scte35：2014： xml + bin"。

> [!NOTE]
> 為求簡潔，[SCTE-35] 允許在信號. Binary 元素（而不是 SpliceInfoSection 元素）中使用 base64 編碼的區段，做為完全剖析的提示訊息的替代方法。
> Azure 媒體服務使用此 ' xml + bin ' 方法在 MPD 資訊清單中發出信號。
> 這也是建議用於 [虛線--------------IOP] 的方法-如 [IOP] 指導方針，請參閱虛線的「 [Ad 插入事件資料流程](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)」一節
> 

EventStream 元素具有下列屬性：

| **屬性名稱** | **型別**                | **必要？** | **描述**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | 字串                  | 必要      | 識別訊息的配置。 配置是設定為即時伺服器資訊清單方塊中 Scheme 屬性的值。 值**應該**是可識別訊息配置的 URN 或 URL;支援的輸出 schemeId 應該是 "urn： scte： scte35：2014： xml + bin" per [SCTE-214-1] sec 6.7.4 （MPD），因為這次服務僅支援 "xml + bin"，以便在 MPD 中進行簡潔。 |
| value              | 字串                  | 選用      | 由配置擁有者用來自訂訊息語意的額外字串值。 為了區分多個具有相同配置的事件資料流程，此值**必須**設定為事件資料流程的名稱（[ms-sstr-內嵌] 的 trackname 前或 [RTMP] 內嵌的 AMF 訊息名稱）。                                                                         |
| 時幅          | 32 位元不帶正負號的整數 | 必要      | 時間刻度，以每秒的刻度數為單位。                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 MPEG 虛線的範例事件資料流程

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 範例 MPEG 破折號。 mpd 使用 Adobe simple 模式 RTMP 串流的資訊清單

下列範例示範如何使用 Adobe 「簡單」模式信號，從適用于 RTMP 串流的媒體服務動態封裝程式中 EventStream 摘錄。

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

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 範例 MPEG 破折號。使用 Adobe SCTE-35 模式的 RTMP 串流的 mpd 資訊清單信號

下列範例顯示使用 Adobe SCTE-35 模式信號的 RTMP 串流媒體服務動態封裝程式的摘錄 EventStream。

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
> 請注意，presentationTime 是轉譯為相對於期間開始時間的 [SCTE-35] 事件的呈現時間，而不是訊息的抵達時間。
> [MPEGDASH] 將定義 Event@presentationTime 為 "指定事件的呈現時間（相對於期間開始）。
> 呈現時間（以秒為單位）的值是這個屬性值與屬性值的除法 EventStream@timescale 。
> 如果不存在，表示時間的值為0。

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 範例 MPEG 破折號資訊清單（MPD）搭配單句點 EventStream、使用 Adobe simple 模式信號

下列範例會使用 Adobe "simple" 模式 ad 信號方法，顯示來源 RTMP 資料流程的媒體服務動態封裝程式的輸出。 輸出是一個使用 schemeId Uri 設定為 "urn： com： adobe： DPI： simple： 2015" 且 value 屬性設定為 "simplesignal" 的 EventStream 的單一期間資訊清單。
每個簡單信號都是在事件元素中提供 @presentationTime ， @duration 而且會 @id 根據傳入的簡單信號來填入、和屬性。

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

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 範例 MPEG 破折號資訊清單（MPD）搭配多段 EventStream，使用 Adobe SCTE35 模式信號

下列範例顯示使用 Adobe SCTE35 模式信號的來源 RTMP 資料流程之媒體服務動態封裝程式的輸出。
在此情況下，輸出資訊清單是多週期的 mpd，具有 EventStream 元素，且 @schemeIdUri 屬性設定為 "urn： scte： scte35：2014： xml + bin"，而 @value 屬性設定為 "scte35"。 EventStream 中的每個 Event 元素都包含完整的 base64 編碼二進位 SCTE35 信號 

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
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG 破折號-頻外事件訊息方塊信號

頻內事件串流要求 MPD 在「自適性集合」層必須有 InbandEventStream 元素。  這個元素具有強制 schemeIdUri 屬性和選擇性的 [時間刻度] 屬性，這也會出現在事件訊息方塊中（' emsg '）。  MPD 中未定義配置識別碼的事件訊息方塊不**應**存在。

對於頻外 [SCTE-35]，信號**必須**使用 schemeId = "urn： SCTE： scte35：2013： bin"。
在 [SCTE-214-3] sec 7.3.2 （SCTE 35 提示訊息的 [SCTE]）中，會定義 [-35] 的「內建訊息」的標準化定義。

下列詳細資料會概述用戶端在與 [SCTE-214-3] 相容時，應該會在 ' emsg ' 中預期的特定值：

| **功能變數名稱**          | **欄位類型**          | **必要？** | **描述**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | 字串                  | 必要      | 識別訊息的配置。 配置是設定為即時伺服器資訊清單方塊中 Scheme 屬性的值。 此值**必須**是可識別訊息配置的 URN。 對於 [SCTE-35] 訊息，這**必須**是 "urn： SCTE： scte35：2013： bin"，符合 [SCTE-214-3]          |
| 值                   | 字串                  | 必要      | 由配置擁有者用來自訂訊息語意的額外字串值。 若要區分具有相同配置的多個事件串流，值必須設定為事件串流的名稱 (適用於 Smooth 內嵌的 trackName，或適用於 RTMP 內嵌的 AMF 訊息名稱)。 |
| 時幅               | 32 位元不帶正負號的整數 | 必要      | [Emsg] 方塊內 [時間] 和 [持續時間] 欄位的時間長度（以每秒的刻度為單位）。                                                                                                                                                                                                            |
| presentation_time_delta | 32 位元不帶正負號的整數 | 必要      | 事件呈現時間與此區段中最早的呈現時間之間的媒體呈現時間差異。 簡報時間和持續時間**應該**與類型1或2的串流存取點（SAP）一致，如 [ISO-14496-12] 附錄 I 中所定義。                                  |
| event_duration          | 32 位元不帶正負號的整數 | 必要      | 事件的持續時間，或指定 0xFFFFFFFF 以指出未知的持續時間。                                                                                                                                                                                                                              |
| Id                      | 32 位元不帶正負號的整數 | 必要      | 識別訊息的此執行個體。 具有同等語意的訊息應該有相同的值。 如果內嵌訊息時未指定 ID，「Azure 媒體服務」將會產生一個唯一 ID。                                                                                        |
| Message_data            | 位元組陣列              | 必要      | 事件訊息。 針對 [SCTE-35] 訊息，訊息資料是二進位 splice_info_section （），符合 [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Adobe Simple 模式的範例 InBandEvenStream 實體
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 破折號訊息處理

在 [emsg] 方塊內，會針對影片和音訊播放軌以頻外方式發出事件。  針對 presentation_time_delta 為 15 秒或低於 15 秒的所有區段要求，都會發生訊號處理。 

當滑動呈現視窗啟用時，若事件訊息的時間與持續時間總和小於資訊清單中媒體資料的時間，事件訊息會從 MPD 移除。  換句話說，當其所指之媒體時間已滾出滑動呈現視窗之外，就會從資訊清單移除事件訊息。

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 適用于編碼器廠商的內嵌實施指導方針

下列指導方針是可能會影響編碼器廠商的此規格執行的常見問題。  下列指導方針已根據真實世界合作夥伴的意見反應收集，讓您更輕鬆地為其他人執行此規格。 

[SCTE-35] 訊息是以二進位格式內嵌，使用 " **urn： SCTE： scte35：2013： bin"** （適用于 [MS-ms-sstr-內嵌]）和類型 **"scte35"** （適用于 [RTMP] 內嵌）。 為促進 [SCTE-35] 計時的轉換 (以 MPEG-2 傳輸串流呈現時間戳記 (PTS) 為基礎)，PTS (pts_time + pts_adjustment of the splice_time()) 與媒體時間軸之間的對應是由事件呈現時間 (適用於 Smooth 內嵌的 fragment_absolute_time 欄位，或適用於 RTMP 內嵌的時間欄位) 所提供。 對應是必要的，因為 33 位元 PTS 值大約每隔 26.5 小時就會滾動一次。

Smooth Streaming 內嵌 [MS-SSTR-內嵌] 需要媒體資料箱（' mdat '）**必須**包含 [SCTE-35] 中定義的**splice_info_section （）** 。 

對於 RTMP 內嵌，AMF 訊息的 [提示] 屬性會設定為 [SCTE-35] 中定義的 base64 編碼**splice_info_section （）** 。  

當訊息具有上述格式時，會將其傳送至 HLS、平滑和虛線用戶端，如上面所定義。  

當您使用 Azure 媒體服務平臺測試您的執行時，請先使用「傳遞」 LiveEvent 開始測試，再移至 [編碼 LiveEvent] 上的測試。

---

## <a name="change-history"></a>變更歷程記錄

| 日期     | 變更                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | 已針對 SCTE35 支援修改 RTMP 內嵌，已為 Elemental Live 新增 RTMP "onCuePoint"                                  |
| 08/22/19 | 已更新以將自訂中繼資料的 OnUserDataEvent 新增至 RTMP                                                          |
| 1/08/20  | 已修正 RTMP Simple 和 RTMP SCTE35 模式的錯誤。 已從 "onCuePoint" 變更為 "onAdCue"。 已更新簡單模式資料表。 |

## <a name="next-steps"></a>後續步驟
檢視媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
