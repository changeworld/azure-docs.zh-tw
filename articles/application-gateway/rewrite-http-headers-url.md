---
title: 使用 Azure 應用程式閘道重寫 HTTP 標頭和 URL |Microsoft Docs
description: 本文概述如何在 Azure 應用程式閘道中重寫 HTTP 標頭和 URL
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: surmb
ms.openlocfilehash: 93af3183ae9e969d14a35ce4e365d48895ef4e79
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216669"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>使用應用程式閘道重寫 HTTP 標頭和 URL

 應用程式閘道可讓您重寫所選的要求和回應內容。 您可以使用這項功能來轉譯 Url、查詢字串參數，以及修改要求和回應標頭。 它也可讓您新增條件，以確保只有在符合特定條件時，才會重寫 URL 或指定的標頭。 這些條件所根據的是要求和回應資訊。

>[!NOTE]
>HTTP 標頭和 URL 重寫功能僅適用于 [應用程式閘道 V2 SKU](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>支援重寫類型

### <a name="request-and-response-headers"></a>要求與回應標頭

HTTP 標頭可讓用戶端和伺服器透過要求或回應傳遞其他資訊。 藉由重寫這些標頭，您可以完成重要的工作，例如新增安全性相關的標頭欄位（例如 HSTS/X-XSS-保護）、移除可能會顯示機密資訊的回應標頭欄位，以及從 X 轉送的標頭移除埠資訊。

應用程式閘道可讓您在要求和回應封包於用戶端與後端集區之間移動時，新增、移除或更新 HTTP 要求和回應標頭。

若要瞭解如何使用 Azure 入口網站來重寫應用程式閘道的要求和回應標頭，請參閱 [這裡](rewrite-url-portal.md)。

![img](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**支援的標頭**

您可以重寫要求和回應中的所有標頭，但不包括連接和升級標頭。 您也可以使用應用程式閘道來建立自訂標頭，並將其新增至要求和透過該標頭路由傳送的回應。

### <a name="url-path-and-query-string-preview"></a>URL 路徑和查詢字串 (預覽) 

使用應用程式閘道中的 URL 重寫功能時，您可以：

* 重寫要求 URL 的主機名稱、路徑和查詢字串 

* 選擇在接聽程式上重寫所有要求的 URL，或只重寫符合您設定的一或多個條件的要求。 這些條件是以 (要求、標頭、回應標頭和伺服器變數) 的要求和回應屬性為基礎。

* 選擇路由傳送要求 (根據原始 URL 或重寫的 URL 來選取後端集區) 

若要瞭解如何使用 Azure 入口網站來以應用程式閘道重寫 URL，請參閱 [這裡](rewrite-url-portal.md)。

![描述以應用程式閘道重寫 URL 之程式的圖表。](./media/rewrite-http-headers-url/url-rewrite-overview.png)

>[!NOTE]
> URL 重寫功能目前為預覽狀態，僅適用于應用程式閘道的 Standard_v2 和 WAF_v2 SKU。 不建議在實際執行環境中使用。 若要深入瞭解預覽，請參閱 [這裡的使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="rewrite-actions"></a>重寫動作

您可以使用重寫動作來指定您要重寫的 URL、要求標頭或回應標頭，以及您想要重新撰寫的新值。 您可以將 URL 或新的或現有的標頭值設定為這些類型的值：

* Text
* 要求標頭。 若要指定要求標頭，您必須使用語法 {HTTP_req_*headerName*}
* 回應標頭。 若要指定回應標頭，您必須使用語法 {HTTP_resp_*headerName*}
* 伺服器變數。 若要指定伺服器變數，您必須使用語法 {var_*serverVariable*}。 查看支援的伺服器變數清單
* 文字、要求標頭、回應標頭和伺服器變數的組合。 

## <a name="rewrite-conditions"></a>重寫條件

您可以使用重寫條件（選擇性設定）來評估 HTTP (S) 要求和回應的內容，並只在符合一或多個條件時，才執行重寫。 應用程式閘道會使用這些類型的變數來評估要求和回應的內容：

* 要求中的 HTTP 標頭
* 回應中的 HTTP 標頭
* 應用程式閘道伺服器變數

您可以使用條件來評估指定的變數是否存在、指定的變數是否符合特定的值，或指定的變數是否符合特定模式。 


### <a name="pattern-matching"></a>模式比對 

應用程式閘道會在條件中使用正則運算式進行模式比對。 您可以使用與 [Perl 相容的正則運算式 (PCRE) 程式庫](https://www.pcre.org/) ，在條件中設定正則運算式模式比對。 若要深入瞭解正則運算式語法，請參閱 [Perl 正則運算式主頁面](https://perldoc.perl.org/perlre.html)。

### <a name="capturing"></a>擷取

若要抓取子字串以供稍後使用，請在條件 RegEx 定義中，將括弧放在與它相符的子模式周圍。 第一組括弧會將子字串儲存在1中，第二組儲存在2，依此類推。 您可以依需要使用任意數目的括弧;Perl 只會持續定義更多編號的變數，以代表這些已捕捉的字串。 [參考](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm)的一些範例： 

*  / ( \d) # A2\d) /# 比對兩位數，將它們捕捉到群組1和2 

* / ( \d +) /# 符合一或多個數位，並將其全部捕獲到群組1 

* / ( \d) +/# 比對數位一次或多次，將最後一次讀入群組1

一旦捕捉之後，您就可以使用下列格式，在動作集中參考它們：

* 針對要求標頭捕捉，您必須使用 {HTTP_req_headerName_groupNumber}。 例如，{HTTP_req_User-Agent_1} 或 {HTTP_req_User-Agent_2}
* 對於回應標頭捕捉，您必須使用 {HTTP_resp_headerName_groupNumber}。 例如 {HTTP_resp_Location_1} 或 {HTTP_resp_Location_2}
* 若為伺服器變數，您必須使用 {var_serverVariableName_groupNumber}。 例如 {var_uri_path_1} 或 {var_uri_path_2}

如果您想要使用整個值，您不應該提及數位。 只要使用 {HTTP_req_headerName} 的格式，就不需要 groupNumber。

## <a name="server-variables"></a>伺服器變數

應用程式閘道會使用伺服器變數來儲存有關伺服器的實用資訊、與用戶端的連接，以及目前的連接要求。 儲存的資訊範例包括用戶端的 IP 位址和網頁瀏覽器類型。 伺服器變數會動態變更，例如，當新頁面載入或張貼表單時。 您可以使用這些變數來評估重寫條件和重寫標頭。 若要使用伺服器變數的值來重寫標頭，您必須在語法 {var_*serverVariableName*} 中指定這些變數。

應用程式閘道支援下列伺服器變數：

|   變數名稱    |                   描述                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | 具有變數 (的 X 轉送的用戶端要求標頭欄位 `client_ip` 請參閱下表稍後的說明，) 以 IP1、IP2、i p 3 等格式附加。 如果 X 轉送的欄位不在用戶端要求標頭中，則 `add_x_forwarded_for_proxy` 變數等於 `$client_ip` 變數。   當您想要重寫應用程式閘道所設定的 X 轉送標頭，讓標頭只包含沒有埠資訊的 IP 位址時，此變數特別有用。 |
| ciphers_supported         | 用戶端支援的密碼清單。               |
| ciphers_used              | 已建立的 TLS 連接所使用的密碼字串。 |
| client_ip                 | 應用程式閘道接收要求的來源用戶端 IP 位址。 如果應用程式閘道和原始用戶端之前有反向 proxy， `client_ip` 將會傳回反向 proxy 的 IP 位址。 |
| client_port               | 用戶端埠。                                             |
| client_tcp_rtt            | 用戶端 TCP 連接的相關資訊。 可在支援 TCP_INFO 通訊端選項的系統上使用。 |
| client_user               | 使用 HTTP 驗證時，提供用於驗證的使用者名稱。 |
| 主機                      | 依此順序的優先順序：來自要求行的主機名稱、主機要求標頭欄位中的主機名稱，或符合要求的伺服器名稱。 範例：在要求中 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` ，主機值會是 `contoso.com` |
| cookie_*名稱*             | *名稱*cookie。                                           |
| http_method               | 用來提出 URL 要求的方法。 例如 GET 或 POST。 |
| http_status               | 會話狀態。 例如，200、400或403。           |
| http_version              | 要求通訊協定。 通常是 HTTP/1.0、HTTP/1.1 或 HTTP/2.0。 |
| query_string              | 在要求的 URL 中的 "？" 之後的變數/值組清單。 範例：在要求中 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` ，query_string 值會是 `id=123&title=fabrikam` |
| received_bytes            | 要求的長度 (包括要求行、標頭和要求主體) 。 |
| request_query             | 要求行中的引數。                           |
| request_scheme            | 要求配置： HTTP 或 HTTPs。                           |
| request_uri               | 完整原始要求 URI (，) 引數。 範例：在要求中 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` ，request_uri 值會是 `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | 傳送至用戶端的位元組數目。                        |
| server_port               | 接受要求之伺服器的埠。              |
| ssl_connection_protocol   | 已建立的 TLS 連接的通訊協定。               |
| ssl_enabled               | 如果連接以 TLS 模式運作，則為 "On"。 否則為空字串。 |
| uri_path                  | 識別主機中 web 用戶端想要存取的特定資源。 這是不含引數之要求 URI 的一部分。 範例：在要求中 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` ，uri_path 值會是 `/article.aspx` |

 

## <a name="rewrite-configuration"></a>重寫設定

若要設定重寫規則，您必須建立重寫規則集，並在其中新增重寫規則設定。

重寫規則集包含：

* **要求路由規則關聯：** 重寫設定會透過路由規則與來源接聽程式相關聯。 當您使用基本路由規則時，重寫設定會與來源接聽程式相關聯，而且是全域標頭重寫。 當您使用以路徑為基礎的路由規則時，會在 URL 路徑對應上定義重寫設定。 在這種情況下，它只會套用到網站的特定路徑區域。 您可以建立多個重寫集，並將每個重寫集套用至多個接聽程式。 但是，您只能將一個重寫集套用至特定的接聽程式。

* **重寫條件**：這是選擇性的設定。 重寫條件會評估 HTTP (S 的內容) 要求和回應。 如果 HTTP (S) 要求或回應符合重寫條件，則會發生重寫動作。 如果您將多個條件與某個動作產生關聯，則只有在符合所有條件時，才會發生此動作。 換句話說，此作業是邏輯 AND 運算。

* **重寫類型**：有3種類型的重寫可用：
   * 重寫要求標頭 
   * 重寫回應標頭。
   * 重寫 URL： URL 重寫類型有3個元件
      * **URL 路徑**：要重寫路徑的值。 
      * **URL 查詢字串**：要重新寫入查詢字串的目標值。 
      * **重新評估路徑對應**：用來判斷是否要重新評估 URL 路徑對應。 如果保持未核取，則會使用原始 URL 路徑來比對 URL 路徑對應中的路徑模式。 如果設定為 true，則會重新評估 URL 路徑對應，以檢查與重寫路徑相符的結果。 啟用此參數有助於在重寫後將要求路由傳送至不同的後端集區。

### <a name="common-scenarios-for-header-rewrite"></a>標頭重寫的常見案例

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>從 X 轉送的標頭移除埠資訊

在將要求轉送至後端之前，應用程式閘道會在所有要求中插入 X 轉送的標頭。 此標頭是以逗號分隔的 IP 埠清單。 在某些情況下，後端伺服器只需要包含 IP 位址的標頭。 您可以使用標頭重寫，從 X 轉送的標頭中移除埠資訊。 其中一個方法是將標頭設定為 add_x_forwarded_for_proxy server 變數。 或者，您也可以使用變數 client_ip：

![移除埠](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>修改重新導向 URL

當後端應用程式傳送重新導向回應時，您可能會想要將用戶端重新導向至與後端應用程式所指定的 URL 不同的 URL。 例如，您可能會想要在應用程式閘道後方裝載 app service 時這麼做，並且要求用戶端重新導向至其相對路徑。  (例如，從 contoso.azurewebsites.net/path1 重新導向至 contoso.azurewebsites.net/path2。 ) 

因為 App Service 是多租使用者服務，所以它會使用要求中的主機標頭將要求路由傳送至正確的端點。 應用程式服務的預設功能變數名稱為 *. azurewebsites.net (說 contoso.azurewebsites.net) 與應用程式閘道的功能變數名稱不同 (例如 contoso.com) 。 由於用戶端的原始要求具有應用程式閘道的功能變數名稱 (contoso.com) 作為主機名稱，因此應用程式閘道會將主機名稱變更為 contoso.azurewebsites.net。 它會進行這種變更，讓 app service 可以將要求路由傳送至正確的端點。

當 app service 傳送重新導向回應時，它會在其回應的 location 標頭中使用與從應用程式閘道收到的要求中相同的主機名稱。 因此，用戶端會直接向 contoso.azurewebsites.net/path2 提出要求，而不是透過應用程式閘道 (contoso.com/path2) 。 略過應用程式閘道不是理想的做法。

您可以藉由將 location 標頭中的主機名稱設定為應用程式閘道的功能變數名稱來解決此問題。

以下是取代主機名稱的步驟：

1. 建立重寫規則，其中的條件會評估回應中的位置標頭是否包含 azurewebsites.net。 輸入模式 `(https?):\/\/.*azurewebsites\.net(.*)$` 。
2. 執行動作以重寫 location 標頭，使其具有應用程式閘道的主機名稱。 若要這麼做，請輸入 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 做為標頭值。 或者，您也可以使用伺服器變數 `host` 來設定主機名稱，以符合原始要求。

![修改位置標頭](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>執行安全性 HTTP 標頭以防止弱點

您可以在應用程式回應中執行必要的標頭，以修正數個安全性弱點。 這些安全性標頭包含 X-XSS-保護、嚴格傳輸-安全性和內容安全性原則。 您可以使用應用程式閘道，為所有回應設定這些標頭。

![安全性標頭](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>刪除不必要的標頭

您可能會想要移除可從 HTTP 回應中顯示機密資訊的標頭。 例如，您可能想要移除資訊，例如後端伺服器名稱、作業系統或程式庫詳細資料。 您可以使用應用程式閘道來移除這些標頭：

![刪除標頭](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>檢查標頭是否存在

您可以評估 HTTP 要求或回應標頭，以瞭解標頭或伺服器變數是否存在。 當您只想要在出現特定標頭時執行標頭重寫時，此評估會很有用。

![檢查標頭是否存在](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>URL 重寫的常見案例

#### <a name="parameter-based-path-selection"></a>以參數為基礎的路徑選取專案

若要完成您想要根據標頭值、URL 部分或要求中的查詢字串來選擇後端集區的案例，您可以使用 URL 重寫功能和路徑型路由的組合。  例如，如果您有購物網站，而產品類別目錄是以查詢字串的形式傳遞至 URL，而且您想要根據查詢字串將要求路由至後端，則：

**步驟1：**  建立路徑對應，如下圖所示

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL 重寫案例1-1。":::

**步驟 2 () ：** 建立具有3個重寫規則的重寫集： 

* 第一個規則有一個條件，會檢查*類別 = 鞋*的*query_string*變數，並具有可將 URL 路徑重寫為/*Listing1* ，且已啟用**重新評估路徑對應**的動作

* 第二個規則有一個條件，會檢查*類別 = 包*的*query_string*變數，並具有會將 URL 路徑重寫為/*Listing2* ，且已啟用**重新評估路徑對應**的動作

* 第三個規則有一個條件，會檢查*類別 = 附屬*元件的*query_string*變數，並具有可將 URL 路徑重寫為/*Listing3* ，且已啟用**重新評估路徑對應**的動作

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL 重寫案例1-1。":::

 

**步驟 2 (b) ：** 將此重寫集與上述以路徑為基礎之規則的預設路徑產生關聯

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL 重寫案例1-1。":::

現在，如果使用者要求 *contoso.com/listing?category=any*，則會比對預設路徑，因為路徑對應中沒有任何路徑模式 (/listing1、/listing2、/listing3) 將會相符。 由於您將上述重寫集與此路徑相關聯，因此將會評估此重寫集。 由於查詢字串不符合此重寫集內任何3重寫規則中的條件，因此不會執行任何重寫動作，因此會將要求原封不動地路由傳送至與預設路徑相關聯的後端， (*GenericList*) 。

 如果使用者要求 *contoso.com/listing?category=shoes，* 則會再次符合預設路徑。 不過，在此情況下，第一個規則中的條件會相符，因此將會執行與條件相關聯的動作，這會將 URL 路徑重寫為/*listing1*  並重新評估路徑對應。 重新評估路徑對應時，要求現在會符合與模式 */listing1* 相關聯的路徑，而要求會路由傳送至與此模式相關聯的後端，也就是 ShoesListBackendPool

>[!NOTE]
>您可以根據所定義的條件，將此案例延伸至任何標頭或 cookie 值、URL 路徑、查詢字串或伺服器變數，基本上可讓您根據這些條件來路由傳送要求。

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>以 URL 為基礎重寫查詢字串參數

假設有一個購物網站案例，其中使用者可見的連結應該簡單明瞭，但後端伺服器需要查詢字串參數來顯示正確的內容。

在此情況下，應用程式閘道可以從 URL 抓取參數，並從 URL 新增查詢字串索引鍵/值組。 例如，假設使用者想要重寫， `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` 可以透過下列 URL 重寫設定來達成。

**條件** -如果伺服器變數 `uri_path` 等於模式 `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL 重寫案例1-1。":::

**動作** -將 URL 路徑設定為 `buy.aspx` ，並查詢字串 `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL 重寫案例1-1。":::

如需完成上述案例的逐步指南，請參閱 [使用 Azure 入口網站的應用程式閘道重寫 URL](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL 重寫與 URL 重新導向

在重寫 URL 的情況下，應用程式閘道會在要求傳送至後端之前重寫 URL。 這不會變更使用者在瀏覽器中看到的內容，因為使用者會隱藏變更。

在 URL 重新導向的情況下，應用程式閘道會使用新的 URL，將重新導向回應傳送給用戶端。 接著，用戶端會要求用戶端重新傳送其要求至重新導向中提供的新 URL。 使用者在瀏覽器中看到的 URL 將會更新為新的 URL

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="URL 重寫案例1-1。":::

## <a name="limitations"></a>限制

- 如果回應中有多個具有相同名稱的標頭，則重寫其中一個標頭的值，將會導致卸載回應中的其他標頭。 Set-Cookie 標頭通常會發生這種情況，因為您的回應中可以有一個以上的 Set-Cookie 標頭。 其中一種情況是當您使用應用程式服務搭配應用程式閘道，並在應用程式閘道上設定以 cookie 為基礎的會話親和性。 在此情況下，回應會包含兩個 Set-Cookie 標頭：一個是 app service 所使用的標頭，例如 `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` ，另一個用於應用程式閘道親和性，例如： `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` 。 在此案例中重寫其中一個 Set-Cookie 標頭可能會導致移除回應中的其他 Set-Cookie 標頭。
- 當應用程式閘道設定為重新導向要求或顯示自訂錯誤頁面時，不支援重寫。
- 標頭名稱可以包含任何英數位元和特定符號，如 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)中所定義。 我們目前不支援底線 (_) 標頭名稱中的特殊字元。
- 無法重寫連接和升級標頭

## <a name="next-steps"></a>後續步驟

- [瞭解如何使用 Azure 入口網站來重寫具有應用程式閘道的 HTTP 標頭](rewrite-http-headers-portal.md)
- [瞭解如何使用 Azure 入口網站來以應用程式閘道重寫 URL](rewrite-url-portal.md)
