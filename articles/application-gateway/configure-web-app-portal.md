---
title: 使用入口網站管理多租使用者應用程式的流量
titleSuffix: Azure Application Gateway
description: 本文提供如何在現有或新的應用程式閘道上將 Azure App 服務 web 應用程式設定為後端集區成員的指引。
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: c538b6e4cf27b992500c079ad9c914c05d55eb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854905"
---
# <a name="configure-app-service-with-application-gateway"></a>透過應用程式閘道設定 App Service

由於應用程式服務是多租用戶服務，而不是專用部署，因此會使用連入要求中的主機標頭來將要求解析為正確的應用程式服務端點。 通常，應用程式的 DNS 名稱（又是與 app service 面向的應用程式閘道相關聯的 DNS 名稱）與後端應用程式服務的功能變數名稱不同。 因此，應用程式閘道接收的原始要求中的主機標頭與後端服務的主機名稱不同。 基於這個原因，除非從應用程式閘道到後端的要求中的主機標頭變更為後端服務的主機名稱，否則多租使用者後端無法將要求解析為正確的端點。

應用程式閘道會提供一個參數， `Pick host name from backend target` 其會在要求從應用程式閘道路由至後端時，以後端的主機名稱覆寫要求中的主機標頭。 這項功能可支援多租使用者後端，例如 Azure app service 和 API 管理。 

在本文中，您將學會如何：

- 編輯後端集區，並在其中新增 App Service
- 已啟用 [挑選主機名稱] 切換的 [編輯 HTTP 設定]

## <a name="prerequisites"></a>必要條件

- 應用程式閘道：建立不具後端集區目標的應用程式閘道。 如需詳細資訊，請參閱 [快速入門：使用 Azure 應用程式閘道引導網路流量-Azure 入口網站](quick-create-portal.md)

- App service：如果您沒有現有的 App service，請參閱 [app service 檔](../app-service/index.yml)。

## <a name="add-app-service-as-backend-pool"></a>將 App service 新增為後端集區

1. 在 [Azure 入口網站中，選取您的應用程式閘道。

2. 在 [ **後端** 集區] 下，選取後端集區。

4. 在 [ **目標型別**] 下，選取 [ **應用程式服務**]。

5. 在 [ **目標** ] 底下，選取您的 App Service。

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="App service 後端":::
   
   > [!NOTE]
   > 下拉式清單只會填入與您應用程式閘道位於相同訂用帳戶中的應用程式服務。 如果您想要使用與應用程式閘道不同的訂用帳戶中的 app service，請 **選擇 [** **IP 位址] 或 [主機名稱**] 選項，並輸入主機名稱 (範例。  app service 的 azurewebsites.net) 。
1. 選取 [儲存]。

## <a name="edit-http-settings-for-app-service"></a>編輯 App Service 的 HTTP 設定

1. 在 [ **Http 設定**] 底下，選取現有的 HTTP 設定。

2. 在 [ **以新的主機名稱覆寫**] 下，選取 **[是]**。
3. 在 [ **主機名稱覆寫**] 下，選取 [ **從後端目標挑選主機名稱**]。
4. 選取 [儲存]。

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="從後端 HTTP 設定挑選主機名稱":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>重新導向至 app service 的相對路徑時的其他設定

當 app service 傳送重新導向回應給用戶端以重新導向至其相對路徑時 (例如，從重新導向 `contoso.azurewebsites.net/path1` 至 `contoso.azurewebsites.net/path2`) 時，它會在其回應的位置標頭中使用與從應用程式閘道收到的要求中相同的主機名稱。 因此，用戶端會直接提出要求， `contoso.azurewebsites.net/path2` 而不是透過應用程式閘道 (`contoso.com/path2`) 。 略過應用程式閘道不是理想的做法。

如果在您的使用案例中，應用程式服務必須將重新導向回應傳送給用戶端，請執行 [其他步驟來重寫位置標頭](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration)。

## <a name="restrict-access"></a>限制存取

在這些範例中部署的 Web 應用程式會使用可直接從網際網路存取的公用 IP 位址。 這在您了解新功能及嘗試新事物時，可協助進行疑難排解。 但是，如果您想要將一項功能部署到生產環境，則需要新增更多限制。

您可以限制存取 Web 應用程式的其中一個方法是使用 [Azure App Service 靜態 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，您可以限制 Web 應用程式，使它只會從應用程式閘道接收流量。 您可以使用應用程式服務 IP 限制功能，將應用程式閘道 VIP 列為具有存取權的唯一位址。

## <a name="next-steps"></a>後續步驟

若要深入瞭解應用程式閘道的 App service 和其他多租使用者支援，請參閱 [使用應用程式閘道的多租使用者服務支援](./application-gateway-web-app-overview.md)。
