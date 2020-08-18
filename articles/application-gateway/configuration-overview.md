---
title: Azure 應用程式閘道設定總覽
description: 本文說明如何設定 Azure 應用程式閘道的元件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: absha
ms.openlocfilehash: 32809c33e1c365d8d333bb89a5c2f773b311c2ff
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511077"
---
# <a name="application-gateway-configuration-overview"></a>應用程式閘道設定總覽

Azure 應用程式閘道包含數個元件，您可以在不同的案例中以各種方式設定這些元件。 本文說明如何設定每個元件。

![應用程式閘道元件流程圖](./media/configuration-overview/configuration-overview1.png)

此圖說明有三個接聽程式的應用程式。 前兩個分別是和的多網站接聽程式 `http://acme.com/*` `http://fabrikam.com/*` 。 兩者都在埠80上接聽。 第三個是具有端對端傳輸層安全性 (TLS) 終止的基本接聽程式，先前稱為安全通訊端層 (SSL) 終止。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虛擬網路和專用子網

應用程式閘道是您虛擬網路中的專用部署。 在您的虛擬網路中，應用程式閘道需要專用子網。 子網中可以有指定應用程式閘道部署的多個實例。 您也可以在子網中部署其他應用程式閘道。 但您無法在應用程式閘道子網中部署任何其他資源。

> [!NOTE]
> 您無法在相同的子網上混合 Standard_v2 和標準 Azure 應用程式閘道。

#### <a name="size-of-the-subnet"></a>子網路的大小

應用程式閘道會針對每個實例使用一個私人 IP 位址，再加上另一個私人 IP 位址（若已設定私人前端 IP 位址）。

Azure 也會在每個子網中保留5個 IP 位址供內部使用：前四個和最後一個 IP 位址。 例如，請考慮15個沒有私人前端 IP 的應用程式閘道實例。 此子網至少需要20個 IP 位址：五個供內部使用，而15個用於應用程式閘道實例。

請考慮具有27個應用程式閘道實例的子網，以及私人前端 IP 的 IP 位址。 在此情況下，您需要33個 IP 位址：27個用於應用程式閘道實例，一個用於私人前端，而五個供內部使用。

 (Standard 或 WAF) SKU 的應用程式閘道最多可支援32個實例 (32 實例 IP 位址 + 1 個私人前端 IP + 5 個 Azure 保留的) –因此建議的子網大小下限為/26

應用程式閘道 (Standard_v2 或 WAF_v2 SKU) 最多可支援125個實例 (125 個實例 IP 位址 + 1 個私人前端 IP + 5 個 Azure 保留) –因此建議的子網大小下限為/24

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>應用程式閘道子網上的網路安全性群組

應用程式閘道上支援 (Nsg) 的網路安全性群組。 但有一些限制：

- 針對應用程式閘道 v1 SKU 的 TCP 通訊埠 65503-65534，以及 v2 SKU 的 TCP 通訊埠 65200-65535，您必須允許目的地子網路為 **Any** 且來源為 **GatewayManager** 服務標記的連入網際網路流量。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受到保護， (Azure 憑證) 鎖定。 外部實體（包括這些閘道的客戶）無法在這些端點上進行通訊。

- 無法封鎖輸出網際網路連線。 NSG 中的預設輸出規則允許網際網路連線能力。 建議您：

  - 請勿移除預設輸出規則。
  - 請勿建立拒絕任何輸出連線能力的其他輸出規則。

- 必須允許來自 **AzureLoadBalancer** **標記與目的地** 子網的流量。

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>允許應用程式閘道存取一些來源 Ip

針對此案例，請在應用程式閘道子網上使用 Nsg。 依優先順序將下列限制放在子網上：

1. 允許來自來源 IP 或 IP 範圍的連入流量，以目的地為整個應用程式閘道子網位址範圍和目的地埠作為您的輸入存取埠，例如 HTTP 存取的埠80。
2. 允許來自來源作為 **GatewayManager** 服務標籤和目的地的連入要求，作為應用程式閘道 v1 SKU 的 **任何** 和目的地埠（65503-65534），以及用於 [後端健康狀態通訊](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)的 v2 SKU 埠65200-65535。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受到保護， (Azure 憑證) 鎖定。 若沒有適當的憑證，外部實體就無法在這些端點上起始變更。
3. 允許傳入的 Azure Load Balancer 探查 (*AzureLoadBalancer*標籤) 以及[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)上 (*VirtualNetwork*標籤) 的輸入虛擬網路流量。
4. 使用全部拒絕規則來封鎖所有其他連入流量。
5. 允許所有目的地的網際網路輸出流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網路上支援的使用者定義路由

> [!IMPORTANT]
> 在應用程式閘道子網上使用 Udr 可能會導致 [後端健康狀態視圖](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) 中的健全狀況狀態顯示為 [ **不明**]。 它也可能會造成應用程式閘道記錄和計量的產生失敗。 建議您不要在應用程式閘道子網上使用 Udr，如此您就可以查看後端健康情況、記錄和計量。

- **v1**

   在 v1 SKU 中，應用程式閘道子網 (Udr) 支援使用者定義的路由，只要它們不會改變端對端要求/回應通訊。 例如，您可以在應用程式閘道子網中設定 UDR，以指向防火牆設備以進行封包檢查。 但是，您必須確定封包在檢查之後可以到達其預定目的地。 如果無法這麼做，可能會導致健康情況探查或流量路由行為不正確。 這包括學習到的路由，或虛擬網路中的 Azure ExpressRoute 或 VPN 閘道所傳播的預設 0.0.0.0/0 路由。 必須將 0.0.0.0/0 重新導向至內部部署 (強制通道) 不支援 v1 的任何情況。

- **v2**

   針對 v2 SKU，有支援和不支援的案例：

   **v2 支援的案例**
   > [!WARNING]
   > 路由表的設定不正確可能會導致應用程式閘道 v2 中的非對稱路由。 確定所有管理/控制平面流量都會直接傳送到網際網路，而不是透過虛擬裝置。 記錄和計量也可能受到影響。


  **案例 1**： UDR 停用邊界閘道協定 (BGP) 將傳播傳送至應用程式閘道子網

   有時候預設閘道路由 (0.0.0.0/0) 是透過與應用程式閘道虛擬網路相關聯的 ExpressRoute 或 VPN 閘道來通告。 這會中斷管理平面流量，這需要網際網路的直接路徑。 在這種情況下，UDR 可用來停用 BGP 路由傳播。 

   若要停用 BGP 路由傳播，請使用下列步驟：

   1. 在 Azure 中建立路由表資源。
   2. 停用 **虛擬網路閘道路由傳播** 參數。 
   3. 將路由表與適當的子網產生關聯。 

   啟用此案例的 UDR 應該不會中斷任何現有的設置。

  **案例 2**： UDR 至將 0.0.0.0/0 導向至網際網路

   您可以建立 UDR，將 0.0.0.0/0 流量直接傳送到網際網路。 

  **案例 3**：使用 kubenet Azure Kubernetes Service 的 UDR

  如果您使用 kubenet 搭配 Azure Kubernetes Service (AKS) 和應用程式閘道輸入控制器 (AGIC) ，您將需要路由表，以允許從應用程式閘道傳送至 pod 的流量路由傳送至正確的節點。 如果您使用 Azure CNI，就不需要這麼做。 

  若要使用路由表來允許 kubenet 運作，請依照下列步驟執行：

  1. 移至 AKS 所建立的資源群組 (資源群組的名稱應該以 "MC_" 開頭 ) 
  2. 在該資源群組中尋找 AKS 所建立的路由表。 路由表應填入下列資訊：
     - 位址首碼應該是您想要在 AKS 中到達之 pod 的 IP 範圍。 
     - 下一個躍點類型應該是虛擬裝置。 
     - 下一個躍點位址應該是裝載 pod 之節點的 IP 位址。
  3. 將此路由表與應用程式閘道子網建立關聯。 
    
  **v2 不支援的案例**

  **案例 1**：虛擬裝置的 UDR

  任何需要透過任何虛擬裝置、中樞/輪輻虛擬網路或內部部署來重新導向 0.0.0.0/0 的案例，都不支援 V2 的 (強制通道) 。

## <a name="front-end-ip"></a>前端 IP

您可以將應用程式閘道設定為具有公用 IP 位址、私人 IP 位址或兩者。 當您裝載後端，用戶端必須透過網際網路對向的虛擬 IP (VIP) 來透過網際網路存取時，就需要公用 IP。

> [!NOTE]
> 應用程式閘道 V2 目前不支援私人 IP 模式。 它支援下列組合：
>* 私人 IP 和公用 IP
>* 僅限公用 IP
>
> 如需詳細資訊，請參閱 [關於應用程式閘道](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)的常見問題。


未向網際網路公開的內部端點不需要公用 IP。 這就是所謂的 *內部負載平衡器* (ILB) 端點或私人前端 IP。 應用程式閘道 ILB 適用于不會向網際網路公開的內部企業營運系統應用程式。 它也適用于在不會對網際網路公開但需要迴圈配置資源負載散發、會話或 TLS 終止的安全性界限內的多層式應用程式中的服務和層級。

僅支援一個公用 IP 位址或一個私人 IP 位址。 當您建立應用程式閘道時，請選擇前端 IP。

- 針對公用 IP，您可以建立新的公用 IP 位址，或在與應用程式閘道相同的位置中使用現有的公用 IP。 如需詳細資訊，請參閱 [靜態與動態公用 IP 位址](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)。

- 若為私人 IP，您可以從建立應用程式閘道的子網中指定私人 IP 位址。 如果您未指定，則會自動從子網選取任意 IP 位址。 您選取 (靜態或動態) 的 IP 位址類型，稍後無法變更。 如需詳細資訊，請參閱 [使用內部負載平衡器建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

前端 IP 位址與接聽程式相關 *聯，它*會檢查前端 ip 上的傳入要求。

## <a name="listeners"></a>接聽程式

接聽程式是一種邏輯實體，會使用埠、通訊協定、主機和 IP 位址來檢查連入連線要求。 當您設定接聽程式時，必須輸入符合閘道傳入要求中對應值的值。

當您使用 Azure 入口網站建立應用程式閘道時，您也可以選擇接聽程式的通訊協定和埠，以建立預設的接聽程式。 您可以選擇是否要啟用接聽程式的 HTTP2 支援。 建立應用程式閘道之後，您可以編輯該預設接聽項的設定 (*appGatewayHttpListener*) 或建立新的接聽程式。

### <a name="listener-type"></a>接聽程式類型

當您建立新的接聽程式時，可以選擇 [ [*基本* ] 和 [ *多網站*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)]。

- 如果您想要將所有的要求 (給任何) 接受並轉送至後端集區的網域，請選擇 [基本]。 瞭解 [如何使用基本接聽程式來建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果您想要根據 *主機* 標頭或主機名稱將要求轉寄至不同的後端集區，請選擇 [多網站接聽程式]，您也必須指定符合連入要求的主機名稱。 這是因為應用程式閘道依賴 HTTP 1.1 主機標頭，在同一個公用 IP 位址和埠上裝載多個網站。 若要深入瞭解，請參閱 [使用應用程式閘道裝載多個網站](multiple-site-overview.md)。

#### <a name="order-of-processing-listeners"></a>處理接聽程式的順序

針對 v1 SKU，要求會根據規則的順序以及接聽程式的類型來進行比對。 如果具有基本接聽程式的規則最先出現在順序中，則會先進行處理，並接受該埠和 IP 組合的任何要求。 若要避免這種情況，請先設定具有多網站接聽程式的規則，並將具有基本接聽程式的規則推送至清單中的最後一個。

若為 v2 SKU，則會在基本接聽程式之前處理多網站接聽程式。

### <a name="front-end-ip"></a>前端 IP

選擇您要與此接聽程式建立關聯的前端 IP 位址。 接聽程式會接聽此 IP 上的傳入要求。

### <a name="front-end-port"></a>前端埠

選擇前端埠。 選取現有的埠，或建立一個新的埠。 從 [允許的埠範圍](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)中選擇任何值。 您不僅可以使用已知的埠，例如80和443，也可以使用任何允許的自訂埠。 埠可用於公開接聽程式或私用接聽程式。

### <a name="protocol"></a>通訊協定

選擇 HTTP 或 HTTPS：

- 如果您選擇 HTTP，用戶端與應用程式閘道之間的流量不會加密。

- 如果您想要 [tls 終止](features.md#secure-sockets-layer-ssltls-termination) 或 [端對端 TLS 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)，請選擇 [HTTPS]。 用戶端與應用程式閘道之間的流量會經過加密。 而且 TLS 連接會在應用程式閘道上終止。 如果您想要端對端 TLS 加密，您必須選擇 HTTPS 並設定 **後端 HTTP** 設定。 這可確保流量會在從應用程式閘道傳輸到後端時重新加密。


若要設定 TLS 終止和端對端 TLS 加密，您必須將憑證新增至接聽程式，以便讓應用程式閘道衍生對稱金鑰。 這取決於 TLS 通訊協定規格。 對稱金鑰是用來加密和解密傳送至閘道的流量。 閘道憑證必須是個人資訊交換 (PFX) 格式。 此格式可讓您匯出閘道用來加密和解密流量的私密金鑰。

#### <a name="supported-certificates"></a>支援的憑證

請參閱 [支援 TLS 終止的憑證](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>其他通訊協定支援

#### <a name="http2-support"></a>HTTP2 支援

HTTP/2 通訊協定支援僅適用于連線到應用程式閘道接聽程式的用戶端。 與後端伺服器集區的通訊是透過 HTTP/1.1。 預設已停用 HTTP/2 支援。 下列 Azure PowerShell 程式碼片段示範如何啟用：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 支援

預設會啟用 WebSocket 支援。 沒有使用者可設定的設定，無法啟用或停用它。 您可以搭配 HTTP 和 HTTPS 接聽程式使用 Websocket。

### <a name="custom-error-pages"></a>自訂錯誤頁面

您可以在全域層級或接聽程式層級定義自訂錯誤。 但目前不支援從 Azure 入口網站建立全域層級的自訂錯誤頁面。 您可以在接聽程式層級設定 403 web 應用程式防火牆錯誤或502維護頁面的自訂錯誤頁面。 您也必須為指定的錯誤狀態碼指定可公開存取的 blob URL。 如需詳細資訊，請參閱[建立應用程式閘道的自訂錯誤頁面](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![應用程式閘道錯誤碼](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要設定全域自訂錯誤頁面，請參閱 [Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)設定。

### <a name="tls-policy"></a>TLS 原則

您可以集中化 TLS/SSL 憑證管理，並降低後端伺服器陣列的加密解密負擔。 集中式 TLS 處理也可讓您指定適用于安全性需求的中央 TLS 原則。 您可以選擇 *預設*、 *預先定義*或 *自訂* 的 TLS 原則。

您可以設定 TLS 原則來控制 TLS 通訊協定版本。 您可以將應用程式閘道設定為使用 tls 1.0、TLS 1.1 和 TLS 1.2 的 TLS 交握的最低通訊協定版本。 預設會停用 SSL 2.0 和3.0，而且無法設定。 如需詳細資訊，請參閱 [應用程式閘道 TLS 原則總覽](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

建立接聽程式之後，您可以將它與要求路由規則產生關聯。 該規則會決定如何將接聽程式接收到的要求路由至後端。

## <a name="request-routing-rules"></a>要求路由規則

當您使用 Azure 入口網站建立應用程式閘道時，您會 (*rule1*) 建立預設規則。 此規則會將預設的接聽程式 (*appGatewayHttpListener*) 與預設的後端集區系結 (*appGatewayBackendPool*) 和預設的後端 HTTP 設定 (*appGatewayBackendHttpSettings*) 。 建立閘道之後，您可以編輯預設規則的設定，或建立新的規則。

### <a name="rule-type"></a>規則型別

當您建立規則時，您可以選擇 [ [*基本* ] 和 [ *路徑型*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)]。

- 如果您想要將相關接聽程式上的所有要求轉寄 (例如， * <i></i> contoso.com/ \*) * 到單一後端集區，請選擇 [基本]。
- 如果您想要將來自特定 URL 路徑的要求路由至特定的後端集區，請選擇 [以路徑為基礎]。 路徑模式只會套用至 URL 的路徑，而不會套用到其查詢參數。

#### <a name="order-of-processing-rules"></a>處理規則的順序

針對 v1 和 v2 SKU，傳入要求的模式比對會依照路徑在路徑型規則的 URL 路徑對應中列出的順序來處理。 如果要求符合路徑對應中兩個或多個路徑中的模式，則第一個列出的路徑會相符。 而且會將要求轉送到與該路徑相關聯的後端。

### <a name="associated-listener"></a>相關聯的接聽程式

將接聽程式與規則建立關聯，以便評估與接聽程式相關聯的 *要求路由規則* ，以判斷要將要求路由傳送至的後端集區。

### <a name="associated-back-end-pool"></a>相關聯的後端集區

將包含後端目標的後端集區與規則產生關聯，以提供接聽程式所接收的要求。

 - 若為基本規則，則只允許一個後端集區。 相關接聽程式上的所有要求都會轉送至該後端集區。

 - 針對以路徑為基礎的規則，新增多個對應至每個 URL 路徑的後端集區。 符合所輸入 URL 路徑的要求會轉送到對應的後端集區。 此外，新增預設的後端集區。 不符合規則中任何 URL 路徑的要求都會轉送到該集區。

### <a name="associated-back-end-http-setting"></a>相關聯的後端 HTTP 設定

新增每個規則的後端 HTTP 設定。 要求會使用此設定中指定的埠號碼、通訊協定和其他資訊，從應用程式閘道路由傳送至後端目標。

若為基本規則，則只允許一個後端 HTTP 設定。 相關接聽程式上的所有要求都會使用此 HTTP 設定轉送到對應的後端目標。

如果是以路徑為基礎的規則，請新增多個對應至每個 URL 路徑的後端 HTTP 設定。 與此設定中的 URL 路徑相符的要求，會使用對應至每個 URL 路徑的 HTTP 設定來轉送到對應的後端目標。 此外，也請新增預設 HTTP 設定。 不符合此規則中任何 URL 路徑的要求，會使用預設的 HTTP 設定轉送到預設的後端集區。

### <a name="redirection-setting"></a>重新導向設定

如果已針對基本規則設定重新導向，則會將相關聯接聽程式上的所有要求重新導向至目標。 這是 *全域* 重新導向。 如果已針對以路徑為基礎的規則設定重新導向，則只會重新導向特定網站區域中的要求。 例如，以 */cart/ \* *表示的購物車區域。 這是以 *路徑為基礎的重新導向* 。

如需重新導向的詳細資訊，請參閱 [應用程式閘道重新導向總覽](redirect-overview.md)。

#### <a name="redirection-type"></a>重新導向類型

選擇所需的重新導向類型： *永久性 (301) *、 *暫時性 (307) *、 *找到 (302) *，或 *查看其他 (303) *。

#### <a name="redirection-target"></a>重新導向目標

選擇另一個接聽程式或外部網站做為重新導向目標。

##### <a name="listener"></a>接聽程式

選擇接聽程式做為重新導向目標，以將流量從一個接聽程式重新導向至閘道上的另一個接聽程式。 當您想要啟用 HTTP 對 HTTPS 重新導向時，需要此設定。 它會將流量從檢查傳入 HTTP 要求的來源接聽程式重新導向至目的地接聽程式，以檢查傳入的 HTTPS 要求。 您也可以選擇在轉送至重新導向目標的要求中包含來自原始要求的查詢字串和路徑。

![應用程式閘道元件對話方塊](./media/configuration-overview/configure-redirection.png)

如需有關 HTTP 對 HTTPS 重新導向的詳細資訊，請參閱：
- [使用 Azure 入口網站的 HTTP 對 HTTPS 重新導向](redirect-http-to-https-portal.md)
- [使用 PowerShell 的 HTTP 對 HTTPS 重新導向](redirect-http-to-https-powershell.md)
- [使用 Azure CLI 的 HTTP 對 HTTPS 重新導向](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>外部網站

當您想要將與此規則相關聯之接聽程式上的流量重新導向至外部網站時，請選擇 [外部網站]。 您可以選擇在轉送至重新導向目標的要求中包含來自原始要求的查詢字串。 您無法將路徑轉送到原始要求中的外部網站。

如需重新導向的詳細資訊，請參閱：
- [使用 PowerShell 將流量重新導向至外部網站](redirect-external-site-powershell.md)
- [使用 CLI 將流量重新導向至外部網站](redirect-external-site-cli.md)

### <a name="rewrite-http-headers-and-url"></a>重寫 HTTP 標頭和 URL

藉由使用重寫規則，您可以新增、移除或更新 HTTP (S) 要求和回應標頭，以及 URL 路徑和查詢字串參數，因為要求和回應封包會透過應用程式閘道在用戶端和後端集區之間移動。

標頭和 URL 參數可以設定為靜態值或其他標頭和伺服器變數。 這有助於重要的使用案例，例如，將用戶端 IP 位址解壓縮、移除後端的機密資訊、增加更多安全性等等。
如需詳細資訊，請參閱

 - [重寫 HTTP 標頭和 URL 總覽](rewrite-http-headers-url.md)
 - [設定 HTTP 標頭重寫](rewrite-http-headers-portal.md)
 - [設定 URL 重寫](rewrite-url-portal.md)

## <a name="http-settings"></a>HTTP 設定

應用程式閘道會使用您在此處指定的設定，將流量路由傳送至後端伺服器。 建立 HTTP 設定之後，您必須將它與一或多個要求路由規則相關聯。

### <a name="cookie-based-affinity"></a>以 Cookie 為基礎的同質性

Azure 應用程式閘道會使用閘道管理的 cookie 來維護使用者會話。 當使用者將第一個要求傳送至應用程式閘道時，它會在回應中使用包含會話詳細資料的雜湊值來設定親和性 cookie，如此一來，包含相似性 cookie 的後續要求就會路由傳送至相同的後端伺服器，以維持一致。 

當您想要在相同的伺服器上保留使用者會話，以及將會話狀態儲存在伺服器本機上的使用者會話時，這項功能就很有用。 如果應用程式無法處理以 cookie 為基礎的親和性，您就無法使用這項功能。 若要使用它，請確定用戶端支援 cookie。

[Chromium 瀏覽器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)會帶出一個規定，其中不含[SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7)屬性的 HTTP Cookie 必須視為 SameSite = 不嚴格的。 在 CORS (跨原始資源分享) 要求的情況下，如果 cookie 必須在協力廠商內容中傳送，則必須使用 *SameSite = None;安全* 屬性，只應透過 HTTPS 傳送。 否則，在僅限 HTTP 案例中，瀏覽器不會在協力廠商內容中傳送 cookie。 這項來自 Chrome 的更新目標是增強安全性，並避免 (CSRF) 攻擊的跨網站偽造要求。 

為了支援這項變更，自 17 2020 年2月起，應用程式閘道 (所有的 SKU 類型) 除了現有的*ApplicationGatewayAffinity* cookie 之外，還會插入另一個稱為*ApplicationGatewayAffinityCORS*的 cookie。 *ApplicationGatewayAffinityCORS* cookie 已新增兩個屬性 (*"SameSite = None;「安全*」) 讓即使是跨原始來源的要求，仍會維持「粘滯」會話。

請注意，預設的親和性 cookie 名稱是 *ApplicationGatewayAffinity* ，您可以加以變更。 如果您使用自訂的親和性 cookie 名稱，則會以 CORS 作為尾碼來新增額外的 cookie。 例如， *CustomCookieNameCORS*。

> [!NOTE]
> 如果設定屬性 *SameSite = None* ，則必須同時包含 *安全* 旗標，且必須透過 HTTPS 傳送。  如果需要透過 CORS 的會話親和性，您必須將工作負載遷移至 HTTPS。 請參閱這裡的應用程式閘道的 TLS 卸載和端對端 TLS 檔- [總覽](ssl-overview.md)、 [使用 Azure 入口網站設定 tls 終止的應用程式閘道](create-ssl-portal.md)、使用 [應用程式閘道搭配入口網站設定端對端 tls](end-to-end-ssl-portal.md)。

### <a name="connection-draining"></a>清空連線

連接清空可協助您在規劃的服務更新期間，正常地移除後端集區成員。 您可以藉由啟用 HTTP 設定的連線清空，將此設定套用至後端集區的所有成員。 它可確保後端集區的所有取消登錄實例都繼續維持現有的連線，並提供可設定的超時時間的進行中要求，且不會收到任何新的要求或連接。 唯一的例外是由於閘道管理的會話親和性，會將要求系結至取消註冊的實例，而且會繼續轉送至取消註冊的實例。 連接清空適用于從後端集區中明確移除的後端實例。

### <a name="protocol"></a>通訊協定

應用程式閘道支援 HTTP 和 HTTPS 來將要求路由傳送至後端伺服器。 如果您選擇 HTTP，則連至後端伺服器的流量都會加密。 如果無法接受未加密的通訊，請選擇 [HTTPS]。

這項設定與接聽程式中的 HTTPS 結合， [可支援端對端 TLS](ssl-overview.md)。 這可讓您安全地傳輸加密至後端的機密資料。 在已啟用端對端 TLS 的後端集區中，每個後端伺服器都必須使用憑證來設定，以允許安全通訊。

### <a name="port"></a>連接埠

此設定會指定後端伺服器接聽來自應用程式閘道之流量的埠。 您可以設定介於1到65535之間的埠。

### <a name="request-timeout"></a>要求逾時

此設定是應用程式閘道等待接收來自後端伺服器回應的秒數。

### <a name="override-back-end-path"></a>覆寫後端路徑

這項設定可讓您設定選擇性的自訂轉送路徑，以在將要求轉送至後端時使用。 傳入路徑中符合 [覆 **寫後端路徑** ] 欄位中之自訂路徑的任何部分，都會複製到轉送的路徑。 下表顯示這項功能的運作方式：

- 當 HTTP 設定附加至基本要求路由規則時：

  | 原始要求  | 覆寫後端路徑 | 要求轉寄至後端 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | 覆寫            | /override/home/              |
  | /home/secondhome/ | 覆寫            | /override/home/secondhome/   |

- 當 HTTP 設定附加至以路徑為基礎的要求路由規則時：

  | 原始要求           | 路徑規則       | 覆寫後端路徑 | 要求轉寄至後端 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | 覆寫            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | 覆寫            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | 覆寫            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | 覆寫            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | 覆寫            | 覆寫                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | 覆寫            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | 覆寫            | 覆寫                   |

### <a name="use-for-app-service"></a>使用於 app service

這是僅限 UI 的快捷方式，可選取 Azure App Service 後端的兩個必要設定。 它可讓您 **從後端位址挑選主機名稱**，如果您還沒有自訂探查，則會建立新的探查。  (需詳細資訊，請參閱本文的 [從後端位址設定挑選主機名稱](#pick) 一節。 ) 新的探查會建立，並從後端成員的位址挑選探查標頭。

### <a name="use-custom-probe"></a>使用自訂探查

此設定會將 [自訂探查](application-gateway-probe-overview.md#custom-health-probe) 與 HTTP 設定產生關聯。 您只能將一個自訂探查與 HTTP 設定建立關聯。 如果您未明確建立自訂探查的關聯，則會使用 [預設探查](application-gateway-probe-overview.md#default-health-probe-settings) 來監視後端的健康情況。 建議您建立自訂探查，以進一步控制您後端的健全狀況監視。

> [!NOTE]
> 除非對應的 HTTP 設定與接聽程式明確相關聯，否則自訂探查不會監視後端集區的健康情況。

### <a name="pick-host-name-from-back-end-address"></a><a name="pick"></a>從後端位址挑選主機名稱

這項功能會將要求中的 *主機* 標頭動態設定為後端集區的主機名稱。 它會使用 IP 位址或 FQDN。

當後端的功能變數名稱不同于應用程式閘道的 DNS 名稱時，後端會依賴特定主機標頭來解析為正確的端點，這項功能會有所説明。

範例案例為多租使用者服務作為後端。 App service 是一種多租使用者服務，會使用具有單一 IP 位址的共用空間。 因此，只能透過在自訂網域設定中設定的主機名稱來存取 app service。

根據預設，自訂功能變數名稱為 *example.azurewebsites.net*。 若要使用應用程式閘道透過未在 app service 中明確註冊的主機名稱，或透過應用程式閘道的 FQDN 來存取 app service，請將原始要求中的主機名稱覆寫至 app service 的主機名稱。 若要這樣做，請啟用 [ **從後端位址設定挑選主機名稱** ] 設定。

針對現有的自訂 DNS 名稱對應至 app service 的自訂網域，您不需要啟用此設定。

> [!NOTE]
> App Service 環境（這是專用部署）不需要此設定。

### <a name="host-name-override"></a>主機名稱覆寫

這項功能會以您指定的主機名稱，取代應用程式閘道上連入要求中的 *主機* 標頭。

例如，如果在 [**主機名稱**] 設定中指定*www.contoso.com* ，則將 `https://appgw.eastus.cloudapp.azure.com/path1` `https://www.contoso.com/path1` 要求轉送至後端伺服器時，原始的要求 * 會變更為 *。

## <a name="back-end-pool"></a>後端集區

您可以將後端集區指向四種類型的後端成員：特定虛擬機器、虛擬機器擴展集、IP 位址/FQDN 或 app service。 

建立後端集區之後，您必須將它與一或多個要求路由規則建立關聯。 您也必須針對應用程式閘道上的每個後端集區設定健康情況探查。 當符合要求路由規則條件時，應用程式閘道會將流量轉送到狀況良好的伺服器 (由對應後端集區中的健康情況探查) 所決定。

## <a name="health-probes"></a>健康狀態探查

應用程式閘道預設會監視其後端中所有資源的健康情況。 但強烈建議您為每個後端 HTTP 設定建立自訂探查，以更充分掌控健康情況監視。 若要瞭解如何設定自訂探查，請參閱 [自訂健康情況探查設定](application-gateway-probe-overview.md#custom-health-probe-settings)。

> [!NOTE]
> 建立自訂健康情況探查之後，您必須將它與後端 HTTP 設定產生關聯。 除非對應的 HTTP 設定與使用規則的接聽程式明確相關聯，否則自訂探查不會監視後端集區的健康情況。

## <a name="next-steps"></a>後續步驟

現在您已瞭解應用程式閘道元件，您可以：

- [在 Azure 入口網站中建立應用程式閘道](quick-create-portal.md)
- [使用 PowerShell 建立應用程式閘道](quick-create-powershell.md)
- [使用 Azure CLI 建立應用程式閘道](quick-create-cli.md)
