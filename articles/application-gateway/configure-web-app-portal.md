---
title: 使用入口網站管理多租使用者應用程式的流量
titleSuffix: Azure Application Gateway
description: 本文提供如何在現有或新的應用程式閘道上將 Azure App 服務 web 應用程式設定為後端集區成員的指引。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: absha
ms.openlocfilehash: dbaad0f6639d65d88da6847886d3aa3d39b93e82
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563748"
---
# <a name="configure-app-service-with-application-gateway"></a>透過應用程式閘道設定 App Service

由於 app service 是多租使用者服務，而不是專用的部署，因此它會在連入要求中使用主機標頭，以將要求解析至正確的 app service 端點。 通常，應用程式的 DNS 名稱（又是與 app service 面向的應用程式閘道相關聯的 DNS 名稱）與後端應用程式服務的功能變數名稱不同。 因此，應用程式閘道接收的原始要求中的主機標頭與後端服務的主機名稱不同。 基於這個原因，除非從應用程式閘道到後端的要求中的主機標頭變更為後端服務的主機名稱，否則多租使用者後端無法將要求解析為正確的端點。

應用程式閘道會提供一個參數， `Pick host name from backend address` 其會在要求從應用程式閘道路由至後端時，以後端的主機名稱覆寫要求中的主機標頭。 這項功能可支援多租使用者後端，例如 Azure app service 和 API 管理。 

在本文中，您將學會如何：

- 建立後端集區，並在其中新增 App Service
- 使用已啟用「挑選主機名稱」的參數建立 HTTP 設定和自訂探查

## <a name="prerequisites"></a>Prerequisites

- 應用程式閘道：如果您沒有現有的應用程式閘道，請參閱如何 [建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- App service：如果您沒有現有的 App service，請參閱 [app service 檔](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>將 App service 新增為後端集區

1. 在 Azure 入口網站中，開啟您應用程式閘道的設定視圖。

2. 在 [ **後端**集區] 底下，按一下 [ **新增** ] 以建立新的後端集區。

3. 提供適用于後端集區的名稱。 

4. 在 [ **目標**] 底下，按一下下拉式清單，然後選擇 [ **應用程式服務** ] 作為選項。

5. [ **目標**  ] 下拉式清單正下方的下拉式清單隨即出現，其中將包含您的應用程式服務清單。 從這個下拉式清單中，選擇您想要新增為後端集區成員的 App Service，然後按一下 [新增]。

   ![App service 後端](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > 下拉式清單只會填入與您應用程式閘道位於相同訂用帳戶中的應用程式服務。 如果您想要使用與應用程式閘道不同的訂用帳戶中的 app service，請**選擇 [** **IP 位址] 或 [主機名稱**] 選項，並輸入主機名稱 (範例。 **App Services** app service 的 azurewebsites.net) 。

## <a name="create-http-settings-for-app-service"></a>建立 App service 的 HTTP 設定

1. 在 [ **HTTP 設定**] 底下，按一下 [ **新增** ] 以建立新的 HTTP 設定。

2. 輸入 HTTP 設定的名稱，您可以依據您的需求啟用或停用 Cookie 型親和性。

3. 根據您的使用案例，選擇 HTTP 或 HTTPS 通訊協定。 

   > [!NOTE]
   > 如果您選取 HTTPS，就不需要上傳任何驗證憑證或受信任的根憑證，即可允許 app service 後端，因為 app service 是受信任的 Azure 服務。

4. 核取用於 **App Service** 的方塊。 請注意，參數  `Create a probe with pick host name from backend address` 和 `Pick host name from backend address` 將會自動啟用。`Pick host name from backend address` 將要求從應用程式閘道路由傳送到後端時，會使用後端的主機名稱覆寫要求中的主機標頭。  

   `Create a probe with pick host name from backend address` 會自動建立健康情況探查，並將其與此 HTTP 設定產生關聯。 您不需要針對此 HTTP 設定建立任何其他健康情況探查。 您可以檢查 <HTTP Setting name> <Unique GUID> 健康情況探查清單中是否已新增具有該名稱的新探查，而且已經有參數 `Pick host name from backend http settings enabled` 。

   如果您已經有用於 App service 的一或多個 HTTP 設定，而且這些 HTTP 設定使用的通訊協定與您正在建立的通訊協定相同，則而不是使用 `Create a probe with pick host name from backend address` 參數，則您會看到一個下拉式清單，以選取其中一個自訂探查。 這是因為應用程式服務已經有 HTTP 設定，因此也會存在具有參數的健康狀態探查 `Pick host name from backend http settings enabled` 。 從下拉式清單中選擇該自訂探查。

5. 按一下 **[確定]** 以建立 HTTP 設定。

   ![螢幕擷取畫面顯示 [新增 H T P P 設定] 窗格，其中已選取 [App service] 和 [確定]。](./media/configure-web-app-portal/http-setting1.png)

   ![螢幕擷取畫面顯示已選取「挑選主機名稱」表單後端 HTTP 設定的健康情況探查。](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>建立規則以結合接聽程式、後端集區和 HTTP 設定

1. 在 [ **規則**] 底下，按一下 [ **基本** ] 以建立新的基本規則。

2. 提供適當的名稱，並選取將接受 App service 內送要求的接聽程式。

3. 在 [ **後端集** 區] 下拉式清單中，選擇您先前建立的後端集區。

4. 在 [ **HTTP 設定** ] 下拉式清單中，選擇您先前建立的 HTTP 設定。

5. 按一下 **[確定]** 儲存此規則。

   ![螢幕擷取畫面顯示 [新增基本規則] 窗格，其中已醒目提示接聽程式、後端集區和 H T T P 設定。](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>重新導向至 app service 的相對路徑時的其他設定

當 app service 傳送重新導向回應給用戶端以重新導向至其相對路徑時 (例如，從 contoso.azurewebsites.net/path1 重新導向至 contoso.azurewebsites.net/path2) ，它會在其回應的 location 標頭中使用與從應用程式閘道收到的要求中相同的主機名稱。 因此，用戶端會直接向 contoso.azurewebsites.net/path2 提出要求，而不是透過應用程式閘道 (contoso.com/path2) 。 略過應用程式閘道不是理想的做法。

如果在您的使用案例中，應用程式服務必須將重新導向回應傳送給用戶端，請執行 [其他步驟來重寫位置標頭](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)。

## <a name="restrict-access"></a>限制存取

在這些範例中部署的 Web 應用程式會使用可直接從網際網路存取的公用 IP 位址。 這在您了解新功能及嘗試新事物時，可協助進行疑難排解。 但是，如果您想要將一項功能部署到生產環境，則需要新增更多限制。

您可以限制存取 Web 應用程式的其中一個方法是使用 [Azure App Service 靜態 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，您可以限制 Web 應用程式，使它只會從應用程式閘道接收流量。 您可以使用應用程式服務 IP 限制功能，將應用程式閘道 VIP 列為具有存取權的唯一位址。

## <a name="next-steps"></a>後續步驟

若要深入瞭解應用程式閘道的 App service 和其他多租使用者支援，請參閱 [使用應用程式閘道的多租使用者服務支援](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。
