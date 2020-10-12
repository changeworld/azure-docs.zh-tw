---
title: Bing 當地企業搜尋 API v7 參考
titleSuffix: Azure Cognitive Services
description: 本文提供回應物件的相關技術詳細資料，以及影響搜尋結果的查詢參數和標頭。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: d5de1cc606f97655427c0c86aea0c5c722e1bab8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84171458"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Bing 當地商家搜尋 API v7 參考

當地商家搜尋 API 向 Bing 傳送搜尋查詢以取得包含餐廳、旅館或其他當地商家的結果。 針對地點，查詢可指定本地商家名稱或類別 (例如，我附近的餐廳)。 實體結果包含人員、地點或事項。 此內容中的位置是商務實體、州、國家/地區等。  

本節將提供關於影響搜尋結果的回應物件，以及查詢參數和標頭的技術詳細資料。 如需示範如何提出要求的範例，請參閱[當地商家搜尋 C# 快速入門](quickstarts/local-quickstart.md)或[當地商家搜尋 Java 快速入門](quickstarts/local-search-java-quickstart.md)。 
  
關於要求所應包含之標頭的相關資訊，請參閱[標頭](#headers)。  
  
如需要求所應包含的查詢參數相關資訊，請參閱[查詢參數](#query-parameters)。  
  
如需回應所包含的 JSON 物件相關資訊，請參閱[回應物件](#response-objects)。

如需使用允許和顯示結果方式的相關資訊，請參閱[使用和顯示需求](use-display-requirements.md)。


  
## <a name="endpoint"></a>端點  
若要要求當地商家結果，請傳送 GET 要求： 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
要求必須使用 HTTPS 通訊協定。  
  
> [!NOTE]
> URL 長度上限是 2,048 個字元。 若要確保您的 URL 長度不超過限制，查詢參數的最大長度應小於 1,500 個字元。 如果 URL 超過 2,048 個字元，則伺服器會傳回「404 找不到」。  
  
  
## <a name="headers"></a>headers  
以下是要求和回應可能包含的標頭。  
  
|標頭|說明|  
|------------|-----------------|  
|接受|選擇性要求標頭。<br /><br /> 預設媒體類型為 application/json。 若要指定回應必須使用 [JSON-LD](https://json-ld.org/)，請將 Accept 標頭設定為 application/ld+json。|  
|<a name="acceptlanguage"></a>Accept-Language|選擇性要求標頭。<br /><br /> 要用於使用者介面字串語言的逗號分隔清單。 清單採用喜好設定的遞減順序。 如需詳細資訊 (包括預期的格式)，請參閱 [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 此標頭和 [setLang](#setlang) 查詢參數彼此互斥 &mdash; 請勿同時指定。<br /><br /> 若您設定此標頭，則您也必須指定 cc 查詢參數。 若要決定要傳回結果的市場，Bing 會使用它從清單中找到的第一個支援的語言，然後將其與 `cc` 參數值結合。 如果清單中未包含支援的語言，Bing 會就近尋找支援要求的語言和市場，或將彙總或預設的市場用於結果。 若要判斷 Bing 所使用的市場，請參閱 BingAPIs-Market 標頭。<br /><br /> 只有在指定了多種語言時，才需要使用此標頭和 `cc` 查詢參數。 否則，請使用 [mkt](#mkt) 和 [setLang](#setlang) 查詢參數。<br /><br /> 使用者介面字串是在使用者介面中作為標籤的字串。 JSON 回應物件中有幾個使用者介面字串。 回應物件中 Bing.com 屬性的任何連結都會套用指定的語言。|  
|<a name="market"></a>BingAPIs-Market|回應標頭。<br /><br /> 要求所使用的市場。 格式為 \<languageCode\> - \<countryCode\> 。 例如：en-US。|  
|<a name="traceid"></a>BingAPIs-TraceId|回應標頭。<br /><br /> 包含要求詳細資料記錄項目的識別碼。 發生錯誤時，會擷取這個識別碼。 如果您無法判定並解決問題，請將此識別碼與其他資訊一併提供給支援小組。|  
|<a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key|必要的要求標頭。<br /><br /> 您在[認知服務](https://www.microsoft.com/cognitive-services/)中註冊此服務時收到的訂用帳戶金鑰。|  
|<a name="pragma"></a>Pragma|選擇性的要求標頭<br /><br /> 根據預設，Bing 會傳回快取的內容 (如果有的話)。 若要防止 Bing 傳回快取的內容，請將 Pragma 標頭設定為 no-cache (例如，Pragma: no-cache)。
|<a name="useragent"></a>User-Agent|選擇性要求標頭。<br /><br /> 提出要求的使用者代理程式。 Bing 會利用使用者代理程式為行動使用者提供最佳體驗。 雖然是選擇性的，但我們仍建議您一律指定此標頭。<br /><br /> 「使用者代理程式」應為任何常用瀏覽器所傳送的相同字串。 如需使用者代理程式的相關資訊，請參閱 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 以下是使用者代理程式字串的範例。<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid"></a>X-MSEdge-ClientID|選擇性要求和回應標頭。<br /><br /> Bing 使用此標頭在 Bing API 所有呼叫之間為使用者提供一致的行為。 Bing 經常推出新功能和改善項目，且會以用戶端識別碼作為不同測試版指派流量的金鑰。 如果在多個要求中，未讓使用者使用相同的用戶端識別碼，Bing 可能會將使用者指派至多個衝突的測試版。 若指派給多個衝突的測試版，可能會導致使用者體驗不一致。 例如若第二個要求與第一個要求指派的測試版不同，可能會產生意料外的體驗。 此外，Bing 可以使用用戶端識別碼將 Web 結果調整為該用戶端識別碼的搜尋記錄，為使用者提供更豐富的體驗。<br /><br /> Bing 也會使用此標頭分析用戶端識別碼產生的活動，協助改善結果的順位。 相關性改進功能有助於 Bing API 提供更高品質的結果，進而為 API 取用者提供更高的點擊率。<br /><br /> **重要事項：** 雖然是選擇性的，但您仍應將此標頭視為必要項目。 為相同的使用者和裝置組合跨多個要求保存用戶端識別碼，可讓 1) API 取用者獲得一致的使用者體驗，以及 2) 透過 Bing API 更理想的結果品質獲得較高的點擊率。<br /><br /> 以下是適用於此標頭的基本使用規則。<br /><ul><li>在裝置上使用您應用程式的每個使用者必須具有 Bing 產生的唯一用戶端識別碼。<br /><br/>如果您未在要求中包含此標頭，Bing 會產生一個識別碼，並於 X-MSEdge-ClientID 回應標頭傳回該識別碼。 不應在要求中包含此標頭的唯一時機是使用者初次在該裝置上使用您的應用程式時。<br /><br/></li><li>您的應用程式每次為該裝置上的該名使用者提出 Bing API 請求時，均需使用此用戶端識別碼。<br /><br/></li><li>**注意：** 您必須確保此用戶端識別碼不能連結到任何可辨識身分的使用者帳戶資訊。</li><br/><li>保存用戶端識別碼。 若要在瀏覽器應用程式中保存識別碼，請使用永續性的 HTTP Cookie，以確保在所有工作階段均使用該識別碼。 請勿使用工作階段 Cookie。 若為其他應用程式 (例如行動裝置應用程式)，請使用裝置本身的永久儲存裝置保存識別碼。<br /><br/>使用者下次在該裝置上使用您的應用程式時，會取得您保存的用戶端識別碼。</li></ul><br /> **注意：** Bing 回應不一定會包含此標頭。 如果回應包含此標頭，請擷取用戶端識別碼，並將其用於該裝置上使用者的所有後續 Bing 要求。<br /><br /> **注意：** 如果您包含 X-MSEdge-ClientID，則不得在要求中加入 Cookie。|  
|<a name="clientip"></a>X-MSEdge-ClientIP|選擇性要求標頭。<br /><br /> 用戶端裝置的 IPv4 或 IPv6 位址。 此 IP 位址可用來探索使用者的位置。 Bing 會使用位置資訊來判斷安全搜尋行為。<br /><br /> **注意：** 雖然是選擇性的，但我們仍建議您一律指定此標頭和 X-Search-Location 標頭。<br /><br /> 請勿混淆位址 (例如，藉由將最後一個八位元變更為 0)。 混淆位址會導致位置不在裝置的實際位置附近，這可能會造成 Bing 產生錯誤結果。|  
|<a name="location"></a>X-Search-Location|選擇性要求標頭。<br /><br /> 以分號分隔的索引鍵/值組清單，用以說明用戶端的地理位置。 Bing 會使用位置資訊來判斷安全的搜尋行為，以及傳回相關的區域內容。 將索引鍵/值組指定為 \<key\> ： \<value\> 。 以下是您用來指定使用者位置的索引鍵。<br /><br /><ul><li>lat &mdash; 用戶端所在位置的緯度，以度為單位。 緯度必須大於或等於 -90.0 且小於或等於 +90.0。 負數值表示南半球的緯度，正數值表示北半球的緯度。<br /><br /></li><li>long &mdash; 用戶端所在位置的經度，以度為單位。 經度必須大於或等於 -180.0 且小於或等於 +180.0。 負數值表示東半球的經度，正數值表示西半球的經度。<br /><br /></li><li>re &mdash; 以公尺為單位的半徑，指定座標的水平精確度。 請傳遞裝置的位置服務所傳回的值。 常見的值可能是 22m (用於 GPS/Wi-Fi)、380m (用於無線訊號基地台三角網定位)，和 18,000m (用於保留 IP 查閱)。<br /><br /></li><li>ts &mdash; 用戶端位於該位置的 UTC UNIX 時間戳記。 (UNIX 時間戳記是自 1970 年 1 月 1 日起經過的秒數)。<br /><br /></li><li>head &mdash; 選擇性。 用戶端移動的相對走向或方向。 指定 0 到 360 度的的移動方向為，計算相對於正北的順時針偏移角度。 只有在 `sp` 索引鍵為非零值時，才需要指定此索引鍵。<br /><br /></li><li>sp &mdash; 用戶端裝置移動的水平速度，以公尺/秒為單位。<br /><br /></li><li>alt &mdash; 用戶端裝置的海拔高度，以公尺為單位。<br /><br /></li><li>are &mdash; 選擇性。 以公尺為單位的半徑，指定座標的垂直精確度。 半徑預設為 50 公里。 只有在指定了 `alt` 索引鍵時，才需要指定此索引鍵。<br /><br /></li></ul> **注意：** 雖然這些索引鍵是選擇性的，但您提供的資訊愈詳細，位置結果就愈精確。<br /><br /> **注意：** 建議您一律指定使用者的地理位置。 如果用戶端的 IP 位址未精確反映使用者的實體位置 (例如，如果用戶端使用 VPN)，提供位置就益發重要。 若要獲得最佳結果，您應加入此標頭和 X-MSEdge-ClientIP 標頭，但加入此標頭是最基本的要求。|

> [!NOTE] 
> 請記住，使用條款會要求您遵守所有適用法規，包括使用這些標頭的相關法規。 例如，特定轄區 (例如歐洲) 會要求必須先取得使用者同意，才可在使用者裝置上放置特定追蹤裝置。
  

## <a name="query-parameters"></a>查詢參數  
要求可能含有下列查詢參數。 請參閱必要參數的必要資料行。 您必須對查詢參數進行 URL 編碼。  
  
  
|名稱|值|類型|必要|  
|----------|-----------|----------|--------------|
|<a name="count"></a>count|要傳回的結果數目，從參數指定的索引開始 `offset` 。|String|否|   
|<a name="localCategories"></a>localCategories|依商家類別定義搜尋的選項清單。  請參閱[當地商家類別搜尋](local-categories.md)|String|否|  
|<a name="mkt"></a>mkt|產生結果的市場。 <br /><br />如需可能的市場值清單，請參閱市場代碼。<br /><br /> **注意：** 當地商家搜尋 API 目前僅支援 en-us 市場和語言。<br /><br />|String|是|
|<a name="offset"></a>offset|用於啟動 `count` 參數指定之結果的索引。|整數|否|  
|<a name="query"></a>q|使用者的搜尋字詞。|String|否|  
|<a name="responseformat"></a>responseFormat|要用於回應的媒體類型。 以下是可能的值 (不區分大小寫)。<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> 預設值為 JSON。 如需回應所含 JSON 物件的相關資訊，請參閱[回應物件](#response-objects)。<br /><br />  如果您指定 JsonLd，回應本文會有內含搜尋結果的 JSON-LD 物件。 如需 JSON-LD 的相關資訊，請參閱 [JSON-LD](https://json-ld.org/)。|String|否|  
|<a name="safesearch"></a>safeSearch|用來篩選成人內容的篩選條件。 以下是可能的篩選值 (不區分大小寫)。<br /><ul><li>關閉 &mdash; 傳回含有成人文字、影像或視訊的網頁。<br /><br/></li><li>中度 &mdash; 傳回含有成人文字、但不含成人影像或視訊的網頁。<br /><br/></li><li>嚴格 &mdash; 不傳回含有成人文字、影像或視訊的網頁。</li></ul><br /> 預設值為「中度」。<br /><br /> **注意：** 如果要求來自於 Bing 的成人內容原則必須將 `safeSearch` 設為「嚴格」的市場，Bing 將會忽略 `safeSearch` 值並使用「嚴格」。<br/><br/>**注意：** 如果您使用 `site:` 查詢運算子，則無論 `safeSearch` 查詢參數設定為何，回應都有可能包含成人內容。 只有在您了解網站上的內容，而且您的案例支援成人內容的可能性時，才可使用 `site:`。 |String|否|  
|<a name="setlang"></a>setLang|用於使用者介面字串的語言。 請使用 ISO 639-1 2 字母語言代碼指定語言。 例如，英文的語言代碼是 EN。 預設值為 EN (英文)。<br /><br /> 語言雖然是選擇性的，但您應一律加以指定。 一般而言，除非使用者想要以不同的語言顯示使用者介面字串，否則您都會將 `setLang` 設定為 `mkt` 所指定的相同語言。<br /><br /> 此參數和 [Accept-Language](#acceptlanguage) 標頭彼此互斥 &mdash; 請勿同時指定。<br /><br /> 使用者介面字串是在使用者介面中作為標籤的字串。 JSON 回應物件中有幾個使用者介面字串。 同樣地，回應物件中 Bing.com 屬性的任何連結都會套用指定的語言。|String|否| 


## <a name="response-objects"></a>回應物件  
以下是回應可能包含的 JSON 回應物件。 如果要求成功，則回應中的最上層物件是 [SearchResponse](#searchresponse) 物件。 如果要求失敗，則最上層的物件是 [ErrorResponse](#errorresponse) 物件。


|Object|描述|  
|------------|-----------------|  
|[Place](#place)|定義有關當地商家 (例如餐廳或旅館) 的相關資訊。|  

  
### <a name="error"></a>錯誤  
定義發生的錯誤。  
  
|元素|描述|類型|  
|-------------|-----------------|----------|  
|<a name="error-code"></a>code|識別錯誤類別的錯誤碼。 如需可能的代碼清單，請參閱[錯誤碼](#error-codes)。|String|  
|<a name="error-message"></a>message|錯誤的描述。|String|  
|<a name="error-moredetails"></a>moreDetails|提供其他錯誤相關資訊的描述。|String|  
|<a name="error-parameter"></a>parameter|要求中導致錯誤的查詢參數。|String|  
|<a name="error-subcode"></a>subCode|識別錯誤的錯誤碼。 例如，如果 `code`是 InvalidRequest，則 `subCode` 可能是 ParameterInvalid 或 ParameterInvalidValue。 |String|  
|<a name="error-value"></a>value|非有效的查詢參數值。|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
要求失敗時，回應包含的最上層物件。  
  
|名稱|值|類型|  
|----------|-----------|----------|  
|_type|類型提示。|String|  
|<a name="errors"></a>errors|說明要求失敗原因的錯誤清單。|[錯誤](#error)[]|  

  
  
### <a name="license"></a>授權  
定義可據以使用文字或相片的授權。  
  
|名稱|值|類型|  
|----------|-----------|----------|  
|NAME|授權的名稱。|String|  
|url|可讓使用者取得更多授權相關資訊的網站 URL。<br /><br /> 請使用名稱和 URL 建立超連結。|String|  


### <a name="link"></a>連結  
定義超連結的元件。  
  
|名稱|值|類型|  
|----------|-----------|----------|  
|_type|類型提示。|String|  
|text|顯示文字。|String|  
|url|URL。 使用 URL 和顯示文字建立超連結。|String|  
  


  
### <a name="organization"></a>組織  
定義發行者。  
  
請注意，發行者可以提供其名稱和 (或) 網站。  
  
|名稱|值|類型|  
|----------|-----------|----------|  
|NAME|發行者的名稱。|String|  
|url|發行者網站的 URL。<br /><br /> 請注意，發行者可能不會提供網站。|String|  
  
  

### <a name="place"></a>位置  
定義有關當地商家的相關資訊，例如餐廳或旅館。  
  
|名稱|值|類型|  
|----------|-----------|----------|  
|_type|類型提示，可能會設定為下列其中之一：<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>餐廳</ul><li>|String|  
|address|實體所在位置的郵政地址。|PostalAddress|  
|entityPresentationInfo|有關實體的其他資訊，例如可用於判斷實體類型的提示。 例如，是餐廳還是旅館。 `entityScenario` 欄位設定為 ListItem。|EntityPresentationInfo|  
|NAME|實體的名稱。|String|  
|telephone|實體的電話號碼。|String|  
|url|實體網站的 URL。<br /><br /> 使用此 URL 以及實體的名稱建立超連結，按一下該連結會將使用者帶到實體的網站。|String|  
|webSearchUrl|此地點的 Bing 搜尋結果 URL。|String| 
  
  
### <a name="querycontext"></a>QueryContext  
定義 Bing 針對要求而使用的查詢內容。  
  
|元素|描述|類型|  
|-------------|-----------------|----------|  
|adultIntent|一個布林值，用以指出指定的查詢是否有成人意圖。 如果查詢有成人意圖，則此值為 **true**，若沒有則為 **false**。|Boolean|  
|alterationOverrideQuery|要用來強制 Bing 使用原始字串的查詢字串。 例如，如果查詢字串為 *saling downwind*，覆寫查詢字串將是 *+saling downwind*。 請記得編碼會產生 *%2Bsaling+downwind* 的查詢字串。<br /><br /> 只有原始查詢字串包含拼字錯誤時，才需要加入此欄位。|String|  
|alteredQuery|Bing 用來執行查詢的查詢字串。 如果原始查詢字串包含拼字錯誤，Bing 就會使用更改的查詢字串。 例如，如果查詢字串是 `saling downwind`，更改的查詢字串將是 `sailing downwind`。<br /><br /> 只有原始查詢字串包含拼字錯誤時，才需要加入此欄位。|String|  
|askUserForLocation|一個布林值，用以指出 Bing 是否需要使用者的位置以提供精確的結果。 如果您已使用 [X-MSEdge-ClientIP](#clientip) 和 [X-Search-Location](#location) 標頭指定使用者的位置，則可以忽略此欄位。<br /><br /> 針對需要使用者位置以提供精確結果的位置感知查詢 (例如「今天的天氣」或「這附近的餐廳」)，此欄位會設定為 **true**。<br /><br /> 針對包含位置的位置感知查詢 (例如「西雅圖的天氣」)，此欄位會設定為 **false**。 針對不是位置感知的查詢 (例如「最佳銷售員」)，此欄位也會設定為 **false**。|Boolean|  
|originalQuery|指定於要求中的查詢字串。|String|  

### <a name="identifiable"></a>Identifiable

|名稱|值|類型|  
|-------------|-----------------|----------|
|id|資源識別碼|String|
 
### <a name="rankinggroup"></a>RankingGroup
定義搜尋結果群組，例如主線。

|名稱|值|類型|  
|-------------|-----------------|----------|
|項目|要顯示在群組中的搜尋結果清單。|RankingItem|

### <a name="rankingitem"></a>RankingItem
定義要顯示的搜尋結果項目。

|名稱|值|類型|  
|-------------|-----------------|----------|
|resultIndex|答案中要顯示的項目以零為起始的索引。 如果該項目未包含此欄位，則會顯示答案中的所有項目。 例如，顯示「新聞」答案中的所有新聞發行項。|整數|
|answerType|包含要顯示項目的答案。 例如「新聞」。<br /><br />請使用類型在 SearchResponse 物件中尋找答案。 類型是 SearchResponse 欄位的名稱。<br /><br /> 不過，只有在此物件包含值欄位時，才需要使用答案類型，否則請加以忽略。|String|
|textualIndex|TextualAnswers 中要顯示答案的索引。| 不帶正負號的整數|
|value|此為識別碼，用來識別要顯示的答案或要顯示的答案項目。 如果此識別碼用來識別答案，則會顯示答案的所有項目。|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
定義內容在搜尋結果頁面上的放置位置和順序。  
  
|名稱|值|  
|----------|-----------|  
|<a name="ranking-mainline"></a>mainline|要顯示在主線中的搜尋結果。|  
|<a name="ranking-pole"></a>pole|應獲得最明顯處理的搜尋結果 (例如，顯示在主線和資訊看板上方)。|  
|<a name="ranking-sidebar"></a>sidebar|要顯示在資訊看板中的搜尋結果。| 

### <a name="searchresponse"></a>SearchResponse  
定義回應在要求成功時所包含的最上層物件。  
  
請注意，如果服務懷疑有阻絕服務攻擊，要求就會成功 (HTTP 狀態碼為 200 OK)；不過，回應本文將是空的。  
  
|名稱|值|類型|  
|----------|-----------|----------|  
|_type|類型提示，設定為 SearchResponse。|String|  
|places|與搜索查詢相關的實體清單。|JSON 物件|  
|queryContext|包含 Bing 用於要求之查詢字串的物件。<br /><br /> 此物件包含使用者輸入的查詢字串。 如果查詢字串包含拼字錯誤，它還可能包含 Bing 針對查詢所使用的變更查詢字串。|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>錯誤碼

以下是要求傳回的可能 HTTP 狀態碼。  
  
|狀態碼|描述|  
|-----------------|-----------------|  
|200|成功。|  
|400|其中一個查詢參數遺失或無效。|  
|401|缺少訂用帳戶金鑰或無效。|  
|403|使用者已通過身分驗證 (例如已使用有效的訂用帳戶金鑰)，但並未擁有所要求的資源的權限。<br /><br /> 如果呼叫者超過其每月查詢配額，Bing 可能也會傳回此狀態。|  
|410|要求所用的是 HTTP 而非 HTTPS 通訊協定。 HTTPS 是唯一支援的通訊協定。|  
|429|呼叫者超過其每秒查詢配額。|  
|500|意外的伺服器錯誤。|

如果要求失敗，回應會包含 [ErrorResponse](#errorresponse) 物件，內有說明錯誤原因的 [Error](#error) 物件清單。 如果錯誤與參數有關，則 `parameter` 欄位會識別有問題的參數。 如果錯誤與參數值有關，則 `value` 欄位會識別無效的值。

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

以下是可能的錯誤碼和子錯誤碼值。

|程式碼|子代碼|說明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP 狀態碼為 500。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>封鎖|只要要求的任何部分無效，Bing 就會傳回 InvalidRequest。 例如缺少必要的參數或參數值無效。<br/><br/>如果錯誤是 ParameterMissing 或 ParameterInvalidValue，則 HTTP 狀態碼為 400。<br/><br/>如果您使用的是 HTTP 通訊協定，而不是 HTTPS，Bing 會傳回 HttpNotAllowed，且 HTTP 狀態碼為 410。
|RateLimitExceeded|沒有子代碼|每當您超過每秒查詢 (QPS) 或每月查詢 (QPM) 配額時，Bing 會傳回 RateLimitExceeded。<br/><br/>如果您超過 QPS，Bing 會傳回 HTTP 狀態碼 429，如果您超過 QPM，Bing 會傳回 403。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|當 Bing 無法驗證呼叫者時，Bing 會傳回 InvalidAuthorization。 例如，缺少 `Ocp-Apim-Subscription-Key` 標頭，或訂用帳戶金鑰無效。<br/><br/>如果您指定一個以上的驗證方法，則會出現備援。<br/><br/>如果錯誤是 InvalidAuthorization，則 HTTP 狀態碼為 401。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|當呼叫者沒有資源存取權限時，Bing 會傳回 InsufficientAuthorization。 如果訂用帳戶金鑰已停用或已過期，則會發生此情況。 <br/><br/>如果錯誤是 InsufficientAuthorization，則 HTTP 狀態碼為 403。

## <a name="next-steps"></a>接下來的步驟
- [當地商家搜尋快速入門](quickstarts/local-quickstart.md)
- [當地商家搜尋 Java 快速入門](quickstarts/local-search-java-quickstart.md)
- [當地商家搜尋 Node 快速入門](quickstarts/local-search-node-quickstart.md)
- [當地商家搜尋 Python 快速入門](quickstarts/local-search-python-quickstart.md)
