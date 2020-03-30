---
title: 使用 Azure 應用程式閘道重寫 HTTP 標頭 |微軟文檔
description: 本文概述了在 Azure 應用程式閘道中重寫 HTTP 標頭
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132988"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>使用應用程式閘道重寫 HTTP 標頭

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 標頭允許用戶端和伺服器通過請求或回應傳遞其他資訊。 通過重寫這些標頭，可以完成重要任務，例如添加與安全相關的標頭欄位，如 HSTS/X-XSS 保護、刪除可能顯示敏感資訊的回應標頭欄位，以及從 X-前轉-For 標頭中刪除埠資訊。

應用程式閘道可讓您在要求和回應封包於用戶端與後端集區之間移動時，新增、移除或更新 HTTP 要求和回應標頭。 其可讓您新增條件，以確保僅在符合特定條件時才重寫指定的標頭。

應用程式閘道還支援多個[伺服器變數](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables)，這些變數可説明您存儲有關請求和回應的其他資訊。 這樣，您就更容易地創建了強大的重寫規則。

> [!NOTE]
>
> HTTP 標頭重寫支援僅適用于[Standard_V2 和 sKU WAF_v2](application-gateway-autoscaling-zone-redundant.md)。

![重寫標頭](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>支援的標頭

您可以重寫請求和回應中的所有標頭，但主機、連接和升級標頭除外。 您還可以使用應用程式閘道創建自訂標頭，並將它們添加到通過它路由的請求和回應。

## <a name="rewrite-conditions"></a>重寫條件

可以使用重寫條件評估 HTTP（S） 請求和回應的內容，並且僅在滿足一個或多個條件時執行標頭重寫。 應用程式閘道使用這些類型的變數來評估 HTTP（S） 請求和回應的內容：

- 請求中的 HTTP 標頭。
- 回應中的 HTTP 標頭。
- 應用程式閘道伺服器變數。

可以使用條件計算是否存在指定變數、指定變數是否與特定值匹配，或者指定變數是否與特定模式匹配。 您可以使用[Perl 相容正則運算式 （PCRE） 庫](https://www.pcre.org/)在條件中設置正則運算式模式匹配。 要瞭解正則運算式語法，請參閱[Perl 正則運算式主頁](https://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重寫操作

使用重寫操作指定要重寫的請求和回應標頭以及標頭的新值。 您可以創建新標頭、修改現有標頭的值或刪除現有標頭。 可以將新標頭或現有標頭的值設置為這些類型的值：

- Text。
- 要求標頭。 要指定請求標頭，需要使用語法 [HTTP_req_*標頭名稱*]。
- 回應標頭。 要指定回應標頭，需要使用語法 [HTTP_resp_*標頭名稱*]。
- 伺服器變數。 要指定伺服器變數，需要使用語法 [var_*伺服器變數*]。
- 文本、請求標頭、回應標頭和伺服器變數的組合。

## <a name="server-variables"></a>伺服器變數

應用程式閘道使用伺服器變數來存儲有關伺服器、與用戶端的連接以及連接上的當前請求的有用資訊。 存儲的資訊示例包括用戶端的 IP 位址和 Web 瀏覽器類型。 伺服器變數會動態更改，例如，當新頁載入或發佈表單時。 您可以使用這些變數來評估重寫條件和重寫標頭。

應用程式閘道支援這些伺服器變數：

| 變數名稱 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | 以 IP1、IP2、IP3 等格式`client_ip`追加到的 X-前轉用戶端請求標頭欄位，其中附加了變數（請參閱下表後面的說明）。 如果 X-前行-For 欄位不在用戶端請求標頭中，則`add_x_forwarded_for_proxy`變數等於變數。 `$client_ip` 當您要重寫應用程式閘道設置的 X-前轉-for 標頭，以便標頭僅包含沒有埠資訊的 IP 位址時，此變數特別有用。 |
| ciphers_supported          | 用戶端支援的密碼的清單。          |
| ciphers_used               | 用於已建立 TLS 連接的密碼字串。 |
| client_ip                  | 應用程式閘道從中接收請求的用戶端的 IP 位址。 如果在應用程式閘道和原始用戶端之前有反向代理 *，client_ip*將返回反向代理的 IP 位址。 |
| client_port                | 用戶端埠。                                                  |
| client_tcp_rtt             | 有關用戶端 TCP 連接的資訊。 適用于支援TCP_INFO通訊端選項的系統。 |
| client_user                | 使用 HTTP 身份驗證時，提供用於身份驗證的使用者名。 |
| 主機                       | 按此優先順序順序排列：請求行中的主機名稱、來自主機請求標頭欄位的主機名稱或與請求匹配的伺服器名稱。 |
| cookie_*名稱*              | *名稱*Cookie。                                            |
| http_method                | 用於發出 URL 請求的方法。 例如，獲取或 POST。 |
| http_status                | 會話狀態。 例如，200、400 或 403。                       |
| http_version               | 請求協定。 通常 HTTP/1.0、HTTP/1.1 或 HTTP/2.0。 |
| query_string               | 在請求的 URL 中"？" 後面的變數/值對的清單。 |
| received_bytes             | 請求的長度（包括請求行、標頭和請求正文）。 |
| request_query              | 請求行中的參數。                                |
| request_scheme             | 請求方案：HTTP 或 HTTPs。                            |
| request_uri                | 完整的原始請求 URI（帶參數）。                   |
| sent_bytes                 | 發送到用戶端的位元組數。                             |
| server_port                | 接受請求的伺服器的埠。                 |
| ssl_connection_protocol    | 已建立的 TLS 連接的協定。        |
| ssl_enabled                | 如果連接在 TLS 模式下運行，則為"打開"。 否則，將使用空字串。 |

## <a name="rewrite-configuration"></a>重寫配置

要配置 HTTP 標頭重寫，您需要完成這些步驟。

1. 創建 HTTP 標頭重寫所需的物件：

   - **重寫操作**：用於指定要重寫的請求和請求標頭欄位以及標頭的新值。 您可以將一個或多個重寫條件與重寫操作相關聯。

   - **重寫條件**：可選配置。 重寫條件評估 HTTP（S） 請求和回應的內容。 如果 HTTP（S） 請求或回應與重寫條件匹配，則將執行重寫操作。

     如果將多個條件與操作相關聯，則僅當滿足所有條件時才會執行該操作。 換句話說，該操作是一個邏輯的 AND 操作。

   - **重寫規則**：包含多個重寫操作/重寫條件組合。

   - **規則序列**：説明確定重寫規則的執行順序。 當您重寫集中有多個重寫規則時，此配置非常有用。 具有較低規則序列值的重寫規則首先運行。 如果將相同的規則序列分配給兩個重寫規則，則執行順序是非確定性的。

   - **重寫集**：包含與請求路由規則關聯的多個重寫規則。

2. 將重寫集 （*重寫規則集*） 附加到路由規則。 重寫配置通過路由規則附加到源攔截器。 使用基本路由規則時，標頭重寫配置與源攔截器關聯，並且是全域標頭重寫。 使用基於路徑的路由規則時，將在 URL 路徑映射上定義標頭重寫配置。 在這種情況下，它僅適用于網站的特定路徑區域。
   > [!NOTE]
   > URL 重寫更改標頭;它不會更改路徑的 URL。

您可以創建多個 HTTP 標頭重寫集，並將每個重寫集應用於多個攔截器。 但是，您只能將一個重寫集應用於特定攔截器。

## <a name="common-scenarios"></a>常見的案例

下面是一些用於使用標頭重寫的常見方案。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>從 X 轉發-for 標頭中刪除埠資訊

應用程式閘道在將請求轉發到後端之前，將所有請求插入 X-前行-For 標頭。 此標頭是 IP 埠的逗號分隔清單。 在某些情況下，後端伺服器只需要標頭來包含 IP 位址。 可以使用標頭重寫從 X-前轉-for 標頭中刪除埠資訊。 執行此操作的一種方法是將標頭設置為add_x_forwarded_for_proxy伺服器變數：

![刪除埠](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>修改重定向 URL

當後端應用程式發送重定向回應時，您可能希望將用戶端重定向到與後端應用程式指定的 URL 不同的 URL。 例如，當應用服務託管在應用程式閘道後面，並要求用戶端對其相對路徑進行重定向時，您可能希望執行此操作。 （例如，從contoso.azurewebsites.net/path1重定向到contoso.azurewebsites.net/path2。

由於應用服務是多租戶服務，因此它使用請求中的主機標頭將請求路由到正確的終結點。 應用服務具有與應用程式閘道的功能變數名稱（例如contoso.com）不同的預設功能變數名稱 *azurewebsites.net（例如contoso.azurewebsites.net）。 由於來自用戶端的原始請求具有應用程式閘道的功能變數名稱 （contoso.com） 作為主機名稱，因此應用程式閘道將主機名稱更改為contoso.azurewebsites.net。 它進行此更改，以便應用服務可以將請求路由到正確的終結點。

當應用服務發送重定向回應時，它在其回應的位置標頭中使用與從應用程式閘道接收的請求中的主機名稱相同的主機名稱。 因此，用戶端將直接向contoso.azurewebsites.net/path2發出請求，而不是通過應用程式閘道 （contoso.com/path2）。 繞過應用程式閘道是不可取的。

您可以通過將位置標頭中的主機名稱設置為應用程式閘道的功能變數名稱來解決此問題。

以下是替換主機名稱的步驟：

1. 創建具有條件計算回應中的位置標頭是否包含azurewebsites.net的條件的重寫規則。 輸入模式`(https?):\/\/.*azurewebsites\.net(.*)$`。
1. 執行重寫位置標頭的操作，以便具有應用程式閘道的主機名稱。 為此，輸入`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`為標頭值。

![修改位置標頭](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>實施安全 HTTP 標頭以防止漏洞

通過在應用程式回應中實現必要的標頭，可以修復多個安全性漏洞。 這些安全標頭包括 X-XSS 保護、嚴格傳輸安全和內容安全性原則。 您可以使用應用程式閘道為所有回應設置這些標頭。

![安全標頭](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>刪除不需要的標頭

您可能希望刪除從 HTTP 回應中顯示敏感資訊的標頭。 例如，您可能希望刪除後端伺服器名稱、作業系統或庫詳細資訊等資訊。 您可以使用應用程式閘道刪除這些標頭：

![刪除標頭](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>檢查是否存在標頭

您可以計算 HTTP 要求或回應標頭是否存在標頭或伺服器變數。 僅當只想執行標頭重寫時，此計算非常有用。

![檢查標頭的存在](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>限制

- 如果回應有多個具有相同名稱的標頭，則重寫其中一個標頭的值將導致將其他標頭刪除到回應中。 這通常發生在 Set-Cookie 標頭中，因為回應中可以有多個 Set-Cookie 標頭。 其中一種情況是，當您使用具有應用程式閘道的應用服務並在應用程式閘道上配置了基於 Cookie 的會話相關性時。 在這種情況下，回應將包含兩個 Set-Cookie 標頭：例如，應用服務使用的一個標頭：`Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`另一個用於應用程式閘道關聯，例如。 `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` 重寫此方案中的 Set-Cookie 標頭之一可能會導致從回應中刪除另一個 Set-Cookie 標頭。

- 當前不支援重寫連接、升級和主機標頭。

- 標題名稱可以包含[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)中定義的任何字母數位字元和特定符號。 我們目前不支援標題名稱中的底線\_（ ） 特殊字元。

## <a name="next-steps"></a>後續步驟

要瞭解如何重寫 HTTP 標頭，請參閱：

- [使用 Azure 門戶重寫 HTTP 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [使用 Azure PowerShell 重寫 HTTP 標頭](add-http-header-rewrite-rule-powershell.md)
