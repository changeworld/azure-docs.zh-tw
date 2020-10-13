---
title: 使用流量管理員設定 DNS 名稱
description: 瞭解如何為與流量管理員整合以進行負載平衡的 Azure App Service 應用程式設定自訂網域。
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 0e8d5fa14678a2a26234dfcd73f4a50af62ca7aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962871"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>使用流量管理員整合在 Azure App Service 中設定自訂功能變數名稱

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> 若為雲端服務，請參閱[設定 Azure 雲端服務的自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。

當您使用[Azure 流量管理員](../traffic-manager/index.yml)對[Azure App Service](overview.md)的流量進行負載平衡時，可使用** \<traffic-manager-endpoint> trafficmanager.net**存取 App Service 應用程式。 您可以使用 App Service 應用程式指派自訂功能變數名稱，例如 www \. contoso.com，以便為您的使用者提供更容易辨識的功能變數名稱。

本文說明如何使用與 [流量管理員](../traffic-manager/traffic-manager-overview.md)整合的 App Service 應用程式來設定自訂功能變數名稱。

> [!NOTE]
> 當您使用流量管理員端點設定功能變數名稱時，僅支援 [CNAME](https://en.wikipedia.org/wiki/CNAME_record) 記錄。 由於不支援記錄，因此也不支援根域對應（例如 contoso.com）。
> 

## <a name="prepare-the-app"></a>準備應用程式

若要將自訂 DNS 名稱對應至與 Azure 流量管理員整合的應用程式，web 應用程式的 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/) 必須是 **標準** 層或更高的版本。 在此步驟中，您要確定 App Service 應用程式位於支援的定價層。

### <a name="check-the-pricing-tier"></a>檢查定價層

在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [ **應用程式服務**]。

在 [應用程式服務] 頁面上，選取您的 Azure 應用程式名稱。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/select-app.png)

在 [應用程式] 頁面的左側導覽中，選取 [ **擴大 (] App Service 方案) **。

![相應增加功能表](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

會以藍色框線醒目顯示應用程式目前的層。 請檢查以確定應用程式位於 **標準** 層或更高版本， (**生產** 或 **隔離** 類別) 中的任何層。 如果是，請關閉 [ **擴大** ] 頁面，並略過 [建立 CNAME 對應](#create-the-cname-mapping)。

![檢查定價層](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>擴大 App Service 方案

如果您需要擴大您的應用程式，請選取 [ **生產** ] 類別中的任何定價層。 如需其他選項，請按一下 [查看其他選項]。

按一下 [套用]。

## <a name="create-traffic-manager-endpoint"></a>建立流量管理員端點

遵循 [新增或刪除端點](../traffic-manager/traffic-manager-manage-endpoints.md)的步驟，將您的 App Service 應用程式新增為流量管理員設定檔中的端點。

一旦您的 App Service 應用程式在支援的定價層中，當您新增端點時，它會顯示在可用的 App Service 目標清單中。 如果未列出您的應用程式，請 [確認您應用程式的定價層](#prepare-the-app)。

## <a name="create-the-cname-mapping"></a>建立 CNAME 對應
> [!NOTE]
> 若要設定 [您所購買的 App Service 網域](manage-custom-dns-buy-domain.md)，請略過本節並移至 [ [啟用自訂網域](#enable-custom-domain)]。
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

雖然每個網域提供者的細節各有不同，但您會*從*[非根自訂 (功能變數名稱](#what-about-root-domains)（例如**www.contoso.com**) ）對應*到*與您的應用程式整合 (的流量管理員功能變數名稱**contoso.trafficmanager.net**) 。 

> [!NOTE]
> 如果記錄已在使用中，而您需要事先將您的應用程式繫結到該記錄，您可以建立其他的 CNAME 記錄。 例如，若要事先將 **www \. contoso.com** 系結至您的應用程式，請建立從 **AWVERIFY** 到 **contoso.trafficmanager.net**的 CNAME 記錄。 然後，您可以將 "www \. contoso.com" 新增至您的應用程式，而不需要變更 "www" CNAME 記錄。 如需詳細資訊，請參閱 [將 ACTIVE DNS 名稱遷移至 Azure App Service](manage-custom-dns-migrate-domain.md)。

在網域提供者處完成 DNS 記錄的新增或修改後，請儲存變更。

### <a name="what-about-root-domains"></a>根域呢？

因為流量管理員僅支援使用 CNAME 記錄進行自訂網域對應，且 DNS 標準不支援對應根域的 CNAME 記錄 (例如， **contoso.com**) ，流量管理員不支援對應至根域。 若要解決此問題，請使用應用層級的 URL 重新導向。 例如，在 ASP.NET Core 中，您可以使用 [URL 重寫](/aspnet/core/fundamentals/url-rewriting)。 然後，使用流量管理員來平衡子域 (**www.contoso.com**) 的負載。

在高可用性案例中，您可以建立多個從根域指向每個應用程式複本 IP 位址的 *記錄* ，以在不使用流量管理員的情況下執行容錯 DNS 設定。 然後，將 [相同的根域對應至所有應用程式複本](app-service-web-tutorial-custom-domain.md#map-an-a-record)。 因為相同的功能變數名稱無法對應到相同區域中的兩個不同應用程式，所以只有當您的應用程式複本位於不同區域時，此設定才會運作。

## <a name="enable-custom-domain"></a>啟用自訂網域
在您的功能變數名稱記錄傳播之後，請使用瀏覽器來確認您的自訂功能變數名稱會解析成您的 App Service 應用程式。

> [!NOTE]
> 需要一些時間，CNAME 才能傳播至整個 DNS 系統。 您可以使用之類的服務 <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> 來驗證 CNAME 是否可用。
> 
> 

1. 一旦網域解析成功之後，回到[Azure 入口網站](https://portal.azure.com)中的應用程式頁面
2. 從左側導覽中，選取 [**自訂網域**  >  **新增主機名稱**]。
4. 輸入您稍早對應的自訂功能變數名稱，然後選取 [ **驗證**]。
5. 確定 [主機名稱記錄類型] 已設定為 [CNAME (www\.example.com 或任何子網域)]。

6. 由於 App Service 應用程式現在已與流量管理員端點整合，因此您應該會在 [ **CNAME**設定] 下看到流量管理員功能變數名稱。 選取它，然後按一下 [ **新增自訂網域**]。

    ![將 DNS 名稱新增至應用程式](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Azure App Service 中使用 SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)