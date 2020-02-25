---
title: 什麼是 Azure 應用程式閘道
description: 了解如何使用 Azure 應用程式閘道來管理應用程式的網路流量。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: 1e80fa23519104c3c62f6a0bf5d65cbbe0848ae2
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443817"
---
# <a name="what-is-azure-application-gateway"></a>什麼是 Azure 應用程式閘道？

Azure 應用程式閘道是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。 傳統負載平衡器會在傳輸層 (OSI 層 4 - TCP 和 UDP) 上運作，並根據來源 IP 位址和連接埠，將流量路由傳送到目的地 IP 位址和連接埠。

![應用程式閘道概念](media/overview/figure1-720.png)

透過應用程式閘道，您可以根據 HTTP 要求的其他屬性 (例如 URI 路徑或主機標頭) 進行路由決策。 例如，您可以根據傳入 URL 路由傳送流量。 因此，如果 `/images` 在傳入的 URL 中，您可以將流量路由傳送至一組針對影像設定的特定伺服器 (也稱為集區)。 如果 `/video` 在此 URL 中，該流量就會路由至另一個針對影片最佳化的集區。

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

這類型的路由也稱為應用程式層 (OSI 層 7) 負載平衡。 Azure 應用程式閘道可以進行 URL 型路由等作業。

>[!NOTE]
> Azure 會為您的案例提供一套完整受控的負載平衡解決方案。 如果您需要高效能、低延遲、Layer 4 負載平衡，請參閱[什麼是 Azure Load Balancer？](../load-balancer/load-balancer-overview.md) 如果您要尋找全域 DNS 負載平衡，請參閱[什麼是流量管理員？](../traffic-manager/traffic-manager-overview.md) 結合這些解決方案，可能有益於您的端對端案例。
>
> 如需 Azure 負載平衡選項的比較，請參閱 [Azure 中的負載平衡選項概觀](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)。

Azure 應用程式閘道包含下列功能︰

## <a name="secure-sockets-layer-ssltls-termination"></a>安全通訊端層 (SSL/TLS) 終止

應用程式閘道支援在閘道上終止 SSL/TLS，之後流量通常會以未加密狀態流至後端伺服器。 這項功能可讓 Web 伺服器不必再負擔昂貴的加密和解密成本。 不過，有時候無法對伺服器進行未加密的通訊。 這可能是因為安全性需求、合規性需求，或應用程式可能只接受安全連線。 對於這些應用程式，應用程式閘道可支援端對端 SSL/TLS 加密。

## <a name="autoscaling"></a>自動調整

Standard_v2 或 WAF_v2 SKU 下的應用程式閘道或 WAF 部署支援自動調整，並且可根據變動的流量負載模式來相應增加或相應減少。 自動調整規模也可讓您在佈建時，無須選擇部署大小或執行個體計數。 如需應用程式閘道 standard_v2 和 WAF_v2 功能的詳細資訊，請參閱[自動調整 v2 SKU](application-gateway-autoscaling-zone-redundant.md)。

## <a name="zone-redundancy"></a>區域備援

Standard_v2 或 WAF_v2 SKU 下的應用程式閘道或 WAF 部署可以跨多個可用性區域、提供更好的錯誤復原能力，且讓您無須在每個區域中佈建個別的應用程式閘道。

## <a name="static-vip"></a>靜態 VIP

Standard_v2 或 WAF_v2 SKU 上的應用程式閘道 VIP 支援獨佔的靜態 VIP 類型。 這可確保與應用程式閘道相關聯的 VIP 即使在應用程式閘道的存留期結束後也不會變更。

## <a name="web-application-firewall"></a>Web 應用程式防火牆

Web 應用程式防火牆 (WAF) 是可集中保護 Web 應用程式的服務，使其免於遭遇常見的攻擊和弱點。 WAF 會根據 [OWASP (Open Web Application Security Project) 核心規則集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (僅限 WAF_v2)、3.0 或 2.2.9 中的規則提供保護。 

Web 應用程式已逐漸成為利用常見已知弱點的惡意攻擊目標。 這些攻擊中最常見的是 SQL 插入式攻擊、跨網站指令碼攻擊等等。 想要防止應用程式的程式碼受到這類攻擊會非常困難，而且可能需要對許多層次的應用程式拓撲執行嚴格的維護、修補和監視工作。 集中式 Web 應用程式防火牆有助於簡化安全性管理作業，且更加確保應用程式管理員能夠對抗威脅或入侵。 相較於保護每個個別的 Web 應用程式，WAF 方案還可透過在中央位置修補已知弱點，更快地因應安全性威脅。 現有的應用程式閘道可以輕易地轉換成已啟用 Web 應用程式防火牆的應用程式閘道。

如需詳細資訊，請參閱[什麼是 Azure Web 應用程式防火牆？](../web-application-firewall/overview.md)。

## <a name="ingress-controller-for-aks"></a>AKS 的輸入控制器
應用程式閘道輸入控制器 (AGIC) 可讓您使用應用程式閘道作為 [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 叢集的輸入。 

輸入控制器會在 AKS 叢集中以 Pod 的形式執行，並取用 [Kubernetes 輸入資源](https://kubernetes.io/docs/concepts/services-networking/ingress/)，然後將其轉換成應用程式閘道設定，讓閘道能夠對 Kubernetes Pod 的流量進行負載平衡。 輸入控制器僅支援應用程式閘道 Standard_v2 和 WAF_v2 SKU。 

如需詳細資訊，請參閱[應用程式閘道輸入控制器 (AGIC)](ingress-controller-overview.md)。

## <a name="url-based-routing"></a>URL 型路由

URL 路徑型路由可讓您根據要求的 URL 路徑，將流量路由傳送至後端伺服器集區。 有一個案例是將對於不同內容類型的要求路由傳送至不同的集區。

例如，對 `http://contoso.com/video/*` 的要求會路由傳送至 VideoServerPool，而對 `http://contoso.com/images/*` 的要求則會路由傳送至 ImageServerPool。 如果沒有任何路徑模式相符，則會選取 DefaultServerPool。

如需詳細資訊，請參閱[使用應用程式閘道的 URL 型路由](https://docs.microsoft.com/azure/application-gateway/url-route-overview) \(機器翻譯\)。

## <a name="multiple-site-hosting"></a>多網站裝載

多網站裝載可讓您在相同的應用程式閘道執行個體上設定多個網站。 此功能可讓您將最多 100 個網站新增到一個應用程式閘道，或 40 個網站用於 WAF (可取得最佳效能)，為您的部署設定更有效率的拓撲。 每個網站都可以導向到自己的集區。 例如，應用程式閘道可以從兩個伺服器集區 (名為 ContosoServerPool 和 FabrikamServerPool) 為 `contoso.com` 和 `fabrikam.com` 處理流量。

對 `http://contoso.com` 的要求會路由傳送至 ContosoServerPool，而對 `http://fabrikam.com` 的要求則會路由傳送至 FabrikamServerPool。

同樣地，相同父系網域的兩個子網域也可以裝載在相同的應用程式閘道部署上。 使用子網域的範例可能包括單一應用程式閘道部署上裝載的 `http://blog.contoso.com` 和 `http://app.contoso.com`。

如需詳細資訊，請參閱[使用應用程式閘道的多網站裝載](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview) \(機器翻譯\)。

## <a name="redirection"></a>重新導向

許多 Web 應用程式的常見案例是支援自動 HTTP 至 HTTPS 的重新導向，以確保應用程式與其使用者之間的通訊會透過加密的路徑進行。

在過去，您會使用一些技巧 (例如建立專屬集區)，其唯一目的是要將其在 HTTP 上接收的要求重新導向至 HTTPS。 應用程式閘道支援在應用程式閘道上重新導向流量的功能。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。 應用程式閘道重新導向支援不僅止於 HTTP 至 HTTPS 的重新導向。 這是一般重新導向機制，因此您可以從使用規則定義的任何連接埠重新導向，或是重新導向至使用規則定義的任何連接埠。 它也支援重新導向至外部網站。

應用程式閘道重新導向提供下列功能：

- 從閘道上的一個連接埠到另一個連接埠的全域重新導向。 這允許在網站上進行 HTTP 至 HTTPS 重新導向。
- 路徑式重新導向。 這類型的重新導向只允許在特定網站區域上進行 HTTP 至 HTTPS 重新導向，例如以 `/cart/*` 表示的購物車區域。
- 重新導向至外部網站。

如需詳細資訊，請參閱[使用應用程式閘道將流量重新導向](https://docs.microsoft.com/azure/application-gateway/redirect-overview) \(機器翻譯\)。

## <a name="session-affinity"></a>工作階段親和性

當您想要在同一個後端保留使用者工作階段時，以 Cookie 為基礎的工作階段親和性非常有用。 使用受閘道管理的 Cookie，應用程式閘道即可將來自使用者工作階段的後續流量導向至同一部伺服器進行處理。 當使用者工作階段的工作階段狀態儲存在伺服器本機時，這項功能很重要。

## <a name="websocket-and-http2-traffic"></a>Websocket 和 HTTP/2 流量

應用程式閘道可對 WebSocket 和 HTTP/2 通訊協定提供原生支援。 使用者無法進行設定來選擇要啟用或停用 WebSocket 支援。

WebSocket 和 HTTP/2 通訊協定都可透過長時間執行的 TCP 連線，讓伺服器與用戶端之間能進行全雙工通訊。 此功能可讓網頁伺服器和用戶端之間進行互動性更高的通訊，此通訊可以是雙向的，而不需要像 HTTP 型實作所要求的進行輪詢。 不同於 HTTP，這些通訊協定的負荷很低，而且可以對多個要求/回應重複使用相同的 TCP 連線，進而提升資源使用效率。 這些通訊協定設計為透過傳統 HTTP 連接埠 80 和 443 進行運作。

如需詳細資訊，請參閱 [WebSocket 支援](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) \(機器翻譯\) 與 [HTTP/2 支援](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) \(機器翻譯\)。

## <a name="connection-draining"></a>清空連線

清空連線可協助您在已規劃的服務更新期間，毫無錯誤地移除後端集區成員。 此設定會透過後端 http 設定啟用，而且可以在規則建立期間套用至後端集區的所有成員。 啟用之後，應用程式閘道可確保所有取消註冊的後端集區執行個體不會再接收任何新要求，但允許在已設定的時間限制內完成現有要求。 這適用於透過使用者設定變更從後端集區中確實移除的後端執行個體，以及根據健康情況探查的判斷，而回報為狀況不良的後端執行個體。 唯一的例外是用於取消註冊執行個體的要求，這些要求已因為閘道管理的工作階段親和性而明確取消註冊，而且會繼續透過 Proxy 傳送至取消註冊執行個體。

如需詳細資訊，請參閱[應用程式閘道組態概觀](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining)的「清空連線」一節。

## <a name="custom-error-pages"></a>自訂錯誤頁面

應用程式閘道可讓您建立自訂的錯誤頁面，而不是顯示預設的錯誤頁面。 您可以使用自訂錯誤頁面來搭配您自己的商標和版面配置。

如需相關資訊，請參閱[自訂錯誤](custom-error.md)。

## <a name="rewrite-http-headers"></a>重新撰寫 HTTP 標頭

HTTP 標頭允許用戶端和伺服器透過要求或回應傳遞其他資訊。 重寫這些 HTTP 標頭可協助您完成幾個重要的案例，例如：

- 新增安全性相關的標頭欄位，例如 HSTS/ X-XSS-Protection。
- 移除可能會顯示機密資訊的回應標頭欄位。
- 從 X-Forwarded-For 標頭中移除連接埠資訊。

Application Gateway 支援在要求及回應封包於用戶端與後端應用程式之間移動時，新增、移除或更新 HTTP 要求及回應標頭的功能。 它還為您提供了新增條件的功能，以確保僅在符合特定條件時才重新寫入指定的標頭。

如需詳細資訊，請參閱[重新撰寫 HTTP 標頭](rewrite-http-headers.md)。

## <a name="sizing"></a>調整大小

應用程式閘道 Standard_v2 和 WAF_v2 SKU 可以設定自動調整或固定大小的部署。 這些 SKU 不會提供不同的執行個體大小。 如需 v2 效能和定價的詳細資訊，請參閱[自動調整 v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing)。

應用程式閘道的標準和 WAF SKU 目前提供三種大小：**小型**、**中型**和**大型**。 小型執行個體大小是針對開發和測試案例。

如需應用程式閘道限制的完整清單，請瀏覽[應用程式閘道服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits)。

下表顯示每個應用程式閘道 v1 執行個體，在啟用 SSL 卸載時的平均效能輸送量：

| 平均後端頁面回應大小 | 小型 | 中 | 大型 |
| --- | --- | --- | --- |
| 6 KB |7.5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> 這些值是應用程式閘道輸送量的近似值。 實際的輸送量會依據不同的環境詳細資料而有所不同，例如平均頁面大小、後端執行個體位置，以及提供一個頁面所需的處理時間。 如需實際效能數字，您需自行執行測試。 這些值僅供容量規劃指引使用。

## <a name="next-steps"></a>後續步驟

視需求和環境而定，您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來建立測試應用程式閘道：

- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure 入口網站](quick-create-portal.md)
- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure PowerShell](quick-create-powershell.md)
- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure CLI](quick-create-cli.md)
