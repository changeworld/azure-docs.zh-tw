---
title: 多租戶後端
titleSuffix: Azure Application Gateway
description: 本頁面提供多租用戶後端的應用程式閘道支援概觀。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075182"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>對多租戶後端（如應用服務）的應用程式閘道支援

在 Web 服務器中的多租戶體系結構設計中，多個網站正在同一 Web 服務器實例上運行。 主機名稱用於區分託管的不同應用程式。 根據預設，應用程式閘道不會變更來自用戶端的連入 HTTP 主機標頭，而會將標頭原封不動地傳送回後端。 這非常適合後端池成員，如 NIC、虛擬機器規模集、公共 IP 位址、內部 IP 位址和 FQDN，因為這些不依賴于特定的主機標頭或 SNI 擴展解析到正確的終結點。 但是，有許多服務，如 Azure 應用服務 Web 應用和 Azure API 管理，這些服務本質上是多租戶的，並且依賴于特定的主機標頭或 SNI 擴展解析為正確的終結點。 通常，應用程式的 DNS 名稱（另一種是與應用程式閘道關聯的 DNS 名稱）與後端服務的功能變數名稱不同。 因此，應用程式閘道收到的原始請求中的主機標頭與後端服務的主機名稱不同。 因此，除非請求從應用程式閘道到後端的主機標頭更改為後端服務的主機名稱，否則多租戶後端無法將請求解析為正確的終結點。 

應用程式閘道有個功能可讓使用者根據後端的主機名稱，覆寫要求中的 HTTP 主機標頭。 這項功能可支援多租用戶後端，例如 Azure Web 應用程式和 API 管理。 這項功能同時適用於 v1 和 v2 標準及 WAF SKU。 

![主機覆蓋](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> 這不適用於 Azure 應用服務環境 （ASE），因為 ASE 是專用資源，與多租戶資源的 Azure 應用服務不同。

## <a name="override-host-header-in-the-request"></a>覆蓋請求中的主機標頭

指定主機覆蓋的功能在[HTTP 設置](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)中定義，可以在規則創建期間應用於任何後端池。 支援以下兩種跨占主機標頭和多租戶後端的 SNI 擴展的方法：

- 將主機名稱設置為 HTTP 設置中顯式輸入的固定值的功能。 此功能可確保將主機標頭覆蓋到應用特定 HTTP 設置的後端池的所有流量的此值。 使用端對端 SSL 時，這個覆寫的主機名稱會使用於 SNI 擴充功能。 這項功能可促成以下案例：其中的後端集區伺服器陣列預期會有不同於連入客戶主機標頭的主機標頭。

- 從後端集區成員的 IP 或 FQDN 衍生主機名稱的功能。 HTTP 設置還提供一個選項，用於從後端池成員的 FQDN 動態選取主機名稱（如果配置了從單個後端池成員派生主機名稱的選項）。 使用端對端 SSL 時，此主機名稱衍生自 FQDN 且使用於 SNI 擴充功能。 這項功能可促成以下案例：其中的後端集區可以有兩個或多個多租用戶 PaaS 服務，例如 Azure Web 應用程式，而且每個成員的要求主機標頭包含衍生自其 FQDN 的主機名稱。 為了實現此方案，我們使用 HTTP 設置中的一個開關，稱為[從後端位址選取主機名稱](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)，該開關會動態地將原始請求中的主機標頭覆蓋後端池中提及的主機標頭。  例如，如果後端池 FQDN 包含"contoso11.azurewebsites.net"和"contoso22.azurewebsites.net"，則contoso.com的原始請求的主機標頭將被重寫為contoso11.azurewebsites.net或contoso22.azurewebsites.net當要求傳送到相應的後端伺服器時。 

  ![Web 應用程式案例](./media/application-gateway-web-app-overview/scenario.png)

使用這項功能，客戶可將 HTTP 設定中的選項和自訂探查指定為適當的組態。 這項設定會接著使用規則繫結至接聽程式和後端集區。

## <a name="special-considerations"></a>特殊考量

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL 終止和帶多租戶服務的端到端 SSL

多租戶服務都支援 SSL 終止和端到端 SSL 加密。 對於應用程式閘道的 SSL 終止，仍需要將 SSL 憑證添加到應用程式閘道攔截器中。 但是，在端到端 SSL 的情況下，受信任的 Azure 服務（如 Azure App 服務 Web 應用）不需要將後端列入應用程式閘道中的白名單。 因此，無需添加任何身份驗證憑證。 

![端到端 SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

請注意，在上圖中，當選擇 App 服務作為後端時，無需添加身份驗證憑證。

### <a name="health-probe"></a>健全狀況探查

在**HTTP 設置**中重寫主機標頭只會影響請求及其路由。 它不會影響運行狀況探測行為。 若要讓端對端功能運作，必須修改探查和 HTTP 設定，以反映正確的組態。 除了提供在探測配置中指定主機標頭的功能外，自訂探測還支援從當前配置的 HTTP 設置派生主機標頭的功能。 在探查組態中使用 `PickHostNameFromBackendHttpSettings` 參數即可指定此組態。

### <a name="redirection-to-app-services-url-scenario"></a>重定向到應用服務的 URL 方案

在某些情況下，應用服務回應中的主機名稱可能會將最終使用者瀏覽器定向到 *.azurewebsites.net 主機名稱，而不是與應用程式閘道關聯的域。 在：

- 您在應用服務上配置了重定向。 重定向可以像向請求添加尾隨斜杠一樣簡單。
- 您具有導致重定向的 Azure AD 身份驗證。

要解決此類情況，請參閱[將疑難排解重定向到應用服務的 URL 問題](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。

## <a name="next-steps"></a>後續步驟

瞭解如何使用多租戶應用（如 Azure App 服務 Web 應用）將應用程式閘道設置為後端池成員，通過訪問[具有應用程式閘道的"配置應用服務 Web 應用](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)"
