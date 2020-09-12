---
title: Azure 應用程式閘道要求路由規則設定
description: 本文說明如何設定 Azure 應用程式閘道要求路由規則。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 20a665eefbb73f062f1f036e17b16da891a43eef
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652860"
---
# <a name="application-gateway-request-routing-rules"></a>應用程式閘道要求路由規則

當您使用 Azure 入口網站建立應用程式閘道時，您會 (*rule1*) 建立預設規則。 此規則會將預設的接聽程式 (*appGatewayHttpListener*) 與預設的後端集區系結 (*appGatewayBackendPool*) 和預設的後端 HTTP 設定 (*appGatewayBackendHttpSettings*) 。 建立閘道之後，您可以編輯預設規則的設定，或建立新的規則。

## <a name="rule-type"></a>規則型別

當您建立規則時，您可以選擇 [ [*基本* ] 和 [ *路徑型*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)]。

- 如果您想要將相關接聽程式上的所有要求轉寄 (例如， * <i></i> contoso.com/ \*) * 到單一後端集區，請選擇 [基本]。
- 如果您想要將來自特定 URL 路徑的要求路由至特定的後端集區，請選擇 [以路徑為基礎]。 路徑模式只會套用至 URL 的路徑，而不會套用到其查詢參數。

### <a name="order-of-processing-rules"></a>處理規則的順序

針對 v1 和 v2 SKU，傳入要求的模式比對會依照路徑在路徑型規則的 URL 路徑對應中列出的順序來處理。 如果要求符合路徑對應中兩個或多個路徑中的模式，則第一個列出的路徑會相符。 而且會將要求轉送到與該路徑相關聯的後端。

## <a name="associated-listener"></a>相關聯的接聽程式

將接聽程式與規則建立關聯，以便評估與接聽程式相關聯的 *要求路由規則* ，以判斷要將要求路由傳送至的後端集區。

## <a name="associated-back-end-pool"></a>相關聯的後端集區

將包含後端目標的後端集區與規則產生關聯，以提供接聽程式所接收的要求。

 - 若為基本規則，則只允許一個後端集區。 相關接聽程式上的所有要求都會轉送至該後端集區。

 - 針對以路徑為基礎的規則，新增多個對應至每個 URL 路徑的後端集區。 符合所輸入 URL 路徑的要求會轉送到對應的後端集區。 此外，新增預設的後端集區。 不符合規則中任何 URL 路徑的要求都會轉送到該集區。

## <a name="associated-back-end-http-setting"></a>相關聯的後端 HTTP 設定

新增每個規則的後端 HTTP 設定。 要求會使用此設定中指定的埠號碼、通訊協定和其他資訊，從應用程式閘道路由傳送至後端目標。

若為基本規則，則只允許一個後端 HTTP 設定。 相關接聽程式上的所有要求都會使用此 HTTP 設定轉送到對應的後端目標。

如果是以路徑為基礎的規則，請新增多個對應至每個 URL 路徑的後端 HTTP 設定。 與此設定中的 URL 路徑相符的要求，會使用對應至每個 URL 路徑的 HTTP 設定來轉送到對應的後端目標。 此外，也請新增預設 HTTP 設定。 不符合此規則中任何 URL 路徑的要求，會使用預設的 HTTP 設定轉送到預設的後端集區。

## <a name="redirection-setting"></a>重新導向設定

如果已針對基本規則設定重新導向，則會將相關聯接聽程式上的所有要求重新導向至目標。 這是 *全域* 重新導向。 如果已針對以路徑為基礎的規則設定重新導向，則只會重新導向特定網站區域中的要求。 例如，以 */cart/ \* *表示的購物車區域。 這是以 *路徑為基礎的重新導向* 。

如需重新導向的詳細資訊，請參閱 [應用程式閘道重新導向總覽](redirect-overview.md)。

### <a name="redirection-type"></a>重新導向類型

選擇所需的重新導向類型： *永久性 (301) *、 *暫時性 (307) *、 *找到 (302) *，或 *查看其他 (303) *。

### <a name="redirection-target"></a>重新導向目標

選擇另一個接聽程式或外部網站做為重新導向目標。

#### <a name="listener"></a>接聽程式

選擇接聽程式做為重新導向目標，以將流量從一個接聽程式重新導向至閘道上的另一個接聽程式。 當您想要啟用 HTTP 對 HTTPS 重新導向時，需要此設定。 它會將流量從檢查傳入 HTTP 要求的來源接聽程式重新導向至目的地接聽程式，以檢查傳入的 HTTPS 要求。 您也可以選擇在轉送至重新導向目標的要求中包含來自原始要求的查詢字串和路徑。

![應用程式閘道元件對話方塊](./media/configuration-overview/configure-redirection.png)

如需有關 HTTP 對 HTTPS 重新導向的詳細資訊，請參閱：
- [使用 Azure 入口網站的 HTTP 對 HTTPS 重新導向](redirect-http-to-https-portal.md)
- [使用 PowerShell 的 HTTP 對 HTTPS 重新導向](redirect-http-to-https-powershell.md)
- [使用 Azure CLI 的 HTTP 對 HTTPS 重新導向](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>外部網站

當您想要將與此規則相關聯之接聽程式上的流量重新導向至外部網站時，請選擇 [外部網站]。 您可以選擇在轉送至重新導向目標的要求中包含來自原始要求的查詢字串。 您無法將路徑轉送到原始要求中的外部網站。

如需重新導向的詳細資訊，請參閱：
- [使用 PowerShell 將流量重新導向至外部網站](redirect-external-site-powershell.md)
- [使用 CLI 將流量重新導向至外部網站](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>重寫 HTTP 標頭和 URL

藉由使用重寫規則，您可以新增、移除或更新 HTTP (S) 要求和回應標頭，以及 URL 路徑和查詢字串參數，因為要求和回應封包會透過應用程式閘道在用戶端和後端集區之間移動。

標頭和 URL 參數可以設定為靜態值或其他標頭和伺服器變數。 這有助於重要的使用案例，例如，將用戶端 IP 位址解壓縮、移除後端的機密資訊、增加更多安全性等等。
如需詳細資訊，請參閱

 - [重寫 HTTP 標頭和 URL 總覽](rewrite-http-headers-url.md)
 - [設定 HTTP 標頭重寫](rewrite-http-headers-portal.md)
 - [設定 URL 重寫](rewrite-url-portal.md)

## <a name="next-steps"></a>接下來的步驟

- [深入瞭解 HTTP 設定](configuration-http-settings.md)