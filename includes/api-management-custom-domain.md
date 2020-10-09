---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 3b42d5fbcfb19f08b46241dbe92e6a300bec1df6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81275457"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>APIM Proxy 伺服器在 TLS 交握中使用 SSL 憑證進行回應的方式

### <a name="clients-calling-with-sni-header"></a>具有 SNI 標頭的用戶端呼叫
如果客戶針對 Proxy 設定了一或多個自訂網域，APIM 就可以回應來自自訂網域 (例如，contoso.com) 以及預設網域 (例如，apim-service-name.azure-api.net) 的 HTTPS 要求。 APIM 會根據伺服器名稱指示 (SNI) 標頭中的資訊，使用適當的伺服器憑證來進行回應。

### <a name="clients-calling-without-sni-header"></a>不具有 SNI 標頭的用戶端呼叫
如果客戶使用的用戶端不會傳送 [SNI](https://tools.ietf.org/html/rfc6066#section-3) 標頭，APIM 就會根據下列邏輯建立回應：

* 如果服務針對 Proxy 只設定了一個自訂網域，預設憑證就是發給 Proxy 自訂網域的憑證。
* 如果服務已針對 Proxy 設定多個自訂網域 (**開發人員**和**進階**層可支援此設定)，客戶就可以指定要作為預設憑證的憑證。 若要設定預設憑證，請將 [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) 屬性設定為 true ("defaultSslBinding":"true")。 如果客戶未設定此屬性，預設憑證就是發給 *.azure-api.net 上所裝載之預設 Proxy 網域的憑證。

## <a name="support-for-putpost-request-with-large-payload"></a>有大型承載之 PUT/POST 要求的支援

APIM Proxy 伺服器在 HTTPS 中使用用戶端憑證時，可支援有大型承載的要求 (例如，承載 > 40 KB)。 若要防止系統凍結伺服器的要求，客戶可以在 Proxy 主機名稱上設定 ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) 屬性。 如果將屬性設定為 true，系統就會在任何 HTTP 要求交換之前，於 SSL/TLS 連線時間要求用戶端憑證。 由於設定會在 **Proxy 主機名稱**層級套用，因此所有連線要求都會要求用戶端憑證。 客戶可以針對 Proxy 設定多達 20 個自訂網域 (**進階**層才會支援此設定) 而解決這項限制。

