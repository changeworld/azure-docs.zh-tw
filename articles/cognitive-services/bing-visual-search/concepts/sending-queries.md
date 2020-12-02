---
title: 將搜尋查詢傳送至 Bing 圖像式搜尋 API
titleSuffix: Azure Cognitive Services
description: 本文說明傳送給 Bing 圖像式搜尋 API 的要求所具有的參數和屬性，以及回應物件。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 37d9352b6384ee2b5e95903f35d531bd672b25b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490970"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>將搜尋查詢傳送至 Bing 圖像式搜尋 API

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

本文說明傳送給 Bing 圖像式搜尋 API 的要求所具有的參數和屬性，以及回應物件。 

您可以透過三種方式來取得關於影像的深入解析：

- 使用您在前一次呼叫其中一個 [BING 影像搜尋 API](/rest/api/cognitiveservices/bing-images-api-v7-reference) 端點的映射中取得的深入解析權杖。
- 傳送影像的 URL。
- 以二進位格式)  (上傳影像。

## <a name="bing-visual-search-requests"></a>Bing 圖像式搜尋要求

如果您將影像權杖或 URL 圖像式搜尋傳送，下列程式碼片段會顯示您必須包含在 POST 主體中的 JSON 物件：

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

`imageInfo` 物件必須包含 `url` 或 `imageInsightsToken` 欄位，但不可兩個欄位都包含。 將 `url` 欄位設定為可存取網際網路的影像 URL。 支援的影像大小上限為 1 MB。

`imageInsightsToken` 必須設為深入解析權杖。 若要取得深入解析權杖，請呼叫 Bing 影像 API。 回應會包含 `Image` 物件的清單。 每個 `Image` 物件各有一個 `imageInsightsToken` 欄位，其中包含此權杖。

`cropArea` 是選擇性欄位。 裁剪區域會指定感興趣區域的左上角和右下角。 請指定 0.0 到 1.0 之間的值。 此值是整體寬度或高度的百分比。 例如，上述範例將影像的右半邊標示為相關區域。 如果您想要將深入解析要求限定於相關區域，請包含該區域。

`filters` 物件包含一個網站篩選條件 (請參閱 `site` 欄位)，可用來將類似影像和類似產品的結果限定於特定網域。 例如，如果影像是 Surface Book，您可以將設定 `site` 為 `www.microsoft.com` 。

如果您想要深入了解影像的本機複本，請以二進位資料的形式上傳該影像。

如需在 POST 的本文中包含這些選項的詳細資訊，請參閱[內容表單類型](#content-form-types)。

### <a name="search-endpoint"></a>搜尋端點

圖像式搜尋端點是：https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch。

要求必須以 HTTP POST 要求的形式傳送。

### <a name="query-parameters"></a>查詢參數

以下是您的要求所應指定的查詢參數。 您至少應該包含 `mkt` 查詢參數：

| 名稱 | 值 | 類型 | 必要 |
| --- | --- | --- | --- |
| <a name="cc"></a>cc  | 代表結果來源的兩個字元的國家/地區代碼。<br /><br /> 若您設定此參數，則您也必須指定 [Accept-Language](#acceptlanguage) 標頭。 Bing 會使用它從語言清單中找到的第一個支援的語言，然後將其與您所指定的國碼 (地區碼) 結合，以決定要傳回結果的市場。 如果語言清單中未包含支援的語言，Bing 會就近尋找支援要求的語言和市場。 或者，它可能會將彙總或預設的市場用於結果，而不指定市場。<br /><br /> 只有在指定了多個語言時，才需要使用此查詢參數和 `Accept-Language` 查詢參數，否則，您應使用 `mkt` 和 `setLang` 查詢參數。<br /><br /> 此參數和 [mkt](#mkt) 查詢參數彼此互斥 &mdash; 請勿同時指定。 | 字串 | 否       |
| <a name="mkt"></a>mkt   | 產生結果的市場。 <br /><br /> **注意：** 您應該一律指定市場（如果已知）。 指定市場可協助 Bing 路由傳送要求，並傳回適當的最佳回應。<br /><br /> 此參數和 [cc](#cc) 查詢參數彼此互斥 &mdash; 請勿同時指定。 | String | 是      |
| <a name="safesearch"></a>safeSearch | 成人內容的篩選準則。 以下是可能的篩選值 (不區分大小寫)。<br /><ul><li>關閉 &mdash; 傳回含有成人文字或影像的網頁。<br /><br/></li><li>中度 &mdash; 傳回含有成人文字、但不含成人影像的網頁。<br /><br/></li><li>嚴格 &mdash; 不傳回含有成人文字或影像的網頁。</li></ul><br /> 預設值為「中度」。<br /><br /> **注意：** 如果要求來自於 Bing 的成人內容原則必須將 `safeSearch` 設為「嚴格」的市場，Bing 將會忽略 `safeSearch` 值並使用「嚴格」。<br/><br/>**注意：** 如果您使用 `site:` 查詢運算子，不論查詢參數設定為何，回應都有可能包含成人內容 `safeSearch` 。 只有在您了解網站上的內容，而且您的案例支援成人內容的可能性時，才可使用 `site:`。  | 字串 | 否       |
| <a name="setlang"></a>setLang  | 用於使用者介面字串的語言。 使用 ISO 639-1 兩個字母的語言代碼指定語言。 例如，英文的語言代碼是 EN。 預設值為 EN (英文)。<br /><br /> 語言雖然是選擇性的，但您應一律加以指定。 一般而言，除非使用者想要以不同的語言顯示使用者介面字串，否則您都會將 `setLang` 設定為 `mkt` 所指定的相同語言。<br /><br /> 此參數和 [Accept-Language](#acceptlanguage) 標頭彼此互斥 &mdash; 請勿同時指定。<br /><br /> 使用者介面字串是在使用者介面中作為標籤的字串。 JSON 回應物件中有幾個使用者介面字串。 同樣地，回應物件中 Bing.com 屬性的任何連結都會套用指定的語言。 | 字串 | 否   |

## <a name="headers"></a>標題

以下是您的要求所應指定的標頭。 `Content-Type`和 `Ocp-Apim-Subscription-Key` 標頭是唯一必要的標頭，但您也應該包含 `User-Agent` 、、 `X-MSEdge-ClientID` `X-MSEdge-ClientIP` 和 `X-Search-Location` 。

| 標頭 | 描述 |
| --- | --- |
| <a name="acceptlanguage"></a>Accept-Language  | 選擇性要求標頭。<br /><br /> 要用於使用者介面字串語言的逗號分隔清單。 清單採用喜好設定的遞減順序。 如需詳細資訊 (包括預期的格式)，請參閱 [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 此標頭和 [setLang](#setlang) 查詢參數彼此互斥 &mdash; 請勿同時指定。<br /><br /> 若您設定此標頭，則您也必須指定 [cc](#cc) 查詢參數。 若要決定要傳回結果的市場，Bing 會使用它從清單中找到的第一個支援的語言，然後將其與 `cc` 參數值結合。 如果清單中未包含支援的語言，Bing 會就近尋找支援要求的語言和市場，或將彙總或預設的市場用於結果。 若要判斷 Bing 使用的市場，請參閱 `BingAPIs-Market` 標頭。<br /><br /> 只有在指定了多種語言時，才需要使用此標頭和 `cc` 查詢參數。 否則，請使用 [mkt](#mkt) 和 [setLang](#setlang) 查詢參數。<br /><br /> 使用者介面字串是在使用者介面中作為標籤的字串。 JSON 回應物件中有幾個使用者介面字串。 回應物件中 Bing.com 屬性的任何連結都會套用指定的語言。  |
| <a name="contenttype"></a>Content-Type  |     |
| <a name="market"></a>BingAPIs-Market    | 回應標頭。<br /><br /> 要求所使用的市場。 格式為 \<languageCode\> - \<countryCode\> 。 例如：en-US。  |
| <a name="traceid"></a>BingAPIs-TraceId  | 回應標頭。<br /><br /> 包含要求詳細資料記錄項目的識別碼。 發生錯誤時，會擷取這個識別碼。 如果您無法判定並解決問題，請將此識別碼與其他資訊一併提供給支援小組。 |
| <a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key | 必要的要求標頭。<br /><br /> 您在[認知服務](https://www.microsoft.com/cognitive-services/)中註冊此服務時收到的訂用帳戶金鑰。 |
| <a name="pragma"></a>Pragma |   |
| <a name="useragent"></a>User-Agent  | 選擇性要求標頭。<br /><br /> 提出要求的使用者代理程式。 Bing 會利用使用者代理程式為行動使用者提供最佳體驗。 雖然是選擇性的，但我們仍建議您一律指定此標頭。<br /><br /> 「使用者代理程式」應為任何常用瀏覽器所傳送的相同字串。 如需使用者代理程式的詳細資訊，請參閱 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。<br /><br /> 以下是使用者代理程式字串的範例。<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid"></a>X-MSEdge-ClientID  | 選擇性要求和回應標頭。<br /><br /> Bing 使用此標頭在 Bing API 所有呼叫之間為使用者提供一致的行為。 Bing 經常推出新功能和改善項目，且會以用戶端識別碼作為不同測試版指派流量的金鑰。 如果在多個要求中，未讓使用者使用相同的用戶端識別碼，Bing 可能會將使用者指派至多個衝突的測試版。 若指派給多個衝突的測試版，可能會導致使用者體驗不一致。 例如若第二個要求與第一個要求指派的測試版不同，可能會產生意料外的體驗。 此外，Bing 可以使用用戶端識別碼將 Web 結果調整為該用戶端識別碼的搜尋記錄，為使用者提供更豐富的體驗。<br /><br /> Bing 也會使用此標頭分析用戶端識別碼產生的活動，協助改善結果的順位。 相關性改進功能有助於 Bing API 提供更高品質的結果，進而為 API 取用者提供更高的點擊率。<br /><br /> **重要事項：** 雖然是選擇性的，但您仍應將此標頭視為必要項目。 為相同的使用者和裝置組合跨多個要求保存用戶端識別碼，可讓 1) API 取用者獲得一致的使用者體驗，以及 2) 透過 Bing API 更理想的結果品質獲得較高的點擊率。<br /><br /> 以下是適用於此標頭的基本使用規則。<br /><ul><li>在裝置上使用您應用程式的每個使用者必須具有 Bing 產生的唯一用戶端識別碼。<br /><br/>如果您未在要求中包含此標頭，Bing 會產生一個識別碼，並於 X-MSEdge-ClientID 回應標頭傳回該識別碼。 不應在要求中包含此標頭的唯一時機是使用者初次在該裝置上使用您的應用程式時。<br /><br/></li><li>**注意：** 您必須確保此用戶端識別碼無法連結至任何已驗證的使用者帳戶資訊。</li><li>您的應用程式每次為該裝置上的該名使用者提出 Bing API 請求時，均需使用此用戶端識別碼。<br /><br/></li><li>保存用戶端識別碼。 若要在瀏覽器應用程式中保存識別碼，請使用永續性的 HTTP Cookie，以確保在所有工作階段均使用該識別碼。 請勿使用工作階段 Cookie。 若為其他應用程式 (例如行動裝置應用程式)，請使用裝置本身的永久儲存裝置保存識別碼。<br /><br/>使用者下次在該裝置上使用您的應用程式時，會取得您保存的用戶端識別碼。</li></ul><br /> **注意：** Bing 回應不一定會包含此標頭。 如果回應包含此標頭，請擷取用戶端識別碼，並將其用於該裝置上使用者的所有後續 Bing 要求。<br /><br /> **注意：** 如果您包含 X-MSEdge-ClientID，則不得在要求中加入 Cookie。 |
| <a name="clientip"></a>X-MSEdge-ClientIP   | 選擇性要求標頭。<br /><br /> 用戶端裝置的 IPv4 或 IPv6 位址。 此 IP 位址可用來探索使用者的位置。 Bing 會使用位置資訊來判斷安全搜尋行為。<br /><br /> **注意：** 雖然是選擇性的，但我們仍建議您一律指定此標頭和 X-Search-Location 標頭。<br /><br /> 請勿混淆位址 (例如，藉由將最後一個八位元變更為 0)。 混淆位址會導致位置不在裝置的實際位置附近，這可能會造成 Bing 產生錯誤結果。 |
| <a name="location"></a>X-Search-Location   | 選擇性要求標頭。<br /><br /> 以分號分隔的索引鍵/值組清單，用以說明用戶端的地理位置。 Bing 會使用位置資訊來判斷安全的搜尋行為，以及傳回相關的區域內容。 將索引鍵/值組指定為 \<key\> ： \<value\> 。 以下是您用來指定使用者位置的索引鍵。<br /><br /><ul><li>lat &mdash; 必要項目。 用戶端所在位置的緯度，以度為單位。 緯度必須大於或等於 -90.0 且小於或等於 +90.0。 負數值表示南半球的緯度，正數值表示北半球的緯度。<br /><br /></li><li>long &mdash; 必要項目。 用戶端所在位置的經度，以度為單位。 經度必須大於或等於 -180.0 且小於或等於 +180.0。 負數值表示東半球的經度，正數值表示西半球的經度。<br /><br /></li><li>re &mdash; 必要項目。 以公尺為單位的半徑，指定座標的水平精確度。 請傳遞裝置的位置服務所傳回的值。 一般值可能是適用于 GPS/Wi-fi 的 22 m、380 m 代表資料格中的資料格，而 18000 m 用於反向 IP 查閱。<br /><br /></li><li>ts &mdash; 選擇性。 用戶端位於該位置的 UTC UNIX 時間戳記。 (UNIX 時間戳記是自 1970 年 1 月 1 日起經過的秒數)。<br /><br /></li><li>head &mdash; 選擇性。 用戶端移動的相對走向或方向。 指定 0 到 360 度的的移動方向為，計算相對於正北的順時針偏移角度。 只有在 `sp` 索引鍵為非零值時，才需要指定此索引鍵。<br /><br /></li><li>sp &mdash; 選擇性。 用戶端裝置移動的水平速度，以公尺/秒為單位。<br /><br /></li><li>alt &mdash; 選擇性。 用戶端裝置的海拔高度，以公尺為單位。<br /><br /></li><li>are &mdash; 選擇性。 以公尺為單位的半徑，指定座標的垂直精確度。 只有在指定了 `alt` 索引鍵時，才需要指定此索引鍵。<br /><br /></li></ul> **注意：** 雖然有很多索引鍵是選擇性的，但您提供的資訊愈詳細，位置結果就愈精確。<br /><br /> **注意：** 使用者的地理位置雖然是選擇性的，但建議您一律加以指定。 如果用戶端的 IP 位址未精確反映使用者的實體位置 (例如，如果用戶端使用 VPN)，提供位置就益發重要。 為了獲得最佳結果，您應該包含此標頭和 `X-MSEdge-ClientIP` 標頭，但至少應該包含此標頭。       |

> [!NOTE]
> 請記住， [Bing 搜尋 API 使用和顯示需求](../../bing-web-search/use-display-requirements.md) 必須符合所有適用法律的規範，包括有關使用這些標頭的相關資訊。 例如，特定轄區 (例如歐洲) 會要求必須先取得使用者同意，才可在使用者裝置上放置特定追蹤裝置。

<a name="content-form-types"></a>

### <a name="content-form-types"></a>內容表單類型

每個要求都必須包含 `Content-Type` 標頭。 標頭必須設定為： `multipart/form-data; boundary=\<boundary string\>` ，其中 \<boundary string\> 是識別表單資料界限的唯一不透明字串。 例如： `boundary=boundary_1234-abcd` 。

如果您將影像權杖或 URL 圖像式搜尋傳送，下列程式碼片段會顯示您必須包含在 POST 主體中的表單資料。 表單資料必須包含 `Content-Disposition` 標頭，而且您必須將其 `name` 參數設定為 "knowledgeRequest"。 如需物件的詳細資訊 `imageInfo` ，請參閱要求。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

您可以選擇性地將 `enableEntityData` 標頭中的屬性設定為，以 `true` 取得上傳影像中主要實體的詳細資訊，包括 web 和屬性資訊的連結。 此欄位 `false` 預設為。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

如果您上傳本機影像，下列程式碼片段會顯示您必須包含在 POST 主體中的表單資料。 表單資料必須包含 `Content-Disposition` 標頭。 其 `name` 參數必須設定為 "image"，而 `filename` 參數可以設定為任何字串。 `Content-Type`標頭可設定為任何常用的影像 mime 類型。 表單的內容是影像的二進位資料。 您可以上傳的影像大小上限為 1 MB。 最大寬度或高度應該是 1,500 像素或更少。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

下列程式碼片段說明如何指定所上傳影像的相關區域：

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>範例要求

下列程式碼片段顯示通過映射權杖和感興趣區域的完整影像深入解析要求。 您可以從先前的/images/search 呼叫取得見解權杖：

```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```

## <a name="bing-visual-search-responses"></a>Bing 圖像式搜尋回應


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

如果影像有可用的深入解析，則回應會有一或多個包含深入解析的 `tags`。 `image`欄位包含輸入影像的深入解析權杖：

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

`tags` 欄位會包含顯示名稱和動作 (深入解析) 的清單。 其中一個標記會包含設為空字串的 `displayName` 欄位。 此標記包含預設的深入解析，例如包含影像、外觀上類似的影像，以及可購買到影像中所含項目之購物地點的網頁。 由於整個影像都很感興趣，因此預設深入解析標記不包含感興趣區域的周框方塊：

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

如需預設深入解析的清單，請參閱 [預設深入解析標記](../default-insights-tag.md)。

其餘標記會包含使用者可能感興趣的其他深入解析。 例如，如果影像中包含文字，則其中一個標記可能會有 TextResults 深入解析，其中包含已辨識的文字。 或者，如果 Bing 辨識出實體 (也就是文化特性知名/熱門人員、地點或) 在影像中，其中一個標記可能會識別實體。 圖像式搜尋也會傳回從輸入影像衍生的各種字詞 (標記) 集。 這些標記可讓使用者流覽影像中所找到的概念。 例如，如果輸入影像顯示的是知名運動員，則其中一個標記可能是「運動」，其中包含運動影像的連結。

每個標記都包含您可用來分類深入解析的顯示名稱、可對適用深入解析的相關區域進行識別的週框、深入解析本身，以及影像的縮圖。 例如，如果影像顯示的是穿著運動服的人，則其中一個標記可能會包含週框，用以界定運動服，以及包含 VisualSearch 和 ProductVisualSearch 深入解析。 而其他標記可能會有 ImageResults 深入解析，其中包含/影像/搜尋 API 要求的 URL，用以取得在主題上具有相關性的影像，或是包含 Bing.com 搜尋 URL 以將使用者導向至 Bing.com 影像搜尋結果。

預設深入解析標記以外的所有標記都會包含週框，用以識別影像中的相關區域。 例如，如果影像包含多個可辨識的人，標記可能會為每個人加上週框，或者，如果影像包含可辨識的服飾項目，則標記可能會為每個可辨識的服飾項目加上週框。 您可以使用週框在影像上建立作用點，而在點選這些作用點時提供與該影像區域中的內容有關的詳細資料。 您不應在影像中為識別整個影像的週框加上作用點。

### <a name="text-recognition"></a>文字辨識

如果影像中包含服務可辨識的文字，則其中一個標記將會包含 TextResults 深入解析 (動作)。 深入解析包含已辨識 `displayName` 的文字：

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

標記的 `displayName` 欄位包含 ##TextRecognition，因此請勿將其當作 UX 中的類別標題使用。 這樣會產生任何以 ## 開頭的顯示名稱。 相反地，請使用動作的顯示名稱。

文字辨識也可以辨識名片上的連絡資訊，例如電話號碼和電子郵件地址。 週框會識別連絡資訊在名片上的位置。

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

如果影像包含可辨識的實體，例如文化特性知名/熱門人員、地點或事物，則其中一個標記可能包含實體見解。 `mainEntity` `data` 只有當 `enableEntityData` 標頭中的屬性設定為時，才能使用和欄位 `Content-Type` `true` 。

```json
{
  "image" : {
    "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
  },
  "displayName" : "Statue of Liberty",
  "boundingBox" : {
    "queryRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    },
    "displayRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    }
  },
  "actions" : [
    {
      "_type" : "ImageEntityAction",
      "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
      "displayName" : "Statue of Liberty",
      "actionType" : "Entity",
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>另請參閱

- [什麼是 Bing 圖像式搜尋 API？](../overview.md)
- [教學課程：建立圖像式搜尋單頁 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)