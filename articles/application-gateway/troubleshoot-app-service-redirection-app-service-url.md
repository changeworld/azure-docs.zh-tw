---
title: 針對重新導向至 App Service URL 進行疑難排解
titleSuffix: Azure Application Gateway
description: 本文提供有關如何在搭配 Azure App Service 使用 Azure 應用程式閘道時，針對重新導向問題進行疑難排解的資訊。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 2af52d1e7c211ccc0b5c18ed1ecda66d46d80786
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84806487"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>針對應用程式閘道中的 App Service 問題進行疑難排解

瞭解如何診斷並解決當您使用 Azure App Service 做為 Azure 應用程式閘道的後端目標時可能會遇到的問題。

## <a name="overview"></a>總覽

在本文中，您將瞭解如何針對下列問題進行疑難排解：

> [!div class="checklist"]
> * 當有重新導向時，就會在瀏覽器中公開 app service URL。
> * App service ARRAffinity cookie 網域會設定為 app service 主機名稱 example.azurewebsites.net，而不是原始主機。

當後端應用程式傳送重新導向回應時，您可能會想要將用戶端重新導向至不同于後端應用程式所指定的 URL。 當 app service 裝載于應用程式閘道後方時，您可能會想要這麼做，而且需要用戶端重新導向至其相對路徑。 例如，從 contoso.azurewebsites.net/path1 重新導向至 contoso.azurewebsites.net/path2。 

當 app service 傳送重新導向回應時，它會在其回應的位置標頭中使用與它從應用程式閘道接收之要求中的相同主機名稱。 例如，用戶端會直接向 contoso.azurewebsites.net/path2 提出要求，而不是透過應用程式閘道 contoso.com/path2。 您不想要略過應用程式閘道。

發生此問題的主要原因如下：

- 您已在 app service 上設定重新導向。 重新導向可以簡單到將尾端斜線加入至要求。
- 您有 Azure Active Directory 驗證，這會導致重新導向。

此外，當您使用應用程式閘道後方的應用程式服務時，與應用程式閘道（example.com）相關聯的功能變數名稱會與 app service 的功能變數名稱（例如 example.azurewebsites.net）不同。 App service 所設定之 ARRAffinity cookie 的定義域值會攜帶 example.azurewebsites.net 功能變數名稱，這不是必要的名稱。 原始的主機名稱 example.com 應該是 cookie 中的功能變數名稱值。

## <a name="sample-configuration"></a>範例組態

- HTTP 接聽程式：基本或多網站
- 後端位址集區： App Service
- HTTP 設定：**從已啟用的後端位址挑選主機名稱**
- 探查：**從啟用的 HTTP 設定中挑選主機名稱**

## <a name="cause"></a>原因

App Service 是多租使用者服務，因此它會在要求中使用主機標頭，將要求路由傳送至正確的端點。 應用程式服務的預設功能變數名稱（*. azurewebsites.net （例如 contoso.azurewebsites.net））與應用程式閘道的功能變數名稱（例如 contoso.com）不同。 

來自用戶端的原始要求具有應用程式閘道的功能變數名稱 contoso.com，做為主機名稱。 您需要設定應用程式閘道，以便在將要求路由至 app service 後端時，將原始要求中的主機名稱變更為應用程式服務的主機名稱。 使用參數從應用程式閘道的 HTTP 設定中的**後端位址挑選主機名稱**。 在健康情況探查設定中使用參數**從後端 HTTP 設定挑選主機名稱**。



![應用程式閘道變更主機名稱](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

當 app service 進行重新導向時，除非另有設定，否則會使用 location 標頭中的覆寫主機名稱 contoso.azurewebsites.net，而不是原始主機名稱 contoso.com。 請檢查下列範例要求和回應標頭。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
在上一個範例中，請注意回應標頭的狀態碼為301以進行重新導向。 Location 標頭具有 app service 的主機名稱，而不是原始主機名稱 `www.contoso.com` 。

## <a name="solution-rewrite-the-location-header"></a>解決方案：重寫 location 標頭

將 location 標頭中的主機名稱設定為應用程式閘道的功能變數名稱。 若要這麼做，請建立具有條件的[重寫規則](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)，以評估回應中的位置標頭是否包含 azurewebsites.net。 它也必須執行動作來重寫 location 標頭，使其具有應用程式閘道的主機名稱。 如需詳細資訊，請參閱[如何重寫 location 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)的指示。

> [!NOTE]
> HTTP 標頭重寫支援僅適用于應用程式閘道的[Standard_v2 和 WAF_V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 。 如果您使用 v1 SKU，建議您[從 v1 遷移至 v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)。 您想要使用 v2 SKU 提供的重寫和其他[先進功能](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku)。

## <a name="alternate-solution-use-a-custom-domain-name"></a>替代解決方案：使用自訂功能變數名稱

如果您使用 v1 SKU，就無法重寫 location 標頭。 這項功能僅適用于 v2 SKU。 若要解決重新導向問題，請將應用程式閘道接收的相同主機名稱傳遞至 app service，而不是執行主機覆寫。

App service 現在會在相同的原始主機標頭上執行重新導向（如果有的話），以指向應用程式閘道，而不是其本身。

您必須擁有自訂網域，並遵循此程式：

- 向應用程式服務的自訂網域清單註冊網域。 您的自訂網域中必須有指向 app service 之 FQDN 的 CNAME。 如需詳細資訊，請參閱將[現有的自訂 DNS 名稱對應至 Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

    ![App service 自訂網域清單](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 您的 app service 已準備好接受主機名稱 `www.contoso.com` 。 變更 DNS 中的 CNAME 專案，將它指向應用程式閘道的 FQDN，例如 `appgw.eastus.cloudapp.azure.com` 。

- 當您執行 DNS 查詢時，請確定您的網域會 `www.contoso.com` 解析為應用程式閘道的 FQDN。

- 將您的自訂探查設定為停**用從後端 HTTP 設定中挑選主機名稱**。 在 [Azure 入口網站中，清除 [探查設定] 中的核取方塊。 在 PowerShell 中，請勿在**AzApplicationGatewayProbeConfig**命令中使用 **-PickHostNameFromBackendHttpSettings**參數。 在探查的 [主機名稱] 欄位中，輸入您的 app service 的 FQDN，example.azurewebsites.net。 從應用程式閘道傳送的探查要求會在主機標頭中攜帶此 FQDN。

  > [!NOTE]
  > 在下一個步驟中，請確定您的自訂探查未與後端 HTTP 設定相關聯。 此時，您的 HTTP 設定仍然會啟用**從後端位址切換的挑選主機名稱**。

- 設定應用程式閘道的 HTTP 設定，以停**用從後端位址挑選主機名稱**。 在 [Azure 入口網站中，清除核取方塊。 在 PowerShell 中，請勿在**new-azapplicationgatewaybackendHTTPsettings 設定設定**命令中使用 **-PickHostNameFromBackendAddress**參數。

- 將自訂探查關聯回後端 HTTP 設定，並確認後端狀況良好。

- 應用程式閘道現在應該將相同的主機名稱轉送 `www.contoso.com` 到 app service。 重新導向會在相同的主機名稱上執行。 請檢查下列範例要求和回應標頭。

若要針對現有的安裝程式使用 PowerShell 來執行先前的步驟，請使用下列範例 PowerShell 腳本。 請注意，我們尚未在探查和 HTTP 設定中使用 **-PickHostname**參數。

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟[支援票證](https://azure.microsoft.com/support/options/)。
