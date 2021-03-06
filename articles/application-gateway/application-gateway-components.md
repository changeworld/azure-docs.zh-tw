---
title: 應用程式閘道元件
description: 本文提供應用程式閘道中各元件的相關資訊
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723311"
---
# <a name="application-gateway-components"></a>應用程式閘道元件

 應用程式閘道可做為用戶端的單一聯絡點。 它會將連入的應用程式流量分散到多個後端集區，包括 Azure Vm、虛擬機器擴展集、Azure App Service 和內部部署/外部伺服器。 若要分散流量，應用程式閘道會使用本文中所述的數個元件。

![應用程式閘道中使用的元件](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>前端 IP 位址

前端 IP 位址是與應用程式閘道相關聯的 IP 位址。 您可以將應用程式閘道設定為具有公用 IP 位址、私人 IP 位址或兩者。 應用程式閘道支援一個公用或一個私人 IP 位址。 您的虛擬網路和公用 IP 位址必須位於與您的應用程式閘道相同的位置。 建立之後，前端 IP 位址會與接聽程式相關聯。

### <a name="static-versus-dynamic-public-ip-address"></a>靜態與動態公用 IP 位址

Azure 應用程式閘道 V2 SKU 可設定為同時支援靜態內部 IP 位址和靜態公用 IP 位址，或僅支援靜態公用 IP 位址。 它無法設定為僅支援靜態內部 IP 位址。

V1 SKU 可設定為支援靜態或動態內部 IP 位址和動態公用 IP 位址。 應用程式閘道的動態 IP 位址不會在執行中的閘道上變更。 只有當您停止或啟動閘道時，它才會變更。 系統失敗、更新、Azure 主機更新等都不會變更。 

與應用程式閘道相關聯的 DNS 名稱不會在閘道生命週期內變更。 因此，您應該使用 CNAME 別名，並將它指向應用程式閘道的 DNS 位址。

## <a name="listeners"></a>接聽程式

接聽程式是一種邏輯實體，可檢查連入連線要求。 如果與要求相關聯的通訊協定、埠、主機名稱和 IP 位址符合與接聽程式設定相關聯的相同元素，接聽程式就會接受要求。

使用應用程式閘道之前，您至少必須新增一個接聽程式。 您可以將多個接聽程式附加至應用程式閘道，而且這些接聽程式可用於相同的通訊協定。

在接聽程式偵測到來自用戶端的連入要求之後，應用程式閘道會將這些要求路由至規則中所設定後端集區中的成員。

接聽程式支援下列埠和通訊協定。

### <a name="ports"></a>連接埠

埠是接聽程式接聽用戶端要求的位置。 您可以針對 v1 SKU 設定從1到65502的埠，v2 SKU 可設定1到65199。

### <a name="protocols"></a>通訊協定

應用程式閘道支援四種通訊協定： HTTP、HTTPS、HTTP/2 和 WebSocket：
>[!NOTE]
>HTTP/2 通訊協定支援僅適用於連線到應用程式閘道接聽程式的用戶端。 與後端伺服器集區的通訊一律會透過 HTTP/1.1。 預設已停用 HTTP/2 支援。 您可以選擇啟用它。

- 在接聽程式設定中指定 HTTP 與 HTTPS 通訊協定。
- [Websocket 和 HTTP/2 通訊協定](features.md#websocket-and-http2-traffic)的支援會以原生方式提供，而且預設會啟用[websocket 支援](application-gateway-websocket.md)。 使用者無法進行設定來選擇要啟用或停用 WebSocket 支援。 搭配 HTTP 和 HTTPS 接聽程式使用 Websocket。

使用 HTTPS 接聽程式進行 TLS 終止。 HTTPS 接聽程式會將加密和解密工作卸載至您的應用程式閘道，讓您的 web 伺服器不會受到額外負荷的負擔。

### <a name="custom-error-pages"></a>自訂錯誤頁面

應用程式閘道可讓您建立自訂錯誤頁面，而不是顯示預設的錯誤頁面。 您可以使用自訂錯誤頁面來搭配您自己的商標和版面配置。 當要求無法連線到後端時，應用程式閘道會顯示自訂錯誤頁面。

如需詳細資訊，請參閱 [應用程式閘道的自訂錯誤頁面](custom-error.md)。

### <a name="types-of-listeners"></a>接聽程式的類型

有兩種類型的接聽程式：

- **基本**。 這種類型的接聽程式會接聽單一網域網站，其中包含與應用程式閘道 IP 位址的單一 DNS 對應。 當您在應用程式閘道後方裝載單一網站時，需要此接聽程式設定。

- **多網站**。 當您想要在相同的應用程式閘道上，針對一個以上的 web 應用程式設定以主機名稱或功能變數名稱為基礎的路由時，需要此接聽程式設定。 此功能可讓您將 100 多個網站新增到一個應用程式閘道，為您的部署設定更有效率的拓撲。 每個網站都可以導向到自己的後端集區。 例如，contoso.com、fabrikam.com 和 adatum.com 三個網域都指向應用程式閘道的 IP 位址。 您將建立三個 [多網站](multiple-site-overview.md) 接聽程式，並針對個別的埠和通訊協定設定來設定每個接聽程式。 

    您也可以在多網站接聽程式中定義萬用字元主機名稱，並為每個接聽程式定義最多 5 個主機名稱。 若要深入瞭解，請參閱接聽程式 [中的萬用字元主機名稱 (預覽) ](multiple-site-overview.md#wildcard-host-names-in-listener-preview)。

    如需有關如何設定多網站接聽程式的詳細資訊，請參閱 [使用 Azure 入口網站在應用程式閘道中進行多網站裝載](create-multiple-sites-portal.md)。

建立接聽程式之後，您可以將它與要求路由規則產生關聯。 此規則決定如何將接聽程式收到的要求路由傳送至後端。 要求路由規則也包含要路由傳送的後端集區，以及提及後端埠、通訊協定等的 HTTP 設定。

## <a name="request-routing-rules"></a>要求路由規則

要求路由規則是應用程式閘道的重要元件，因為它會決定如何在接聽程式上路由傳送流量。 此規則會系結接聽程式、後端伺服器集區和後端 HTTP 設定。

當接聽程式接受要求時，要求路由規則會將要求轉送至後端，或將其重新導向至其他位置。 如果將要求轉送至後端，則要求路由規則會定義要將它轉寄至哪個後端伺服器集區。 要求路由規則也會決定是否要重寫要求中的標頭。 一個接聽程式可以附加至一個規則。

有兩種類型的要求路由規則：

- **基本**。 相關聯接聽程式上的所有要求 (例如，blog.contoso.com/* ) 都會使用相關聯的 HTTP 設定轉送到相關聯的後端集區。

- 以**路徑為基礎**。 此路由規則可讓您根據要求中的 URL，將相關聯的接聽程式上的要求路由至特定的後端集區。 如果要求中的 URL 路徑符合以路徑為基礎的規則中的路徑模式，則規則會路由傳送該要求。 它只會將路徑模式套用至 URL 路徑，而不會套用到其查詢參數。 如果接聽程式要求的 URL 路徑不符合任何路徑型規則，則會將要求路由傳送至預設後端集區和 HTTP 設定。

如需詳細資訊，請參閱以 [URL 為基礎的路由](url-route-overview.md)。

### <a name="redirection-support"></a>重新導向支援

要求路由規則也可讓您重新導向應用程式閘道上的流量。 這是泛型重新導向機制，因此您可以使用規則在您定義的任何埠之間進行重新導向。

您可以選擇將重新導向目標設為另一個接聽程式 (這可協助啟用自動 HTTP 至 HTTPS 重新導向) 或外部網站。 您也可以選擇讓重新導向成為暫時性或永久性，或是將 URI 路徑和查詢字串附加至重新導向的 URL。

如需詳細資訊，請參閱 [在您的應用程式閘道上重新導向流量](redirect-overview.md)。

### <a name="rewrite-http-headers-and-url"></a>重寫 HTTP 標頭和 URL

藉由使用重寫規則，您可以新增、移除或更新 HTTP (S) 要求和回應標頭，以及 URL 路徑和查詢字串參數，因為要求和回應封包會透過應用程式閘道在用戶端和後端集區之間移動。

標頭和 URL 參數可以設定為靜態值或其他標頭和伺服器變數。 這有助於重要的使用案例，例如，將用戶端 IP 位址解壓縮、移除後端的機密資訊、增加更多安全性等等。

如需詳細資訊，請參閱在 [您的應用程式閘道上重寫 HTTP 標頭和 URL](rewrite-http-headers-url.md)。

## <a name="http-settings"></a>HTTP 設定

應用程式閘道會將流量路由傳送到要求路由規則中指定的後端伺服器 (，其中包括使用埠號碼、通訊協定，以及此元件中詳述的其他設定) 的 HTTP 設定。

HTTP 設定中使用的埠和通訊協定會決定應用程式閘道和後端伺服器之間的流量是否已加密 (提供端對端 TLS) 或未加密。

此元件也用來：

- 使用以 [cookie 為基礎的會話親和性](features.md#session-affinity)，判斷是否要在相同的伺服器上保留使用者會話。

- 使用連線 [清空](features.md#connection-draining)來正常移除後端集區成員。

- 建立自訂探查的關聯來監視後端健康情況、設定要求逾時間隔、覆寫要求中的主機名稱和路徑，以及提供單鍵的簡易方式來指定 App Service 後端的設定。

## <a name="backend-pools"></a>後端集區

後端集區會將要求路由至提供要求的後端伺服器。 後端集區可以包含：

- NIC
- 虛擬機器擴展集
- 公用 IP 位址
- 內部 IP 位址
- FQDN
- 多租使用者後端 (例如 App Service) 

應用程式閘道後端集區成員未系結至可用性設定組。 應用程式閘道可以與位於虛擬網路外的實例進行通訊。 因此，只要有 IP 連線能力，後端集區的成員可以跨叢集、跨資料中心或 Azure 外部。

如果您使用內部 Ip 作為後端集區成員，您必須使用 [虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md) 或 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 虛擬網路對等互連是支援的，且在其他虛擬網路中的流量負載平衡方面很有用。

如果允許流量，則應用程式閘道也可以與 Azure ExpressRoute 或 VPN 通道連線的內部部署伺服器通訊。

您可以針對不同類型的要求建立不同的後端集區。 例如，針對一般要求建立一個後端集區，然後針對您的應用程式，建立另一個後端集區以進行微服務的要求。

## <a name="health-probes"></a>健康狀態探查

根據預設，應用程式閘道會監視其後端集區中所有資源的健康情況，並自動移除狀況不良的資源。 然後，它會監視狀況不良的實例，並將其新增回狀況良好的後端集區（當它們變成可用時）並回應健康情況探查。

除了使用預設的健全狀況探查監視，您也可以自訂健全狀況探查，以符合應用程式的需求。 自訂探查可讓您更細微地控制健全狀況監視。 使用自訂探查時，您可以設定自訂主機名稱、URL 路徑、探查間隔，以及將後端集區實例標示為狀況不良、自訂狀態碼和回應內文相符等之前，可接受的失敗回應數目。建議您設定自訂探查來監視每個後端集區的健康情況。

如需詳細資訊，請參閱 [監視應用程式閘道的健康情況](../application-gateway/application-gateway-probe-overview.md)。

## <a name="next-steps"></a>接下來的步驟

建立應用程式閘道：

* [在 Azure 入口網站中](quick-create-portal.md)
* [使用 Azure PowerShell](quick-create-powershell.md)
* [使用 Azure CLI](quick-create-cli.md)
