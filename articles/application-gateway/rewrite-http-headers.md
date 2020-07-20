---
title: 使用 Azure 應用程式閘道重寫 HTTP 標頭 |Microsoft Docs
description: 本文提供在 Azure 應用程式閘道中重寫 HTTP 標頭的總覽
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: fb5196f9612cb4ce1f0a49be8b5a76f6703fdab6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85248665"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>使用應用程式閘道重寫 HTTP 標頭

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 標頭可讓用戶端和伺服器透過要求或回應傳遞其他資訊。 藉由重寫這些標頭，您可以完成重要的工作，例如新增與安全性相關的標頭欄位（如 HSTS/X-XSS-保護）、移除可能會顯示機密資訊的回應標頭欄位，以及從 X 轉送的標頭移除埠資訊。

應用程式閘道可讓您在要求和回應封包於用戶端與後端集區之間移動時，新增、移除或更新 HTTP 要求和回應標頭。 其可讓您新增條件，以確保僅在符合特定條件時才重寫指定的標頭。

應用程式閘道也支援數個[伺服器變數](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables)，可協助您儲存有關要求和回應的其他資訊。 這可讓您更輕鬆地建立強大的重寫規則。

> [!NOTE]
>
> HTTP 標頭重寫支援僅適用于[Standard_V2 和 WAF_V2 SKU](application-gateway-autoscaling-zone-redundant.md)。

![重寫標頭](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>支援的標頭

除了主機、連接和升級標頭以外，您可以重寫要求和回應中的所有標頭。 您也可以使用應用程式閘道來建立自訂標頭，並將其新增至透過它所路由傳送的要求和回應。

## <a name="rewrite-conditions"></a>重寫條件

您可以使用重寫條件來評估 HTTP （S）要求和回應的內容，並只在符合一或多個條件時執行標頭重寫。 應用程式閘道會使用這些類型的變數來評估 HTTP （S）要求和回應的內容：

- 要求中的 HTTP 標頭。
- 回應中的 HTTP 標頭。
- 應用程式閘道的伺服器變數。

您可以使用條件來評估指定的變數是否存在、指定的變數是否符合特定的值，或指定的變數是否符合特定模式。 您可以使用[Perl 相容的正則運算式（PCRE）程式庫](https://www.pcre.org/)，在條件中設定正則運算式模式比對。 若要瞭解正則運算式語法，請參閱[Perl 正則運算式主頁面](https://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重寫動作

您可以使用重寫動作來指定您想要重寫的要求和回應標頭，以及標頭的新值。 您可以建立新的標頭、修改現有標頭的值，或刪除現有的標頭。 新標頭或現有標頭的值可以設定為這些類型的值：

- Text。
- 要求標頭。 若要指定要求標頭，您必須使用語法 {HTTP_req_*headerName*}。
- 回應標頭。 若要指定回應標頭，您必須使用語法 {HTTP_resp_*headerName*}。
- 伺服器變數。 若要指定伺服器變數，您必須使用語法 {var_*serverVariable*}。
- 文字、要求標頭、回應標頭和伺服器變數的組合。

## <a name="server-variables"></a>伺服器變數

應用程式閘道使用伺服器變數來儲存有關伺服器、與用戶端的連接，以及連接上目前要求的實用資訊。 儲存的資訊範例包括用戶端的 IP 位址和網頁瀏覽器類型。 伺服器變數會動態變更，例如當新頁面載入時，或當表單張貼時。 您可以使用這些變數來評估重寫條件和重寫標頭。 若要使用伺服器變數的值來重寫標頭，您將需要在 {var_*serverVariable*} 語法中指定這些變數。

應用程式閘道支援下列伺服器變數：

| 變數名稱 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | 以 IP1、IP2、I P 3 等格式附加變數的 [X-轉送-適用于用戶端要求標頭] 欄位 `client_ip` （請參閱本表格稍後的說明）。 如果 X 轉送的欄位不在用戶端要求標頭中，則 `add_x_forwarded_for_proxy` 變數會等於 `$client_ip` 變數。 當您想要重寫由應用程式閘道所設定之 X 轉送的標頭時，這個變數特別有用，因為標頭只包含不含埠資訊的 IP 位址。 |
| ciphers_supported          | 用戶端支援的密碼清單。          |
| ciphers_used               | 用於已建立之 TLS 連線的加密字串。 |
| client_ip                  | 應用程式閘道從中接收要求之用戶端的 IP 位址。 如果應用程式閘道和原始用戶端之前有反向 proxy， *client_ip*會傳回反向 PROXY 的 ip 位址。 |
| client_port                | 用戶端埠。                                                  |
| client_tcp_rtt             | 用戶端 TCP 連線的相關資訊。 可在支援 TCP_INFO 通訊端選項的系統上使用。 |
| client_user                | 使用 HTTP 驗證時，提供用於驗證的使用者名稱。 |
| 主機                       | 依照下列優先順序：要求行中的主機名稱、主機要求標頭欄位中的主機名稱，或符合要求的伺服器名稱。 範例：在要求中 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* ，主機值會是*contoso.com* |
| cookie_*名稱*              | *名稱*cookie。                                            |
| http_method                | 用來提出 URL 要求的方法。 例如，GET 或 POST。 |
| http_status                | 會話狀態。 例如，200、400或403。                       |
| http_version               | 要求通訊協定。 通常是 HTTP/1.0、HTTP/1.1 或 HTTP/2.0。 |
| query_string               | 在要求的 URL 中，後面接著 "？" 的變數/值配對清單。 範例：在要求中 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* ，query_string 值會是*識別碼 = 123&title = fabrikam* |
| received_bytes             | 要求的長度（包括要求行、標頭和要求本文）。 |
| request_query              | 要求行中的引數。                                |
| request_scheme             | 要求配置： HTTP 或 HTTPs。                            |
| request_uri                | 完整的原始要求 URI （含引數）。 範例：在要求中 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* ，request_uri 值會是 */article.aspx？ id = 123&title = fabrikam*   |
| sent_bytes                 | 傳送至用戶端的位元組數目。                             |
| server_port                | 接受要求之伺服器的埠。                 |
| ssl_connection_protocol    | 已建立 TLS 連接的通訊協定。        |
| ssl_enabled                | 如果連接是以 TLS 模式運作，則為 "On"。 否則為空字串。 |
| uri_path                   | 識別 web 用戶端想要存取之主機中的特定資源。 這是要求 URI 中沒有引數的部分。 範例：在要求中 *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* ，uri_path 值會是 */article.aspx*  |

## <a name="rewrite-configuration"></a>重寫設定

若要設定 HTTP 標頭重寫，您需要完成這些步驟。

1. 建立 HTTP 標頭重寫所需的物件：

   - **重寫動作**：用來指定您想要重寫的要求和要求標頭欄位，以及標頭的新值。 您可以將一或多個重寫條件與重寫動作產生關聯。

   - **重寫條件**：選擇性設定。 重寫條件會評估 HTTP （S）要求和回應的內容。 如果 HTTP （S）要求或回應符合重寫條件，則會發生重寫動作。

     如果您將一個以上的條件與一個動作建立關聯，只有在符合所有條件時，才會發生此動作。 換句話說，作業是邏輯 AND 運算。

   - **重寫規則**：包含多個重寫動作/重寫條件組合。

   - **規則順序**：有助於判斷重寫規則的執行順序。 當重寫集內有多個重寫規則時，此設定會很有説明。 規則順序值較低的重寫規則會先執行。 如果您將相同的規則順序指派給兩個重寫規則，執行的順序會不具決定性。

   - **重寫集**：包含多個將與要求路由規則相關聯的重寫規則。

2. 將重寫集（*rewriteruleset:*）附加至路由規則。 重寫設定會透過路由規則附加至來源接聽程式。 當您使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，而且是全域標頭重寫。 當您使用以路徑為基礎的路由規則時，會在 URL 路徑對應上定義標頭重寫設定。 在這種情況下，它只適用于網站的特定路徑區域。
   > [!NOTE]
   > URL 重寫改變標頭;它不會變更路徑的 URL。

您可以建立多個 HTTP 標頭重寫集，並將每個重寫集套用到多個接聽程式。 但是，您只能將一個重寫集套用到特定的接聽程式。

## <a name="common-scenarios"></a>常見案例

以下是使用標頭重寫的一些常見案例。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>從 X 轉送的標頭移除埠資訊

應用程式閘道會在將要求轉寄至後端之前，將標頭的 X 轉送-插入至所有要求。 此標頭是以逗號分隔的 IP 埠清單。 在某些情況下，後端伺服器只需要標頭以包含 IP 位址。 您可以使用標頭重寫，從 X 轉送的標頭中移除埠資訊。 執行此動作的其中一種方法是將標頭設定為 add_x_forwarded_for_proxy 伺服器變數：

![移除埠](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>修改重新導向 URL

當後端應用程式傳送重新導向回應時，您可能會想要將用戶端重新導向至不同于後端應用程式所指定的 URL。 例如，您可能會想要在應用程式閘道後方裝載 app service 時執行此動作，並要求用戶端重新導向至其相對路徑。 （例如，從 contoso.azurewebsites.net/path1 到 contoso.azurewebsites.net/path2 的重新導向）。

因為 App Service 是多租使用者服務，所以它會使用要求中的主機標頭，將要求路由傳送至正確的端點。 應用程式服務的預設功能變數名稱為 *. azurewebsites.net （例如 contoso.azurewebsites.net），不同于應用程式閘道的功能變數名稱（例如 contoso.com）。 由於用戶端的原始要求具有應用程式閘道的功能變數名稱（contoso.com）做為主機名稱，因此應用程式閘道會將主機名稱變更為 contoso.azurewebsites.net。 它會進行這種變更，讓 app service 可以將要求路由傳送至正確的端點。

當 app service 傳送重新導向回應時，它會在其回應的位置標頭中使用相同的主機名稱，就像它從應用程式閘道收到的要求中的一樣。 因此，用戶端會直接向 contoso.azurewebsites.net/path2 提出要求，而不是透過應用程式閘道（contoso.com/path2）。 不需要略過應用程式閘道。

若要解決這個問題，您可以將 location 標頭中的主機名稱設定為應用程式閘道的功能變數名稱。

以下是取代主機名稱的步驟：

1. 建立具有條件的重寫規則，以評估回應中的位置標頭是否包含 azurewebsites.net。 輸入模式 `(https?):\/\/.*azurewebsites\.net(.*)$` 。
1. 執行動作來重寫 location 標頭，使其具有應用程式閘道的主機名稱。 請輸入做 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 為標頭值來執行這項操作。

![修改位置標頭](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>執行安全性 HTTP 標頭以避免弱點

您可以藉由在應用程式回應中執行必要的標頭來修正數個安全性漏洞。 這些安全性標頭包含 X-XSS-保護、嚴格傳輸安全性和內容安全性原則。 您可以使用應用程式閘道來設定所有回應的這些標頭。

![安全性標頭](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>刪除不想要的標頭

您可能想要移除從 HTTP 回應中公開機密資訊的標頭。 例如，您可能會想要移除如後端伺服器名稱、作業系統或程式庫詳細資料等資訊。 您可以使用應用程式閘道來移除這些標頭：

![刪除標頭](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>檢查標頭是否存在

您可以針對標頭或伺服器變數的存在，評估 HTTP 要求或回應標頭。 當您只想要在有特定標頭時執行標頭重寫時，此評估會很有用。

![檢查標頭是否存在](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>限制

- 如果回應有多個相同名稱的標頭，則重寫其中一個標頭的值會導致在回應中卸載其他標頭。 這通常會發生在設定 Cookie 標頭中，因為在回應中可以有一個以上的設定 Cookie 標頭。 其中一種情況是，當您使用 app service 搭配應用程式閘道，並已在應用程式閘道上設定以 cookie 為基礎的會話親和性。 在此情況下，回應會包含兩個設定 Cookie 標頭：一個供 app service 使用，例如： `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` 另一個用於應用程式閘道親和性，例如 `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` 。 重寫此案例中的其中一個設定 Cookie 標頭，可能會導致從回應中移除另一個設定 Cookie 標頭。

- 當應用程式閘道設定為重新導向要求或顯示自訂錯誤頁面時，不支援重寫。

- 目前不支援重寫連接、升級和主機標頭。

- 標頭名稱可以包含任何英數位元和[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)中所定義的特定符號。 我們目前不支援 \_ 標頭名稱中的底線（）特殊字元。

## <a name="next-steps"></a>後續步驟

若要瞭解如何重寫 HTTP 標頭，請參閱：

- [使用 Azure 入口網站重寫 HTTP 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [使用 Azure PowerShell 重寫 HTTP 標頭](add-http-header-rewrite-rule-powershell.md)
