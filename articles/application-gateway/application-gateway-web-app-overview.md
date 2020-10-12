---
title: 多租使用者後端
titleSuffix: Azure Application Gateway
description: 本頁面提供多租用戶後端的應用程式閘道支援概觀。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 308098bd1ac49510afccf0a7964face726906332
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84628681"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>應用程式閘道支援多租使用者後端，例如 App service

在 web 伺服器的多租使用者架構設計中，有多個網站正在相同的 web 伺服器實例上執行。 主機名稱是用來區分所裝載的不同應用程式。 根據預設，應用程式閘道不會變更來自用戶端的連入 HTTP 主機標頭，而會將標頭原封不動地傳送回後端。 這適用于後端集區成員，例如 Nic、虛擬機器擴展集、公用 IP 位址、內部 IP 位址和 FQDN，因為這些成員不依賴特定主機標頭或 SNI 擴充功能來解析為正確的端點。 不過，有許多服務（例如 Azure App 服務 web 應用程式和 Azure API 管理）本質上為多租使用者，並且依賴特定主機標頭或 SNI 擴充功能來解析為正確的端點。 通常，應用程式的 DNS 名稱（又是與應用程式閘道相關聯的 DNS 名稱）與後端服務的功能變數名稱不同。 因此，應用程式閘道接收的原始要求中的主機標頭與後端服務的主機名稱不同。 基於這個原因，除非從應用程式閘道到後端的要求中的主機標頭變更為後端服務的主機名稱，否則多租使用者後端無法將要求解析為正確的端點。 

應用程式閘道有個功能可讓使用者根據後端的主機名稱，覆寫要求中的 HTTP 主機標頭。 這項功能可支援多租用戶後端，例如 Azure Web 應用程式和 API 管理。 這項功能同時適用於 v1 和 v2 標準及 WAF SKU。 

![主機覆寫](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> 這不適用於 (ASE) 的 Azure App service 環境，因為 ASE 是專用資源，不同于 Azure App 服務，這是多租使用者資源。

## <a name="override-host-header-in-the-request"></a>覆寫要求中的主機標頭

指定主機覆寫的功能定義于 [HTTP 設定](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) 中，並可在規則建立期間套用至任何後端集區。 支援下列兩種覆寫多租使用者後端主機標頭和 SNI 延伸的方法：

- 能夠將主機名稱設定為在 HTTP 設定中明確輸入的固定值。 這項功能可確保針對套用特定 HTTP 設定的後端集區，將所有流量的主機標頭覆寫為此值。 使用端對端 TLS 時，會在 SNI 擴充功能中使用此覆寫的主機名稱。 這項功能可促成以下案例：其中的後端集區伺服器陣列預期會有不同於連入客戶主機標頭的主機標頭。

- 從後端集區成員的 IP 或 FQDN 衍生主機名稱的功能。 如果使用從個別後端集區成員衍生主機名稱的選項進行設定，HTTP 設定也會提供從後端集區成員的 FQDN 動態挑選主機名稱的選項。 使用端對端 TLS 時，此主機名稱衍生自 FQDN，並用於 SNI 擴充功能。 這項功能可促成以下案例：其中的後端集區可以有兩個或多個多租用戶 PaaS 服務，例如 Azure Web 應用程式，而且每個成員的要求主機標頭包含衍生自其 FQDN 的主機名稱。 針對此案例，我們會使用 HTTP 設定中的參數，稱為 [從後端位址挑選主機名稱](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) ，這會將原始要求中的主機標頭動態覆寫至後端集區中提及的主機標頭。  例如，如果您的後端集區 FQDN 包含 "contoso11.azurewebsites.net" 和 "contoso22.azurewebsites.net"，則當要求傳送到適當的後端伺服器時，contoso.com 的原始要求主機標頭會被覆寫為 contoso11.azurewebsites.net 或 contoso22.azurewebsites.net。 

  ![Web 應用程式案例](./media/application-gateway-web-app-overview/scenario.png)

使用這項功能，客戶可將 HTTP 設定中的選項和自訂探查指定為適當的組態。 這項設定會接著使用規則繫結至接聽程式和後端集區。

## <a name="special-considerations"></a>特殊考量

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>使用多租使用者服務的 TLS 終止和端對端 TLS

多租使用者服務支援 TLS 終止和端對端 TLS 加密。 針對應用程式閘道上的 TLS 終止，TLS 憑證會繼續需要新增至應用程式閘道接聽程式。 不過，在端對端 TLS 的情況下，受信任的 Azure 服務（例如 Azure App service web apps）不需要在應用程式閘道中允許後端。 因此，不需要新增任何驗證憑證。 

![端對端 TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

請注意，在上圖中，選取 [App service] 作為 [後端] 時，不需要新增驗證憑證。

### <a name="health-probe"></a>健全狀況探查

覆寫 **HTTP 設定** 中的主機標頭，只會影響要求及其路由。 它不會影響健康情況探查行為。 若要讓端對端功能運作，必須修改探查和 HTTP 設定，以反映正確的組態。 除了提供在探查設定中指定主機標頭的能力之外，自訂探查也支援從目前設定的 HTTP 設定衍生主機標頭的能力。 在探查組態中使用 `PickHostNameFromBackendHttpSettings` 參數即可指定此組態。

### <a name="redirection-to-app-services-url-scenario"></a>重新導向至 App Service 的 URL 案例

在某些情況下，App service 回應中的主機名稱可能會將終端使用者瀏覽器導向至 *. azurewebsites.net 主機名稱，而不是與應用程式閘道相關聯的網域。 下列情況可能會發生此問題：

- 您已在 App Service 上設定重新導向。 重新導向可以像在要求中加上尾端斜線一樣簡單。
- 您有 Azure AD 的驗證會導致重新導向。

若要解決這類情況，請參閱針對重新導向 [至 App service 的 URL 問題進行疑難排解](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。

## <a name="next-steps"></a>接下來的步驟

瞭解如何設定具有多租使用者應用程式的應用程式閘道，例如 Azure App 服務 web 應用程式作為後端集區成員，方法是造訪 [設定 App Service web 應用程式與應用程式閘道](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
