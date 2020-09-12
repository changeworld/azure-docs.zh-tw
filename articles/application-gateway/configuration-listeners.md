---
title: Azure 應用程式閘道接聽程式設定
description: 本文說明如何設定 Azure 應用程式閘道接聽程式。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: ef2ff8924cd8a92c5d2d2e5dd9da6bb74fad1a14
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652864"
---
# <a name="application-gateway-listener-configuration"></a>應用程式閘道接聽程式設定

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

接聽程式是一種邏輯實體，會使用埠、通訊協定、主機和 IP 位址來檢查連入連線要求。 當您設定接聽程式時，必須輸入符合閘道傳入要求中對應值的值。

當您使用 Azure 入口網站建立應用程式閘道時，您也可以選擇接聽程式的通訊協定和埠，以建立預設的接聽程式。 您可以選擇是否要啟用接聽程式的 HTTP2 支援。 建立應用程式閘道之後，您可以編輯該預設接聽項的設定 (*appGatewayHttpListener*) 或建立新的接聽程式。

## <a name="listener-type"></a>接聽程式類型

當您建立新的接聽程式時，可以選擇 [ [*基本* ] 和 [ *多網站*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)]。

- 如果您想要將所有的要求 (給任何) 接受並轉送至後端集區的網域，請選擇 [基本]。 瞭解 [如何使用基本接聽程式來建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果您想要根據 *主機* 標頭或主機名稱將要求轉寄至不同的後端集區，請選擇 [多網站接聽程式]，您也必須指定符合連入要求的主機名稱。 這是因為應用程式閘道依賴 HTTP 1.1 主機標頭，在同一個公用 IP 位址和埠上裝載多個網站。 若要深入瞭解，請參閱 [使用應用程式閘道裝載多個網站](multiple-site-overview.md)。

### <a name="order-of-processing-listeners"></a>處理接聽程式的順序

針對 v1 SKU，要求會根據規則的順序以及接聽程式的類型來進行比對。 如果具有基本接聽程式的規則最先出現在順序中，則會先進行處理，並接受該埠和 IP 組合的任何要求。 若要避免這種情況，請先設定具有多網站接聽程式的規則，並將具有基本接聽程式的規則推送至清單中的最後一個。

若為 v2 SKU，則會在基本接聽程式之前處理多網站接聽程式。

## <a name="front-end-ip-address"></a>前端 IP 位址

選擇您要與此接聽程式建立關聯的前端 IP 位址。 接聽程式會接聽此 IP 上的傳入要求。

## <a name="front-end-port"></a>前端埠

選擇前端埠。 選取現有的埠，或建立一個新的埠。 從 [允許的埠範圍](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)中選擇任何值。 您不僅可以使用已知的埠，例如80和443，也可以使用任何允許的自訂埠。 埠可用於公開接聽程式或私用接聽程式。

## <a name="protocol"></a>通訊協定

選擇 HTTP 或 HTTPS：

- 如果您選擇 HTTP，用戶端與應用程式閘道之間的流量不會加密。

- 如果您想要 [tls 終止](features.md#secure-sockets-layer-ssltls-termination) 或 [端對端 TLS 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)，請選擇 [HTTPS]。 用戶端與應用程式閘道之間的流量會經過加密。 而且 TLS 連接會在應用程式閘道上終止。 如果您想要端對端 TLS 加密，您必須選擇 HTTPS 並設定 **後端 HTTP** 設定。 這可確保流量會在從應用程式閘道傳輸到後端時重新加密。


若要設定 TLS 終止和端對端 TLS 加密，您必須將憑證新增至接聽程式，以便讓應用程式閘道衍生對稱金鑰。 這取決於 TLS 通訊協定規格。 對稱金鑰是用來加密和解密傳送至閘道的流量。 閘道憑證必須是個人資訊交換 (PFX) 格式。 此格式可讓您匯出閘道用來加密和解密流量的私密金鑰。

## <a name="supported-certificates"></a>支援的憑證

請參閱 [使用應用程式閘道的 tls 終止和端對端 Tls 總覽](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>其他通訊協定支援

### <a name="http2-support"></a>HTTP2 支援

HTTP/2 通訊協定支援僅適用于連線到應用程式閘道接聽程式的用戶端。 與後端伺服器集區的通訊是透過 HTTP/1.1。 預設已停用 HTTP/2 支援。 下列 Azure PowerShell 程式碼片段示範如何啟用：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket 支援

預設會啟用 WebSocket 支援。 沒有使用者可設定的設定，無法啟用或停用它。 您可以搭配 HTTP 和 HTTPS 接聽程式使用 Websocket。

## <a name="custom-error-pages"></a>自訂錯誤頁面

您可以在全域層級或接聽程式層級定義自訂錯誤。 但目前不支援從 Azure 入口網站建立全域層級的自訂錯誤頁面。 您可以在接聽程式層級設定 403 web 應用程式防火牆錯誤或502維護頁面的自訂錯誤頁面。 您也必須為指定的錯誤狀態碼指定可公開存取的 blob URL。 如需詳細資訊，請參閱[建立應用程式閘道的自訂錯誤頁面](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![應用程式閘道錯誤碼](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要設定全域自訂錯誤頁面，請參閱 [Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)設定。

## <a name="tls-policy"></a>TLS 原則

您可以集中化 TLS/SSL 憑證管理，並降低後端伺服器陣列的加密解密負擔。 集中式 TLS 處理也可讓您指定適用于安全性需求的中央 TLS 原則。 您可以選擇 *預設*、 *預先定義*或 *自訂* 的 TLS 原則。

您可以設定 TLS 原則來控制 TLS 通訊協定版本。 您可以將應用程式閘道設定為使用 tls 1.0、TLS 1.1 和 TLS 1.2 的 TLS 交握的最低通訊協定版本。 預設會停用 SSL 2.0 和3.0，而且無法設定。 如需詳細資訊，請參閱 [應用程式閘道 TLS 原則總覽](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

建立接聽程式之後，您可以將它與要求路由規則產生關聯。 該規則會決定如何將接聽程式接收到的要求路由至後端。

## <a name="next-steps"></a>接下來的步驟

- [瞭解要求路由規則](configuration-request-routing-rules.md)。