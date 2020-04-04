---
title: Azure 應用程式閘道設定概述
description: 本文介紹如何組態 Azure 應用程式閘道的元件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 89d894a5125a16f95e6ef8a15c2503d48f3a8e55
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632182"
---
# <a name="application-gateway-configuration-overview"></a>應用程式閘道設定概述

Azure 應用程式閘道由多個元件組成,您可以以各種方式為不同的方案配置這些元件。 本文介紹如何配置每個元件。

![應用程式閘道元件流程圖](./media/configuration-overview/configuration-overview1.png)

此圖像演示了具有三個偵聽器的應用程式。 前兩個分別為`http://acme.com/*``http://fabrikam.com/*`和 的多站點偵聽器。 兩者都在埠 80 上收聽。 第三個是具有端到端傳輸層安全 (TLS) 端接(以前稱為安全套接字層 (SSL) 端接)的基本偵聽器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虛擬網路和專用子網路

應用程式閘道是虛擬網路中的專用部署。 在虛擬網路中,應用程序閘道需要專用子網路。 可以在子網中具有給定應用程式閘道部署的多個實例。 您還可以在子網中部署其他應用程式閘道。 但是,您無法在應用程式閘道中部署任何其他資源。

> [!NOTE]
> 不能在同一子網上混合Standard_v2和標準 Azure 應用程式閘道。

#### <a name="size-of-the-subnet"></a>子網路的大小

如果配置了專用前端 IP,應用程式閘道每個實體使用一個專用 IP 位址,另一個專用 IP 位址。

Azure 還在每個子網中保留五個 IP 位址供內部使用:前四個 IP 位址和最後一個 IP 位址。 例如,考慮 15 個沒有專用前端 IP 的應用程式閘道實例。 此子網至少需要 20 個 IP 位址:5 個用於內部使用,15 個用於應用程式閘道實例。 因此,您需要一個 /27 子網大小或更大。

考慮一個子網,該子網具有 27 個應用程式閘道實例和專用前端 IP 的 IP 位址。 在這種情況下,您需要 33 個 IP 位址:27 個用於應用程式閘道實例,一個用於專用前端,五個用於內部使用。 因此,您需要 /26 子網大小或更大。

我們建議您使用至少 /28 的子網大小。 此大小為您提供 11 個可用的 IP 位址。 如果應用程式負載需要 10 個以上應用程式閘道實例,請考慮 /27 或 /26 子網大小。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>應用程式閘道網上網路安全群組

應用程式閘道支援網路安全組 (NSG)。 但也有一些限制:

- 您必須允許應用程式閘道 v1 SKU 的 TCP 埠 65503-65534 上的傳入 Internet 流量,允許 v2 SKU 的 TCP 埠 65200-65535,目標子網為**Any,** 源為**閘道管理員**服務標記。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受 Azure 證書的保護(鎖定)。 外部實體(包括這些閘道的客戶)無法在這些終結點上進行通訊。

- 無法封鎖輸出網際網路連線。 NSG 中的預設出站規則允許互聯網連接。 建議您：

  - 不要刪除預設的出站規則。
  - 不要創建拒絕任何出站連接的其他出站規則。

- 必須允許來自**Azure Load 平衡器代碼的**流量。

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>允許應用程式閘道存取幾個來源 IP

對於此方案,請使用應用程式閘道網上的 NSG。 依以下優先權順序對子網實施以下限制:

1. 允許來自源 IP 或 IP 範圍的傳入流量,目標作為整個應用程式閘道網位址範圍和目標埠作為入站存取埠,例如,埠 80 用於 HTTP 訪問。
2. 允許來自源的傳入請求作為**GatewayManager**服務標記和目標埠作為 **"Any",** 目標埠為 65503-65534,用於應用程式閘道 v1 SKU,埠 65200-65535 表示 v2 SKU 的[後端執行狀況狀態通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受 Azure 證書的保護(鎖定)。 如果沒有適當的證書,外部實體就無法對這些終結點發起更改。
3. 允許在[網路安全組中](https://docs.microsoft.com/azure/virtual-network/security-overview)允許傳入的 Azure 負載均衡*器探測(AzureLoad平衡器*標記)和入站虛擬網路流量(*虛擬網路*標記)。
4. 使用「全部拒絕」規則阻止所有其他傳入流量。
5. 允許所有目的地對網際網路的輸出流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>應用程式閘道子網路上支援的使用者定義路由

> [!IMPORTANT]
> 在應用程式閘道網上使用UDR可能會導致[後端運行狀況檢視中](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)的運行狀況顯示為 **「未知」。** 它還可能導致生成應用程式閘道日誌和指標失敗。 我們建議您不要在應用程式閘道網上使用 UDR,以便您可以查看後端運行狀況、日誌和指標。

- **v1**

   對於 v1 SKU,應用程式閘道支援使用者定義的路由 (UDR),只要它們不更改端到端請求/回應通信。 例如,您可以在應用程式閘道網中設置 UDR 以指向防火牆設備進行數據包檢查。 但是,您必須確保數據包在檢查后能夠到達其預期目標。 否則可能會導致運行狀況探測或流量路由行為不正確。 這包括透過 Azure ExpressRoute 或 VPN 閘道在虛擬網路中傳播的學問路由或預設 0.0.0.0/0 路由。

- **v2**

   對於 v2 SKU,有支援且不受支援的方案:

   **v2 支援的機制**
   > [!WARNING]
   > 路由表配置不正確可能會導致應用程式閘道 v2 中的非對稱路由。 確保所有管理/控制平面流量直接發送到 Internet,而不是透過虛擬裝置發送。 日誌記錄和指標也可能受到影響。


  **方案 1**: UDR 停用邊界閘道協定 (BGP) 路由傳播到應用程式閘道

   有時,默認閘道由 (0.0.0.0/0) 通過與應用程式閘道虛擬網路關聯的 ExpressRoute 或 VPN 閘道進行通告。 這打破了管理平面流量,這需要直接訪問 Internet。 在這種情況下,UDR可用於禁用 BGP 路由傳播。 

   要禁用 BGP 路由傳播,請使用以下步驟:

   1. 在 Azure 中建立路由表資源。
   2. 禁用**虛擬網路閘道由傳播**參數。 
   3. 將路由表關聯到相應的子網。 

   為此方案啟用 UDR 不應破壞任何現有設置。

  **方案 2**: UDR 將 0.0.0.0/0 定向到互聯網

   您可以創建 UDR 將 0.0.0.0/0 流量直接發送到 Internet。 

  **方案 3**:Azure 庫伯內斯服務庫貝內特的 UDR

  如果使用 Azure 庫伯奈服務 (AKS) 和應用程式閘道入口控制器 (AGIC) 使用 kubenet,則需要設定路由表,以允許將發送到 pod 的流量路由到正確的節點。 如果使用 Azure CNI,則不需要這樣做。 

   要設置路由表以允許 kubenet 工作,請使用以下步驟:

  1. 在 Azure 中建立路由表資源。 
  2. 創建後,轉到 **「路由」** 頁。 
  3. 新增新路由:
     - 位址前置碼應該是要在 AKS 中覆蓋的 pod 的 IP 範圍。 
     - 下一個躍點類型應該是**虛擬裝置**。 
     - 下一個躍點位址應該是託管在位址前綴欄位中定義的 IP 範圍內的 pod 節點的 IP 位址。 
    
  **v2 不支援的機制**

  **方案 1**: 虛擬裝置的 UDR

  V2 不支援任何需要通過任何虛擬設備、集線器/分支虛擬網路或內部(強制隧道)重定向 0.0.0.0/0 的方案。

## <a name="front-end-ip"></a>前端 IP

您可以將應用程式閘道配置為具有公共 IP 位址、專用 IP 位址或兩者。 當您託管後端時,客戶端必須透過針對 Internet 的虛擬 IP (VIP) 透過網路存取後端,則需要公共 IP。 

對於未暴露到 Internet 的內部終結點,不需要公共 IP。 這稱為*內部負載均衡器*(ILB) 端點或專用前端 IP。 應用程式閘道 ILB 對於未暴露到 Internet 的內部業務線應用程式非常有用。 它還適用於安全邊界內未向 Internet 公開但需要迴圈負載分發、作業階段粘接或 TLS 終止的安全邊界內多層應用程式中的服務和層。

僅支援 1 個公共 IP 位址或一個專用 IP 位址。 創建應用程式閘道時選擇前端 IP。

- 對於公共 IP,您可以建立新的公共 IP 位址,或在與應用程式閘道相同的位置使用現有公共 IP。 關於詳細資訊,請參閱[靜態與動態公共 IP 位址](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)。

- 對於專用 IP,可以從創建應用程式閘道的子網指定專用 IP 位址。 如果未指定一個,則會自動從子網中選擇任意 IP 位址。 以後無法更改您選擇的 IP 位址類型(靜態或動態)。 有關詳細資訊,請參閱[使用內部負載均衡器建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

前端 IP 位址與*偵聽器*相關聯,偵聽器在前端 IP 上檢查傳入請求。

## <a name="listeners"></a>接聽程式

偵聽器是一個邏輯實體,它通過使用埠、協定、主機和IP 地址檢查傳入連接請求。 配置偵聽器時,必須為這些值輸入與閘道上傳入請求中的相應值匹配的值。

使用 Azure 門戶創建應用程式閘道時,還可以透過偵聽器選擇協定和埠來創建預設偵聽器。 您可以選擇是否在偵聽器上啟用 HTTP2 支援。 創建應用程式閘道後,可以編輯該預設偵聽器 *(AppGateway HttpListener)* 的設置或創建新偵聽器。

### <a name="listener-type"></a>偵聽器類型

創建新偵聽器時,在[*基本*和*多站點*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)之間進行選擇。

- 如果希望接受所有請求(針對任何域)並將其轉發到後端池,請選擇"基本"。 [瞭解如何使用基本偵聽器建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果要根據主機頭或*主機*名將請求轉發到不同的後端池,請選擇多網站偵聽器,其中還必須指定與傳入請求匹配的主機名。 這是因為應用程式閘道依賴於 HTTP1.1 主機標頭在同一公共 IP 位址和埠上託管多個網站。

#### <a name="order-of-processing-listeners"></a>處理偵聽器的順序

對於 v1 SKU,請求根據規則的順序和偵聽器的類型進行匹配。 如果具有基本偵聽器的規則按順序排在第一位,則首先處理該規則,並接受對該埠和IP組合的任何請求。 為了避免這種情況,請先使用多站點偵聽器配置規則,並將具有基本偵聽器的規則推送到清單中的最後一個。

對於 v2 SKU,多站點偵聽器在基本偵聽器之前進行處理。

### <a name="front-end-ip"></a>前端 IP

選擇計畫與此偵聽器關聯的前端 IP 位址。 偵聽器將偵聽此 IP 上的傳入請求。

### <a name="front-end-port"></a>前端埠

選擇前端埠。 選擇現有埠或創建新埠。 從[允許的埠範圍](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)中選擇任何值。 您不僅可以使用眾所周知的埠(如 80 和 443),還可以使用任何允許的適合的自定義埠。 埠可用於面向公眾的偵聽器或面向私有的偵聽器。

### <a name="protocol"></a>通訊協定

選擇 HTTP 或 HTTPS:

- 如果選擇 HTTP,則用戶端和應用程式閘道之間的流量將未加密。

- 如果要[TLS 終止](features.md#secure-sockets-layer-ssltls-termination)或[端到端 TLS 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview),請選擇 HTTPS。 用戶端和應用程式閘道之間的流量已加密。 TLS 連接在應用程式閘道終止。 如果要進行端到端 TLS 加密,則必須選擇 HTTPS 並配置**後端 HTTP**設定。 這可確保流量在從應用程式閘道傳輸到後端時重新加密。


要配置 TLS 端接和端到端 TLS 加密,必須向偵聽器添加證書,以使應用程式閘道能夠派生對稱金鑰。 這是由 TLS 協議規範決定的。 對稱密鑰用於加密和解密發送到閘道的流量。 閘道憑證必須採用個人資訊交換 (PFX) 格式。 此格式允許您匯出閘道用於加密和解密流量的私鑰。

#### <a name="supported-certificates"></a>支援的憑證

請參考[TLS 的憑證](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>附加協定支援

#### <a name="http2-support"></a>HTTP2 支援

HTTP/2 協定支援僅適用於僅連接到應用程式閘道偵聽器的用戶端。 與後端伺服器池的通信通過 HTTP/1.1。 預設已停用 HTTP/2 支援。 以下 Azure PowerShell 程式碼段展示如何啟用此功能:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 支援

默認情況下啟用 WebSocket 支援。 沒有用戶可配置的設置來啟用或禁用它。 您可以將 WebSocket 與 HTTP 和 HTTPS 偵聽器同時使用。

### <a name="custom-error-pages"></a>自訂錯誤頁面

您可以在全域級別或偵聽器級別定義自定義錯誤。 但是,目前不支援從 Azure 門戶創建全域級別的自定義錯誤頁。 您可以在偵聽器級別為 403 Web 應用程式防火牆錯誤或 502 維護頁設定自定義錯誤頁。 還必須為給定的錯誤狀態代碼指定可公開存取的 blob URL。 如需詳細資訊，請參閱[建立應用程式閘道的自訂錯誤頁面](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![應用程式閘道錯誤碼](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

要設定全域自訂錯誤頁,請參閱[Azure PowerShell 設定](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)。

### <a name="tls-policy"></a>TLS 政策

您可以集中 TLS/SSL 憑證管理,並減少後端伺服器場的加密解密開銷。 集中式 TLS 處理還允許您指定適合您的安全要求的中央 TLS 策略。 您可以選擇*預設*、*預定義*或*自訂*TLS 策略。

配置 TLS 策略以控制 TLS 協定版本。 您可以將應用程式閘道配置為使用從 TLS1.0、TLS1.1 和 TLS1.2 進行 TLS 握手的最小協定版本。 默認情況下,SSL 2.0 和 3.0 已禁用,並且不可配置。 有關詳細資訊,請參閱[應用程式閘道 TLS 策略概述](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

創建偵聽器後,將其與請求路由規則相關聯。 該規則確定如何在偵聽器上接收的請求路由到後端。

## <a name="request-routing-rules"></a>要求路由規則

使用 Azure 入口閘道時,將創建預設規則 *(rule1)。* 此規則將預設偵聽器 *(appGateway HttpListener)* 與預設後端池 *(AppGateway BackendPool)* 和預設後端 HTTP 設定 *(AppGatewayBackendHttpSettings)* 綁定。 建立閘道後,可以編輯預設規則的設置或創建新規則。

### <a name="rule-type"></a>規則型別

創建規則時,在[*基本*和*基於路徑*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)之間進行選擇。

- 如果要將關聯偵聽器(例如*博客<i></i>.contoso.com/\*)* 上的所有請求轉發到單個後端池,請選擇"基本"。
- 如果要將請求從特定 URL 路徑路由到特定後端池,請選擇基於路徑。 路徑模式僅應用於 URL 的路徑,而不是其查詢參數。

#### <a name="order-of-processing-rules"></a>處理規則的順序

對於 v1 SKU,傳入請求的模式匹配按路徑在基於路徑規則的 URL 路徑映射中列出的順序進行處理。 如果請求在路徑映射中的兩個或多個路徑中匹配模式,則首先列出的路徑將匹配。 請求將轉發到與該路徑關聯的後端。

對於 v2 SKU,完全匹配的優先順序高於 URL 路徑映射中的路徑順序。 如果請求在兩個或多個路徑中匹配模式,則請求將轉發到與與請求完全匹配的路徑關聯的後端。 如果傳入請求中的路徑與映射中的任何路徑不完全匹配,則請求的模式匹配將處理基於路徑規則的路徑映射順序表中。

### <a name="associated-listener"></a>關聯的偵聽器

將偵聽器關聯到規則,以便評估與偵聽器關聯的*請求路由規則*,以確定要將請求路由到的後端池。

### <a name="associated-back-end-pool"></a>關聯的後端介面

與規則關聯包含為偵聽器接收的請求提供服務的後端目標的後端池。

 - 對於基本規則,只允許一個後端池。 關聯偵聽器上的所有請求都轉發到該後端池。

 - 對於基於路徑的規則,添加多個對應於每個 URL 路徑的後端池。 與輸入的 URL 路徑匹配的請求將轉發到相應的後端池。 此外,添加預設後端池。 與規則中的任何 URL 路徑不匹配的請求將轉發到該池。

### <a name="associated-back-end-http-setting"></a>關聯的後端介面 HTTP 設定

為每個規則添加後端 HTTP 設定。 通過使用此設置中指定的埠號、協定和其他資訊,請求從應用程式網路由到後端目標。

對於基本規則,只允許一個後端 HTTP 設置。 使用此 HTTP 設定,關聯偵聽器上的所有請求都轉發到相應的後端目標。

對於基於路徑的規則,添加多個對應於每個 URL 路徑的後端 HTTP 設定。 使用對應於每個 URL 路徑的 HTTP 設定,將匹配此設定中的 URL 路徑的請求轉發到相應的後端目標。 此外,請添加預設的 HTTP 設定。 使用此規則中的任何 URL 路徑不匹配的請求將使用預設 HTTP 設定轉發到預設後端池。

### <a name="redirection-setting"></a>重定設定

如果為基本規則配置重定向,則關聯偵聽器上的所有請求都重定向到目標。 這是*全球*重定向。 如果為基於路徑的規則配置重定向,則僅重定向特定網站區域中的請求。 例如,購物車區域以 */cart/\** 表示。 這是*基於路徑的*重定向。

有關重定向的詳細資訊,請參閱[應用程式閘道重定向概述](redirect-overview.md)。

#### <a name="redirection-type"></a>重定向類型

選擇所需的重定向類型:*永久(301)、**臨時(307)、**找到(302)* 或*查看其他 (303)。*

#### <a name="redirection-target"></a>重定向目標

選擇其他偵聽器或外部網站作為重定向目標。

##### <a name="listener"></a>接聽程式

選擇偵聽器作為重定向目標,將流量從一個偵聽器重定向到另一個偵聽器。 如果要啟用 HTTP 到 HTH 重定向,則需要此設定。 它將檢查傳入 HTTP 請求的源偵聽器的流量重定向到檢查傳入 HTTPS 請求的目標偵聽器。 您還可以選擇在轉發到重定向目標的請求中包括原始請求中的查詢字串和路徑。

![應用程式閘道元件對話框](./media/configuration-overview/configure-redirection.png)

有關 HTTP 到 HTTPS 重定向的詳細資訊,請參閱:
- [使用 Azure 門戶重定向 HTTP 到 HTTPS](redirect-http-to-https-portal.md)
- [使用 PowerShell 將 HTTP 到 HTTPS 重定向](redirect-http-to-https-powershell.md)
- [使用 Azure CLI 重定向 HTTP 到 HTTPS](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>外部網站

如果要將與此規則關聯的偵聽器上的流量重定向到外部網站,請選擇外部網站。 您可以選擇在轉寄到重定向目標的請求中包括原始請求中的查詢字串。 無法將路徑轉發到原始請求中的外部網站。

有關重定向的詳細資訊,請參閱:
- [使用 PowerShell 將流量重定到外部站台](redirect-external-site-powershell.md)
- [使用 CLI 將流量重定到外部站台](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>重寫 HTTP 標頭設定

當請求和回應數據包在用戶端和後端池之間移動時,此設置會添加、刪除或更新 HTTP 請求和回應標頭。 如需詳細資訊，請參閱

 - [重寫 HTTP 標頭概述](rewrite-http-headers.md)
 - [設定 HTTP 標頭重寫](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>HTTP 設定

應用程式閘道使用此處指定的配置將流量路由到後端伺服器。 創建 HTTP 設定後,必須將其與一個或多個請求路由規則相關聯。

### <a name="cookie-based-affinity"></a>以 Cookie 為基礎的同質性

Azure 應用程式閘道使用閘道管理的 Cookie 來維護使用者作業階段。 當使用者將第一個請求發送到應用程式閘道時,它會在回應中設置一個關聯 Cookie,其中包含包含會話詳細資訊的哈希值,以便承載關聯 Cookie 的後續請求將路由到同一後端伺服器以保持粘性。 

當您希望將使用者會話保留在同一伺服器上,並且會話狀態在伺服器上本地保存用於使用者會話時,此功能非常有用。 如果應用程式無法處理基於 Cookie 的關聯,則無法使用此功能。 要使用它,請確保客戶端支援 Cookie。

[鉻瀏覽器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)帶來了一項任務,即沒有[SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7)屬性的 HTTP Cookie 必須被視為 SameSite_Lax。 在 CORS(跨源資源分享)請求中,如果 Cookie 必須在第三方上下文中發送,則必須使用*SameSite_None;安全*屬性,並且應僅通過 HTTPS 發送。 否則,在僅 HTTP 方案中,瀏覽器不會在第三方上下文中發送 Cookie。 Chrome 的此更新的目的是增強安全性,避免跨網站請求偽造 (CSRF) 攻擊。 

為了支援此更改,從 2020 年 2 月 17 日開始,應用程式閘道(所有 SKU 類型)將除了現有的*應用程式閘道 Affinity* Cookie 外,還將注入另一個稱為*應用程式網關 AffinityCORS*的 Cookie。 *應用程式閘道關聯CORS* Cookie 還有兩個屬性添加到它 *("同一網站=無;安全"),* 以便粘滯會話即使對於跨源請求也保持。

請注意,預設的關聯 Cookie 名稱是*應用程式閘道相關性*,您可以更改它。 如果您使用的是自定義關聯曲奇名稱,則添加附加 Cookie 作為後綴。 例如,*自訂 Cookie 名稱CORS*。

> [!NOTE]
> 如果設置了*屬性 SameSite_None,* 則 Cookie 還必須包含*安全*標誌,並且必須通過 HTTPS 發送。  如果通過 CORS 需要會話關聯,則必須將工作負荷遷移到 HS。 請參考此處的應用程式閘道的 TLS 卸載和端到端 TLS 文件 –[概述](ssl-overview.md),[使用 Azure 門戶使用 TLS 終止設定應用程式閘道](create-ssl-portal.md),[使用與門戶一起使用應用程式閘道設定端到端 TLS。](end-to-end-ssl-portal.md)

### <a name="connection-draining"></a>清空連線

連接耗盡可説明您在計畫的服務更新期間正常刪除後端池成員。 您可以在規則創建期間將此設置應用於後端池的所有成員。 它可確保後端池的所有登出實例繼續維護現有連接,並持續滿足可配置超時的請求,並且不會收到任何新請求或連接。 唯一的例外是由於閘道託管的工作階段關聯而綁定用於取消註冊實例的請求,並將繼續轉發到註銷實例。 連接耗盡適用於從後端池顯式刪除的後端實例。

### <a name="protocol"></a>通訊協定

應用程式閘道支援 HTTP 與 HTTPS,用於將請求路由到後端伺服器。 如果選擇 HTTP,則後端伺服器的流量將未加密。 如果未加密的通信不可接受,請選擇 HS。

此設定與偵聽器中的 HTTPS 結合使用,支援端到端[TLS。](ssl-overview.md) 這允許您安全地將加密的敏感數據傳輸到後端。 後端池中啟用端到端 TLS 的每個後端伺服器都必須使用證書進行配置,以允許安全通訊。

### <a name="port"></a>連接埠

此設置指定後端伺服器偵聽來自應用程式閘道的流量的埠。 您可以設定從 1 到 65535 的連接埠。

### <a name="request-timeout"></a>要求逾時

此設置是應用程式閘道等待接收後端伺服器回應的秒數。

### <a name="override-back-end-path"></a>覆寫後端介面路徑

此設定允許您設定可選的自訂轉發路徑,以便在請求轉發到後端時使用。 與**重寫後端路徑**欄位中的自訂路徑匹配的傳入路徑的任何部分都將複製到轉發的路徑。 下表顯示了此功能的工作原理:

- 當 HTTP 設定附加到基本的請求路由規則時:

  | 原始要求  | 覆寫後端介面路徑 | 要求轉寄到後端介面 |
  | ----------------- | --------------------- | ---------------------------- |
  | /首頁/            | /覆寫/            | /覆蓋/首頁/              |
  | /首頁/第二次回家/ | /覆寫/            | /覆蓋/首頁/第二次回家/   |

- 當 HTTP 設定附加到基於路徑的請求路由規則時:

  | 原始要求           | 路徑規則       | 覆寫後端介面路徑 | 要求轉寄到後端介面 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /路徑規則/首頁/            | /路徑規則*      | /覆寫/            | /覆蓋/首頁/              |
  | /路徑規則/首頁/第二家/ | /路徑規則*      | /覆寫/            | /覆蓋/首頁/第二次回家/   |
  | /首頁/                     | /路徑規則*      | /覆寫/            | /覆蓋/首頁/              |
  | /首頁/第二次回家/          | /路徑規則*      | /覆寫/            | /覆蓋/首頁/第二次回家/   |
  | /路徑規則/首頁/            | /路徑規則/首頁* | /覆寫/            | /覆寫/                   |
  | /路徑規則/首頁/第二家/ | /路徑規則/首頁* | /覆寫/            | /覆蓋/秒首頁/        |
  | /路徑規則/                 | /路徑規則/      | /覆寫/            | /覆寫/                   |

### <a name="use-for-app-service"></a>用於應用服務

這是僅 UI 的快捷方式,用於選擇 Azure 應用服務後端的兩個必需設置。 它啟用**從後端位址選擇主機名**,並且如果您還沒有一個新的自定義探測器,它會創建一個新的自定義探測器。 (有關詳細資訊,請參閱本文[後端位址設定中的選取主機名稱](#pick)。將創建新的探測器,並從後端成員的地址選取探測標頭。

### <a name="use-custom-probe"></a>使用自訂探測

此設置將[自定義探測](application-gateway-probe-overview.md#custom-health-probe)與 HTTP 設定關聯。 只能將一個自定義探測與 HTTP 設定相關聯。 如果未顯式關聯自定義探測,則[默認探測器](application-gateway-probe-overview.md#default-health-probe-settings)用於監視後端的運行狀況。 我們建議您創建自定義探測器,以便更好地控制後端的運行狀況監視。

> [!NOTE]
> 自定義探測不會監視後端池的運行狀況,除非相應的 HTTP 設定與偵聽器顯式關聯。

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>從後端位址選擇主機名稱

此功能動態地將請求中的*主機*標頭設置到後端池的主機名。 它使用 IP 位址或 FQDN。

當後端介面的功能變數名稱與應用程式閘道的 DNS 名稱不同,並且後端依賴於特定的主機標頭解析到正確的終結點時,此功能會有所説明。

例如,多租戶服務作為後端。 應用服務是使用具有單個 IP 位址的共用空間的多租戶服務。 因此,只能通過自定義域設置中配置的主機名訪問應用服務。

預設情況下,自訂*網功能變數名稱example.azurewebsites.net*。 要通過使用應用程式閘道存取應用服務,透過應用服務中未顯式註冊的主機名或透過應用程式閘道的FQDN存取應用服務,請將原始請求中的主機名覆蓋到應用服務的主機名。 此選項,**從後端位址設定啟用所選取主機名稱**。

對於其現有自定義 DNS 名稱映射到應用服務的自定義域,不必啟用此設置。

> [!NOTE]
> 應用服務環境(專用部署)不需要此設置。

### <a name="host-name-override"></a>主機名稱覆寫

此功能將應用程式閘道上的傳入請求中的*主機*標頭替換為您指定的主機名。

例如,如果在**主機名稱**設定中指定*了www.contoso.com,* 則當請求轉發到`https://appgw.eastus.cloudapp.azure.com/path1`後端 伺服器`https://www.contoso.com/path1`時,原始 請求 * 將更改為 * 。

## <a name="back-end-pool"></a>後端集區

您可以將後端介面指向四種類型的後端組組成員:特定虛擬機器、虛擬機器規模集、IP 位址/FQDN 或應用服務。 

創建後端池後,必須將其與一個或多個請求路由規則相關聯。 還必須為應用程式閘道上的每個後端池配置運行狀況探測。 當滿足請求路由規則條件時,應用程式閘道將流量轉發到相應後端池中的正常運行伺服器(由運行狀況探測器確定)。

## <a name="health-probes"></a>健康狀態探查

默認情況下,應用程式閘道監視其後端中所有資源的運行狀況。 但是,我們強烈建議您為每個後端 HTTP 設置創建自定義探測,以便更好地控制運行狀況監視。 要瞭解如何設定自訂偵測器,請參閱[自訂執行狀況偵測設定](application-gateway-probe-overview.md#custom-health-probe-settings)。

> [!NOTE]
> 創建自定義運行狀況探測後,需要將其關聯到後端 HTTP 設定。 自定義探測不會監視後端池的運行狀況,除非使用規則顯式與偵聽器關聯相應的 HTTP 設定。

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解了應用程式閘道元件,您可以:

- [在 Azure 門戶建立應用程式閘道](quick-create-portal.md)
- [使用 PowerShell 建立應用程式閘道](quick-create-powershell.md)
- [使用 Azure CLI 建立應用程式閘道](quick-create-cli.md)
