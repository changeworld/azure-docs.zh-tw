---
title: 使用門戶管理多租戶應用的流量
titleSuffix: Azure Application Gateway
description: 本文提供有關如何在現有或新應用程式閘道上的後端池中配置 Azure App 服務 Web 應用的指導。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075169"
---
# <a name="configure-app-service-with-application-gateway"></a>透過應用程式閘道設定 App Service

由於應用服務是多租戶服務而不是專用部署，因此它在傳入請求中使用主機標頭將請求解析為正確的應用服務終結點。 通常，應用程式的 DNS 名稱（其反過來是與應用服務前面的應用程式閘道關聯的 DNS 名稱）與後端應用服務的功能變數名稱不同。 因此，應用程式閘道收到的原始請求中的主機標頭與後端服務的主機名稱不同。 因此，除非請求從應用程式閘道到後端的主機標頭更改為後端服務的主機名稱，否則多租戶後端無法將請求解析為正確的終結點。

應用程式閘道提供一個稱為`Pick host name from backend address`的交換器，當請求從應用程式閘道路由到後端時，該交換器使用後端的主機名稱覆蓋請求中的主機標頭。 此功能支援多租戶後端，如 Azure 應用服務和 API 管理。 

在本文中，您將學會如何：

> [!div class="checklist"]
>
> - 創建後端池並將應用服務添加到其中
> - 啟用"選取主機名稱"開關創建 HTTP 設置和自訂探測

## <a name="prerequisites"></a>Prerequisites

- 應用程式閘道：如果您沒有現有的應用程式閘道，請參閱如何[創建應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- 應用服務：如果您沒有現有的應用服務，請參閱[應用服務文件](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>將應用服務添加為後端池

1. 在 Azure 門戶中，打開應用程式閘道的配置視圖。

2. 在**後端池**下，按一下"**添加"** 以創建新的後端池。

3. 為後端池提供合適的名稱。 

4. 在 **"目標**"下，按一下下拉清單並選擇**應用服務**作為選項。

5. 下拉清單將緊鄰 **"目標**"下拉清單，其中包含應用服務的清單。 在此下拉下清單中，選擇要添加為後端池成員的應用服務，然後按一下"添加"。

   ![應用服務後端](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > 下拉清單將僅填充與應用程式閘道處於相同訂閱中的應用服務。 如果要使用與應用程式閘道不同的訂閱中的應用服務，則而不是在 **"目標"** 下拉下下下下選擇**應用服務**，而是選擇**IP 位址或主機名稱**選項並輸入主機名稱（例如）。 應用服務azurewebsites.net）。

## <a name="create-http-settings-for-app-service"></a>為應用服務創建 HTTP 設置

1. 在**HTTP 設置**下，按一下"**添加**"以創建新的 HTTP 設置。

2. 輸入 HTTP 設置的名稱，您可以根據您的要求啟用或禁用基於 Cookie 的關聯。

3. 根據用例選擇協定為 HTTP 或 HTTPS。 

   > [!NOTE]
   > 如果選擇 HTTPS，則無需上載任何身份驗證憑證或受信任的根憑證來將應用服務後端列入白名單，因為應用服務是受信任的 Azure 服務。

4. 選中"**用於應用服務**"核取方塊。 請注意，開關`Create a probe with pick host name from backend address`將自動`Pick host name from backend address`啟用。`Pick host name from backend address` 當請求從應用程式閘道路由到後端時，將用後端的主機名稱覆蓋請求中的主機標頭。  

   `Create a probe with pick host name from backend address`將自動創建運行狀況探測並將其關聯到此 HTTP 設置。 您無需為此 HTTP 設置創建任何其他運行狀況探測。 您可以檢查在運行狀況探測器清單中添加了名稱<HTTP Setting name><Unique GUID>的新探測器，並且該探測器已具有開關`Pick host name from backend http settings enabled`。

   如果您已經有一個或多個用於應用服務的 HTTP 設置，並且這些 HTTP 設置使用的協定與您正在創建的協定中的協定相同，則您將獲得`Create a probe with pick host name from backend address`一個下拉式功能表以選擇其中一個自訂探測器。 這是因為由於已經存在一個帶有應用服務的 HTTP 設置，因此，還將存在具有開關`Pick host name from backend http settings enabled`的運行狀況探測。 從下拉清單中選擇該自訂探測。

5. 按一下"**確定"** 以創建 HTTP 設置。

   ![HTTP 設置1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP 設置2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>創建規則以綁定攔截器、後端池和 HTTP 設置

1. 在 **"規則"下**，按一下 **"基本"** 以創建新的基本規則。

2. 提供合適的名稱並選擇接受應用服務的傳入請求的攔截器。

3. 在**後端池**下拉清單中，選擇上面創建的後端池。

4. 在**HTTP 設置**下拉清單中，選擇上面創建的 HTTP 設置。

5. 按一下 **"確定"** 以保存此規則。

   ![規則](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>重定向到應用服務的相對路徑時的其他配置

當應用服務向用戶端發送重定向回應以重定向到其相對路徑（例如，從contoso.azurewebsites.net/path1重定向到contoso.azurewebsites.net/path2），它在其回應的位置標頭中使用與從應用程式閘道收到的請求中的主機名稱相同的主機名稱。 因此，用戶端將直接向contoso.azurewebsites.net/path2發出請求，而不是通過應用程式閘道 （contoso.com/path2）。 繞過應用程式閘道是不可取的。

如果在用例中，在某些情況下，應用服務將需要向用戶端發送重定向回應，請執行[其他步驟重寫位置標頭](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)。

## <a name="restrict-access"></a>限制存取

在這些範例中部署的 Web 應用程式會使用可直接從網際網路存取的公用 IP 位址。 這在您了解新功能及嘗試新事物時，可協助進行疑難排解。 但是，如果您想要將一項功能部署到生產環境，則需要新增更多限制。

您可以限制存取 Web 應用程式的其中一個方法是使用 [Azure App Service 靜態 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，您可以限制 Web 應用程式，使它只會從應用程式閘道接收流量。 您可以使用應用程式服務 IP 限制功能，將應用程式閘道 VIP 列為具有存取權的唯一位址。

## <a name="next-steps"></a>後續步驟

要瞭解有關應用程式服務和其他具有應用程式閘道的多租戶支援，請參閱[應用程式閘道的多租戶服務支援](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。
