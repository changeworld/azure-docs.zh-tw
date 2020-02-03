---
title: Azure 應用程式閘道設定總覽
description: 本文說明如何設定 Azure 應用程式閘道的元件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: absha
ms.openlocfilehash: 146dbdbf2f4e107e81515ce83188fa48c52aef36
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714866"
---
# <a name="application-gateway-configuration-overview"></a>應用程式閘道設定總覽

Azure 應用程式閘道是由數個元件所組成，您可以在不同的案例中以各種方式進行設定。 本文說明如何設定每個元件。

![應用程式閘道元件流程圖](./media/configuration-overview/configuration-overview1.png)

此圖說明具有三個接聽程式的應用程式。 前兩個分別是 `http://acme.com/*` 和 `http://fabrikam.com/*`的多網站接聽程式。 兩者都在埠80上接聽。 第三個是具有端對端安全通訊端層（SSL）終止的基本接聽程式。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虛擬網路和專用子網

應用程式閘道是您虛擬網路中的專用部署。 在您的虛擬網路中，應用程式閘道需要專用的子網。 在子網中，您可以有多個指定應用程式閘道部署的實例。 您也可以在子網中部署其他應用程式閘道。 但是，您無法在應用程式閘道子網中部署任何其他資源。

> [!NOTE]
> 您無法在相同的子網上混用 Standard_v2 和標準 Azure 應用程式閘道。

#### <a name="size-of-the-subnet"></a>子網路的大小

應用程式閘道會針對每個實例取用1個私人 IP 位址，再加上另一個私人 IP 位址（如果已設定私人前端 IP）。

Azure 也會在每個子網中保留5個 IP 位址供內部使用：前4個和最後一個 IP 位址。 例如，請考慮15個沒有私人前端 IP 的應用程式閘道實例。 此子網需要至少20個 IP 位址：5供內部使用，而15個用於應用程式閘道實例。 因此，您需要/27 子網大小或更大。

請考慮有27個應用程式閘道實例的子網，以及私人前端 IP 的 IP 位址。 在此情況下，您需要33個 IP 位址：27個用於應用程式閘道實例，1個用於私人前端，而5個供內部使用。 因此，您需要/26 個子網大小或更大。

我們建議使用至少為/28 的子網大小。 此大小會提供您11個可用的 IP 位址。 如果您的應用程式負載需要10個以上的應用程式閘道實例，請考慮使用/27 或/26 子網大小。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>應用程式閘道子網上的網路安全性群組

應用程式閘道支援網路安全性群組（Nsg）。 但有一些限制：

- 您必須允許應用程式閘道 v1 SKU 的 TCP 埠65503-65534 上的連入網際網路流量，以及使用目的地子網做為**Any**和 Source as **GatewayManager**服務標籤的 v2 sku 的 tcp 埠65200-65535。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠會受到 Azure 憑證的保護（鎖定）。 外部實體（包括這些閘道的客戶）無法在這些端點上進行通訊。

- 無法封鎖輸出網際網路連線。 NSG 中的預設輸出規則允許網際網路連線能力。 建議您：

  - 請勿移除預設輸出規則。
  - 請勿建立會拒絕任何輸出連線能力的其他輸出規則。

- 必須允許來自**AzureLoadBalancer**標記的流量。

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>允許應用程式閘道存取一些來源 Ip

針對此案例，請在應用程式閘道子網上使用 Nsg。 依照優先順序，將下列限制放在子網上：

1. 允許來自來源 IP 或 IP 範圍的連入流量，並以目的地作為您的輸入存取埠（例如，用於 HTTP 存取的埠80）做為整個應用程式閘道子網位址範圍和目的地埠。
2. 允許來自來源的傳入要求做為**GatewayManager**服務標籤和目的地，做為應用程式閘道 v1 SKU 的**任何**和目的地埠（65503-65534），以及 v2 sku 的埠65200-65535 （適用于[後端健康狀態通訊](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)）。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠會受到 Azure 憑證的保護（鎖定）。 若沒有適當的憑證，外部實體就無法在這些端點上起始變更。
3. 允許[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)上的傳入 Azure Load Balancer 探查（*AzureLoadBalancer*標記）和輸入虛擬網路流量（*VirtualNetwork*標記）。
4. 使用「全部拒絕」規則封鎖所有其他連入流量。
5. 允許所有目的地對網際網路的輸出流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網上支援的使用者定義路由

就 v1 SKU 而言，應用程式閘道子網支援使用者定義的路由（Udr），但前提是它們不會改變端對端要求/回應通訊。 例如，您可以在應用程式閘道子網中設定 UDR，以指向防火牆設備以進行封包檢查。 但是您必須確定封包在檢查之後可以到達其預定目的地。 如果無法這樣做，可能會導致健康情況探查或流量路由行為不正確。 這包括學習到的路由，或虛擬網路中的 Azure ExpressRoute 或 VPN 閘道所傳播的預設 0.0.0.0/0 路由。

針對 v2 SKU，應用程式閘道子網不支援 Udr。 如需詳細資訊，請參閱[Azure 應用程式 Gateway V2 SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku)。

> [!NOTE]
> 目前的 v2 SKU 不支援 Udr。

> [!NOTE]
> 在應用程式閘道子網上使用 Udr 可能會導致[後端健康](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)情況中的健康狀態顯示為「未知」。 它也可能會產生應用程式閘道記錄和計量的產生失敗。 我們建議您不要在應用程式閘道子網上使用 Udr，以便您可以查看後端健康情況、記錄和計量。

## <a name="front-end-ip"></a>前端 IP

您可以將應用程式閘道設定為具有公用 IP 位址、私人 IP 位址或兩者。 當您裝載的後端必須透過網際網路對向的虛擬 IP （VIP）來存取用戶端時，就需要公用 IP。 

不會對網際網路公開的內部端點，不需要公用 IP。 這稱為*內部負載平衡器*（ILB）端點或私人前端 IP。 應用程式閘道 ILB 適用于不會向網際網路公開的內部企業營運系統應用程式。 對於不會公開至網際網路，但需要迴圈配置資源負載分配、會話粘性或 SSL 終止的安全性界限內的服務和層級，它也很有用。

僅支援1個公用 IP 位址或1個私人 IP 位址。 當您建立應用程式閘道時，請選擇前端 IP。

- 針對公用 IP，您可以建立新的公用 IP 位址，或使用與應用程式閘道位於相同位置的現有公用 IP。 如需詳細資訊，請參閱[靜態與動態公用 IP 位址](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)。

- 若是私人 IP，您可以從建立應用程式閘道的子網指定私人 IP 位址。 如果您未指定，則會自動從子網中選取任意 IP 位址。 您選取的 IP 位址類型（靜態或動態）稍後無法變更。 如需詳細資訊，請參閱[建立具有內部負載平衡器的應用程式閘道](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

前端 IP 位址會與接聽程式相關*聯，以*檢查前端 ip 上的傳入要求。

## <a name="listeners"></a>Listeners

接聽程式是一個邏輯實體，會使用埠、通訊協定、主機和 IP 位址來檢查傳入的連線要求。 當您設定接聽程式時，必須輸入這些值，以符合閘道上傳入要求中的對應值。

當您使用 Azure 入口網站建立應用程式閘道時，也會藉由選擇接聽項的通訊協定和埠來建立預設接聽項。 您可以選擇是否要在接聽程式上啟用 HTTP2 支援。 建立應用程式閘道之後，您可以編輯該預設接聽項的設定（*appGatewayHttpListener*），或建立新的接聽程式。

### <a name="listener-type"></a>接聽程式類型

當您建立新的接聽程式時，您可以選擇 [ [*基本*] 和 [*多網站*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)]。

- 如果您想要接受所有要求（適用于任何網域）並轉送至後端集區，請選擇 [基本]。 瞭解[如何建立具有基本接聽程式的應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果您想要根據*主機*標頭或主機名稱，將要求轉送至不同的後端集區，請選擇 [多網站接聽程式]，您也必須指定符合連入要求的主機名稱。 這是因為應用程式閘道依賴 HTTP 1.1 主機標頭，在同一個公用 IP 位址和埠上裝載多個網站。

#### <a name="order-of-processing-listeners"></a>處理接聽程式的順序

對於 v1 SKU，要求會根據規則的順序和接聽程式的類型來進行比對。 如果具有基本接聽程式的規則第一次出現在順序中，則會先處理它，而且會接受該埠和 IP 組合的任何要求。 若要避免這個情況，請先設定具有多網站接聽程式的規則，並將具有基本接聽程式的規則推送至清單中的最後一個。

針對 v2 SKU，多網站接聽程式會在基本接聽程式之前處理。

### <a name="front-end-ip"></a>前端 IP

選擇您計畫要與此接聽程式產生關聯的前端 IP 位址。 接聽程式會接聽此 IP 上的傳入要求。

### <a name="front-end-port"></a>前端埠

選擇前端埠。 選取現有的埠，或建立一個新的。 選擇[允許的埠範圍](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)中的任何值。 您不只可以使用已知的埠，例如80和443，而是適用的任何允許的自訂埠。 埠可用於對外公開的接聽程式或私用的接聽程式。

### <a name="protocol"></a>通訊協定

選擇 [HTTP] 或 [HTTPS]：

- 如果您選擇 HTTP，則會加密用戶端與應用程式閘道之間的流量。

- 如果您想要[ssl 終止](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination)或[端對端 ssl 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)，請選擇 [HTTPS]。 用戶端和應用程式閘道之間的流量會經過加密。 而且 SSL 連線會在應用程式閘道上終止。 如果您想要端對端 SSL 加密，您必須選擇 [HTTPS] 並設定**後端 HTTP**設定。 這可確保在從應用程式閘道傳送至後端時，會重新加密流量。

若要設定 SSL 終止和端對端 SSL 加密，您必須將憑證新增至接聽程式，讓應用程式閘道可以衍生對稱金鑰。 這取決於 SSL 通訊協定規格。 對稱金鑰是用來加密和解密傳送至閘道的流量。 閘道憑證必須採用「個人資訊交換」（PFX）格式。 此格式可讓您匯出閘道用來加密和解密流量的私密金鑰。

#### <a name="supported-certificates"></a>支援的憑證

請參閱[支援 SSL 終止的憑證](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>其他通訊協定支援

#### <a name="http2-support"></a>HTTP2 支援

HTTP/2 通訊協定支援僅供連接至應用程式閘道接聽程式的用戶端使用。 與後端伺服器集區的通訊是透過 HTTP/1.1。 預設已停用 HTTP/2 支援。 下列 Azure PowerShell 程式碼片段顯示如何啟用此動作：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 支援

預設會啟用 WebSocket 支援。 沒有使用者可設定的設定，無法啟用或停用它。 您可以搭配 HTTP 和 HTTPS 接聽程式使用 Websocket。

### <a name="custom-error-pages"></a>自訂錯誤頁面

您可以在全域層級或接聽程式層級定義自訂錯誤。 但目前不支援從 Azure 入口網站建立全域層級的自訂錯誤頁面。 您可以在接聽程式層級設定 403 web 應用程式防火牆錯誤或502維護頁面的自訂錯誤頁面。 您也必須為給定的錯誤狀態碼指定可公開存取的 blob URL。 如需詳細資訊，請參閱[建立應用程式閘道的自訂錯誤頁面](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![應用程式閘道錯誤碼](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要設定全域自訂錯誤頁面，請參閱[Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)設定。

### <a name="ssl-policy"></a>SSL 原則

您可以集中管理 SSL 憑證，並減少後端伺服器陣列的加密解密額外負荷。 集中式 SSL 處理也可讓您指定適合您安全性需求的中央 SSL 原則。 您可以選擇 [*預設*]、[*預先定義*] 或 [*自訂*SSL 原則]。

您可以設定 SSL 原則來控制 SSL 通訊協定版本。 您可以設定應用程式閘道使用最低通訊協定版本，從 TLS 1.0、TLS 1.1 和 TLS 1.2 進行 TLS 交握。 預設會停用 SSL 2.0 和3.0，且無法設定。 如需詳細資訊，請參閱[應用程式閘道 SSL 原則總覽](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

建立接聽程式之後，您可以將它與要求路由規則產生關聯。 該規則會決定如何將接聽程式收到的要求路由傳送至後端。

## <a name="request-routing-rules"></a>要求路由規則

當您使用 Azure 入口網站建立應用程式閘道時，會建立預設規則（*rule1*）。 此規則會將預設的接聽程式（*appGatewayHttpListener*）與預設後端集區（*appGatewayBackendPool*）和預設的後端 HTTP 設定（*appGatewayBackendHttpSettings*）系結。 建立閘道之後，您可以編輯預設規則的設定，或建立新的規則。

### <a name="rule-type"></a>規則類型

當您建立規則時，您可以選擇 [ [*基本*] 和 [*路徑型*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)]。

- 如果您想要將相關聯接聽程式上的所有要求（例如，  *<i> </i>contoso.com/\*）* 轉送到單一後端集區，請選擇 [基本]。
- 如果您想要將來自特定 URL 路徑的要求路由至特定後端集區，請選擇路徑型。 路徑模式只會套用至 URL 的路徑，而不會套用至其查詢參數。

#### <a name="order-of-processing-rules"></a>處理規則的順序

就 v1 SKU 而言，傳入要求的模式比對是以路徑為基礎之規則的 URL 路徑對應中列出路徑的順序來處理。 如果要求與路徑對應中的兩個或多個路徑中的模式相符，則會比對第一個列出的路徑。 然後將要求轉送至與該路徑相關聯的後端。

針對 v2 SKU，完全符合的優先順序高於 URL 路徑對應中的路徑順序。 如果要求符合兩個或多個路徑中的模式，則會將要求轉送至與完全符合要求的路徑相關聯的後端。 如果傳入要求中的路徑與對應中的任何路徑不完全相符，則會在路徑型規則的路徑對應順序清單中處理要求的模式比對。

### <a name="associated-listener"></a>相關聯的接聽程式

將接聽程式與規則產生關聯，以評估與接聽程式相關聯的*要求路由規則*，以判斷要將要求路由至其中的後端集區。

### <a name="associated-back-end-pool"></a>相關聯的後端集區

將包含後端目標的後端集區，與服務接聽程式接收的要求提供給規則的關聯。

 - 針對基本規則，只允許一個後端集區。 相關聯接聽程式上的所有要求都會轉送到該後端集區。

 - 如需以路徑為基礎的規則，請新增多個對應至每個 URL 路徑的後端集區。 符合所輸入 URL 路徑的要求會轉送到對應的後端集區。 此外，新增預設後端集區。 不符合規則中任何 URL 路徑的要求會轉送到該集區。

### <a name="associated-back-end-http-setting"></a>相關聯的後端 HTTP 設定

為每個規則新增後端 HTTP 設定。 系統會使用此設定中指定的埠號碼、通訊協定和其他資訊，從應用程式閘道將要求路由傳送至後端目標。

針對基本規則，只允許一個後端 HTTP 設定。 相關聯接聽程式上的所有要求都會使用此 HTTP 設定轉送到對應的後端目標。

如需以路徑為基礎的規則，請新增多個與每個 URL 路徑對應的後端 HTTP 設定。 符合此設定中 URL 路徑的要求，會使用對應至每個 URL 路徑的 HTTP 設定轉送到對應的後端目標。 此外，新增預設的 HTTP 設定。 如果要求不符合此規則中的任何 URL 路徑，則會使用預設 HTTP 設定來轉送到預設的後端集區。

### <a name="redirection-setting"></a>重新導向設定

如果已針對基本規則設定重新導向，則相關聯接聽程式上的所有要求都會重新導向至目標。 這是*全域*重新導向。 如果已針對以路徑為基礎的規則設定重新導向，則只會重新導向特定網站區域中的要求。 例如， */cart/\** 表示的購物車區域。 這是以*路徑為基礎的重新導向*。

如需重新導向的詳細資訊，請參閱[應用程式閘道重新導向總覽](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

#### <a name="redirection-type"></a>重新導向類型

選擇所需的重新導向類型： [*永久（301）* ]、[*暫存（307）* ]、 *[找到（302）* ] 或 [*查看其他（303）* ]。

#### <a name="redirection-target"></a>重新導向目標

選擇另一個接聽程式或外部網站做為重新導向目標。

##### <a name="listener"></a>接聽程式

選擇 [接聽程式] 做為重新導向目標，將流量從一個接聽程式重新導向至閘道上的另一個接聽程式 當您想要啟用 HTTP 對 HTTPS 重新導向時，這是必要的設定。 它會將來源接聽程式的流量，從檢查傳入的 HTTP 要求，重新導向至檢查傳入 HTTPS 要求的目的地接聽程式。 您也可以選擇在轉送至重新導向目標的要求中包含查詢字串和來自原始要求的路徑。

![應用程式閘道元件 對話方塊](./media/configuration-overview/configure-redirection.png)

如需有關 HTTP 對 HTTPS 重新導向的詳細資訊，請參閱：
- [使用 Azure 入口網站的 HTTP 對 HTTPS 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [使用 PowerShell 的 HTTP 對 HTTPS 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [使用 Azure CLI 的 HTTP 對 HTTPS 重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>外部網站

當您想要將與此規則相關聯之接聽程式上的流量重新導向至外部網站時，請選擇 [外部網站]。 您可以從轉送至重新導向目標的要求中，選擇包含原始要求中的查詢字串。 您無法將路徑轉送到原始要求中的外部網站。

如需重新導向的詳細資訊，請參閱：
- [使用 PowerShell 將流量重新導向至外部網站](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [使用 CLI 將流量重新導向至外部網站](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>重寫 HTTP 標頭設定

此設定會在要求和回應封包于用戶端與後端集區之間移動時，新增、移除或更新 HTTP 要求和回應標頭。 如需詳細資訊，請參閱

 - [重寫 HTTP 標頭總覽](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [設定 HTTP 標頭重寫](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)

## <a name="http-settings"></a>HTTP 設定

應用程式閘道會使用您在此處指定的設定，將流量路由傳送至後端伺服器。 建立 HTTP 設定之後，您必須將它與一或多個要求路由規則產生關聯。

### <a name="cookie-based-affinity"></a>以 Cookie 為基礎的同質性

當您想要在同一部伺服器上保留使用者會話時，這項功能很有用。 閘道管理的 cookie 可讓應用程式閘道將使用者會話的後續流量導向至同一部伺服器進行處理。 當會話狀態儲存在伺服器本機上供使用者會話使用時，這點很重要。 如果應用程式無法處理以 cookie 為基礎的親和性，您就無法使用這項功能。 若要使用它，請確定用戶端支援 cookie。

### <a name="connection-draining"></a>清空連線

清空連線可協助您在規劃的服務更新期間，正常地移除後端集區成員。 您可以在建立規則時，將此設定套用至後端集區的所有成員。 它可確保後端集區的所有取消註冊實例繼續維持現有的連線，並提供可設定的超時時間的要求，而且不會收到任何新的要求或連接。 唯一的例外是因為閘道管理的會話親和性，而系結至取消註冊實例的要求，而且會繼續轉送到取消註冊的實例。 清空連接會套用至從後端集區明確移除的後端實例。

### <a name="protocol"></a>通訊協定

應用程式閘道支援 HTTP 和 HTTPS，以將要求路由傳送至後端伺服器。 如果您選擇 HTTP，則會加密對後端伺服器的流量。 如果無法接受未加密的通訊，請選擇 [HTTPS]。

這項設定與接聽程式中的 HTTPS 結合，[可支援端對端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 這可讓您安全地將加密的機密資料傳輸至後端。 後端集區中已啟用端對端 SSL 的每部後端伺服器，都必須使用憑證來設定，以允許安全通訊。

### <a name="port"></a>連接埠

此設定會指定後端伺服器用來接聽來自應用程式閘道之流量的埠。 您可以設定範圍從1到65535的埠。

### <a name="request-timeout"></a>要求逾時

此設定是應用程式閘道等候從後端伺服器接收回應的秒數。

### <a name="override-back-end-path"></a>覆寫後端路徑

此設定可讓您設定選擇性的自訂轉送路徑，以在要求轉送至後端時使用。 傳入路徑中符合 [覆**寫後端路徑**] 欄位中自訂路徑的任何部分，都會複製到轉送的路徑。 下表顯示這項功能的運作方式：

- 當 HTTP 設定附加至基本要求路由規則時：

  | 原始要求  | 覆寫後端路徑 | 已將要求轉送到後端 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | 覆寫            | /override/home/              |
  | /home/secondhome/ | 覆寫            | /override/home/secondhome/   |

- 當 HTTP 設定附加至以路徑為基礎的要求路由規則時：

  | 原始要求           | 路徑規則       | 覆寫後端路徑 | 已將要求轉送到後端 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | 覆寫            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | 覆寫            | /override/home/secondhome/   |
  | /home/                     | pathrule      | 覆寫            | /override/home/              |
  | /home/secondhome/          | pathrule      | 覆寫            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | 覆寫            | 覆寫                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | 覆寫            | /override/secondhome/        |
  | pathrule                 | pathrule      | 覆寫            | 覆寫                   |

### <a name="use-for-app-service"></a>針對 app service 使用

這是僅限 UI 的快捷方式，可選取 Azure App Service 後端所需的兩個設定。 它會啟用**從後端位址挑選主機名稱**，如果您還沒有自訂探查，則會建立新的。 （如需詳細資訊，請參閱本文的[從後端位址設定挑選主機名稱](#pick)一節）。隨即會建立新的探查，並從後端成員的位址中挑選探查標頭。

### <a name="use-custom-probe"></a>使用自訂探查

此設定會將[自訂探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)與 HTTP 設定產生關聯。 您只能將一個自訂探查與 HTTP 設定產生關聯。 如果您未明確建立自訂探查的關聯，則會使用[預設探查](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)來監視後端的健全狀況。 我們建議您建立自訂探查，以便更充分掌控後端的健全狀況監視。

> [!NOTE]
> 自訂探查不會監視後端集區的健康情況，除非對應的 HTTP 設定已明確與接聽程式相關聯。

### <a id="pick"/></a>從後端位址挑選主機名稱

這項功能會將要求中的*主機*標頭動態設定為後端集區的主機名稱。 它會使用 IP 位址或 FQDN。

當後端的功能變數名稱與應用程式閘道的 DNS 名稱不同，而且後端依賴特定主機標頭來解析為正確的端點時，這項功能會有説明。

範例案例是多租使用者服務，做為後端。 App service 是一種多租使用者服務，它會使用具有單一 IP 位址的共用空間。 因此，應用程式服務只能透過自訂網域設定中設定的主機名稱來存取。

根據預設，自訂功能變數名稱為*example.azurewebsites.net*。 若要透過未在 app service 中明確註冊的主機名稱，或透過應用程式閘道的 FQDN，使用應用程式閘道來存取您的 app service，您可以將原始要求中的主機名稱覆寫至 app service 的主機名稱。 若要這麼做，請啟用 [**從後端位址挑選主機名稱**] 設定。

針對現有自訂 DNS 名稱對應至 app service 的自訂網域，您不需要啟用此設定。

> [!NOTE]
> App Service 環境（這是專用部署）不需要此設定。

### <a name="host-name-override"></a>主機名稱覆寫

這項功能會以您指定的主機名稱取代應用程式閘道上的傳入要求中的*主機*標頭。

例如，如果在 [**主機名稱**] 設定中指定*www.contoso.com* ，則將要求轉寄至後端伺服器時，原始要求 * https://appgw.eastus.cloudapp.azure.com/path1 會變更為 * https://www.contoso.com/path1。

## <a name="back-end-pool"></a>後端集區

您可以將後端集區指向四種類型的後端成員：特定的虛擬機器、虛擬機器擴展集、IP 位址/FQDN 或應用程式服務。 每個後端集區都可以指向相同類型的多個成員。 不支援指向相同後端集區中不同類型的成員。

建立後端集區之後，您必須將它與一或多個要求路由規則產生關聯。 您也必須為應用程式閘道上的每個後端集區設定健康情況探查。 符合要求路由規則條件時，應用程式閘道會將流量轉送到對應後端集區中狀況良好的伺服器（由健康情況探查所決定）。

## <a name="health-probes"></a>健康狀態探查

應用程式閘道預設會監視其後端中所有資源的健全狀況。 但強烈建議您為每個後端 HTTP 設定建立自訂探查，以進一步掌控健全狀況監視。 若要瞭解如何設定自訂探查，請參閱[自訂健康情況探查設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)。

> [!NOTE]
> 建立自訂健康情況探查之後，您必須將它與後端 HTTP 設定產生關聯。 自訂探查不會監視後端集區的健康情況，除非對應的 HTTP 設定已明確與使用規則的接聽程式相關聯。

## <a name="next-steps"></a>後續步驟

現在您已瞭解應用程式閘道元件，您可以：

- [在 Azure 入口網站中建立應用程式閘道](quick-create-portal.md)
- [使用 PowerShell 建立應用程式閘道](quick-create-powershell.md)
- [使用 Azure CLI 建立應用程式閘道](quick-create-cli.md)
