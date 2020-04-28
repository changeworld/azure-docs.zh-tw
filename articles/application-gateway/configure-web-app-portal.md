---
title: 使用入口網站管理多租使用者應用程式的流量
titleSuffix: Azure Application Gateway
description: 本文提供如何在現有或新的應用程式閘道上，將 Azure App 服務 web 應用程式設定為後端集區成員的指導方針。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075169"
---
# <a name="configure-app-service-with-application-gateway"></a>透過應用程式閘道設定 App Service

由於 app service 是多租使用者服務，而不是專用的部署，因此它會在連入要求中使用主機標頭，以將要求解析為正確的 app service 端點。 通常，應用程式的 DNS 名稱（也就是與 app service 面向的應用程式閘道相關聯的 DNS 名稱）與後端 app 服務的功能變數名稱不同。 因此，應用程式閘道收到的原始要求中的主機標頭與後端服務的主機名稱不同。 因此，除非從應用程式閘道到後端的要求中的主機標頭變更為後端服務的主機名稱，否則多租使用者後端無法將要求解析為正確的端點。

應用程式閘道提供一個稱為`Pick host name from backend address`的交換器，其會在要求從應用程式閘道路由傳送至後端時，以後端的主機名稱覆寫要求中的主機標頭。 這項功能可支援多租使用者後端，例如 Azure app service 和 API 管理。 

在本文中，您將學會如何：

> [!div class="checklist"]
>
> - 建立後端集區，並在其中新增 App Service
> - 建立 HTTP 設定和自訂探查並啟用「挑選主機名稱」交換器

## <a name="prerequisites"></a>Prerequisites

- 應用程式閘道：如果您沒有現有的應用程式閘道，請參閱如何[建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- App service：如果您沒有現有的 App service，請參閱[app service 檔](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>將應用程式服務新增為後端集區

1. 在 [Azure 入口網站中，開啟應用程式閘道的 [設定] 視圖。

2. 在 [**後端**集區] 底下，按一下 [**新增**] 以建立新的後端集區。

3. 為後端集區提供適當的名稱。 

4. 在 [**目標**] 底下，按一下下拉式清單，然後選擇 [**應用程式服務**] 做為選項。

5. [**目標**] 下拉式清單正下方的下拉式清單隨即出現，其中會包含您的應用程式服務清單。 從這個下拉式清單中，選擇您要新增為後端集區成員的 App Service，然後按一下 [新增]。

   ![App service 後端](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > 下拉式清單只會填入與您的應用程式閘道位於相同訂用帳戶中的應用程式服務。 如果您想要使用的應用程式服務與應用程式閘道的訂用帳戶不同，請選擇 [ **IP 位址或主機名稱**] 選項並輸入主機名稱（例如，），而不是在 [**目標**] 下拉式清單中選擇 [**應用程式服務**]。 azurewebsites.net）。

## <a name="create-http-settings-for-app-service"></a>建立 App service 的 HTTP 設定

1. 在 [ **HTTP 設定**] 底下 **，按一下 [新增]** 以建立新的 HTTP 設定。

2. 輸入 HTTP 設定的名稱，您可以依據您的需求啟用或停用 Cookie 型親和性。

3. 依據您的使用案例，選擇 HTTP 或 HTTPS 通訊協定。 

   > [!NOTE]
   > 如果您選取 [HTTPS]，則不需要上傳任何驗證憑證或受信任的根憑證，即可將 app service 後端列入允許清單，因為 app service 是受信任的 Azure 服務。

4. 勾選 [用於**App Service**的方塊。 請注意，和`Create a probe with pick host name from backend address` `Pick host name from backend address`參數會自動啟用。`Pick host name from backend address` 將要求從應用程式閘道路由傳送至後端時，會以後端的主機名稱覆寫要求中的主機標頭。  

   `Create a probe with pick host name from backend address`會自動建立健康狀態探查，並將其與此 HTTP 設定產生關聯。 您不需要為此 HTTP 設定建立任何其他健全狀況探查。 您可以檢查是否已在健康情況探查清單<HTTP Setting name> <Unique GUID>中新增名稱為的新探查，而且它已經有參數`Pick host name from backend http settings enabled`。

   如果您已經有一或多個用於 App service 的 HTTP 設定，而這些 HTTP 設定使用的通訊協定與您在所建立的相同， `Create a probe with pick host name from backend address`則您會看到一個下拉式清單來選取其中一個自訂探查。 這是因為應用程式服務已經有 HTTP 設定，因此也有一個具有交換器`Pick host name from backend http settings enabled`的健全狀況探查。 從下拉式清單中選擇自訂探查。

5. 按一下 **[確定]** 以建立 HTTP 設定。

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>建立規則以系結接聽程式、後端集區和 HTTP 設定

1. 在 [**規則**] 底下，按一下 [**基本**] 以建立新的基本規則。

2. 提供適當的名稱，然後選取將接受 App service 傳入要求的接聽程式。

3. 在 [**後端集**區] 下拉式清單中，選擇您先前建立的後端集區。

4. 在 [ **HTTP 設定**] 下拉式清單中，選擇您在上面建立的 HTTP 設定。

5. 按一下 **[確定]** 以儲存此規則。

   ![規則](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>重新導向至 app service 的相對路徑時的其他設定

當 app service 將重新導向回應傳送至用戶端以重新導向至其相對路徑（例如，從 contoso.azurewebsites.net/path1 重新導向至 contoso.azurewebsites.net/path2）時，它會在其回應的位置標頭中使用相同的主機名稱，如同它從應用程式閘道收到的要求。 因此，用戶端會直接向 contoso.azurewebsites.net/path2 提出要求，而不是透過應用程式閘道（contoso.com/path2）。 不需要略過應用程式閘道。

如果您使用的是，應用程式服務必須將重新導向回應傳送至用戶端的案例，請執行[額外的步驟來重寫 location 標頭](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)。

## <a name="restrict-access"></a>限制存取

在這些範例中部署的 Web 應用程式會使用可直接從網際網路存取的公用 IP 位址。 這在您了解新功能及嘗試新事物時，可協助進行疑難排解。 但是，如果您想要將一項功能部署到生產環境，則需要新增更多限制。

您可以限制存取 Web 應用程式的其中一個方法是使用 [Azure App Service 靜態 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，您可以限制 Web 應用程式，使它只會從應用程式閘道接收流量。 您可以使用應用程式服務 IP 限制功能，將應用程式閘道 VIP 列為具有存取權的唯一位址。

## <a name="next-steps"></a>後續步驟

若要深入瞭解應用程式閘道的 App service 和其他多租使用者支援，請參閱[應用程式閘道的多租使用者服務支援](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。
