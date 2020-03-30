---
title: 故障排除重定向到應用服務 URL
titleSuffix: Azure Application Gateway
description: 本文提供有關如何在 Azure 應用程式閘道與 Azure 應用服務一起使用時解決重定向問題的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293528"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>解決應用程式閘道中的應用服務問題

瞭解如何診斷和解決 Azure 應用服務在 Azure 應用程式閘道中用作後端目標時可能遇到的問題。

## <a name="overview"></a>總覽

在本文中，您將學習如何解決以下問題：

> [!div class="checklist"]
> * 重定向時，應用服務 URL 將在瀏覽器中公開。
> * 應用服務 ARRAffinity Cookie 域設置為應用服務主機名，example.azurewebsites.net，而不是原始主機。

當後端應用程式發送重定向回應時，您可能希望將用戶端重定向到與後端應用程式指定的 URL 不同的 URL。 當應用服務託管在應用程式閘道後面，並要求用戶端對其相對路徑進行重定向時，您可能希望執行此操作。 例如，從contoso.azurewebsites.net/path1重定向到contoso.azurewebsites.net/path2。 

當應用服務發送重定向回應時，它在其回應的位置標頭中使用與從應用程式閘道接收的請求中的主機名稱相同的主機名稱。 例如，用戶端將請求直接contoso.azurewebsites.net/path2而不是通過應用程式閘道contoso.com/path2。 您不想繞過應用程式閘道。

此問題可能發生以下主要原因：

- 應用服務上已配置重定向。 重定向可以像向請求添加尾隨斜杠一樣簡單。
- 您具有 Azure 活動目錄身份驗證，這將導致重定向。

此外，當您在應用程式閘道後面使用應用服務時，與應用程式閘道 （example.com） 關聯的功能變數名稱與應用服務的功能變數名稱（例如，example.azurewebsites.net）不同。 應用服務設置的 ARRAffinity Cookie 的域值包含example.azurewebsites.net功能變數名稱，這是不可取的。 原始主機名稱example.com應為 Cookie 中的功能變數名稱值。

## <a name="sample-configuration"></a>範例組態

- HTTP 攔截器：基本網站或多網站
- 後端位址集區：應用服務
- HTTP 設置：**從啟用的後端位址選擇主機名稱**
- 探測：**從啟用的 HTTP 設置中選擇主機名稱**

## <a name="cause"></a>原因

應用服務是一個多租戶服務，因此它使用請求中的主機標頭將請求路由到正確的終結點。 應用服務的預設功能變數名稱 _.azurewebsites.net（例如contoso.azurewebsites.net）與應用程式閘道的功能變數名稱不同（例如，contoso.com）。 

來自用戶端的原始請求將應用程式閘道的功能變數名稱contoso.com作為主機名稱。 您需要配置應用程式閘道，在將請求路由到應用服務後端時，將原始請求中的主機名稱更改為應用服務的主機名稱。 在應用程式閘道的 HTTP 設置配置中使用交換器**從後端位址選擇主機名稱**。 在運行狀況探測配置中使用開關**從後端 HTTP 設置中選擇主機名稱**。



![應用程式閘道更改主機名稱](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

當應用服務執行重定向時，它使用位置標頭中覆蓋的主機名稱contoso.azurewebsites.net，而不是原始主機名稱contoso.com，除非另有配置。 檢查以下示例請求和回應標頭。
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
在前面的示例中，請注意回應標頭的狀態碼為 301 用於重定向。 位置標頭具有應用服務的主機名稱，而不是原始主機名稱`www.contoso.com`。

## <a name="solution-rewrite-the-location-header"></a>解決方案：重寫位置標頭

將位置標頭中的主機名稱設置為應用程式閘道的功能變數名稱。 為此，請創建一個[重寫規則](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)，其條件將評估回應中的位置標頭是否包含azurewebsites.net。 它還必須執行操作以重寫位置標頭，才能具有應用程式閘道的主機名稱。 有關詳細資訊，請參閱有關如何[重寫位置標頭的說明](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)。

> [!NOTE]
> HTTP 標頭重寫支援僅適用于應用程式閘道[的Standard_v2和WAF_v2 SKU。](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 如果使用 v1 SKU，我們建議您[從 v1 遷移到 v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)。 您希望使用 v2 SKU 可用的重寫和其他[高級功能](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku)。

## <a name="alternate-solution-use-a-custom-domain-name"></a>備用解決方案：使用自訂功能變數名稱

如果使用 v1 SKU，則無法重寫位置標頭。 此功能僅適用于 v2 SKU。 要解決重定向問題，請將應用程式閘道接收到應用服務的相同主機名稱也傳遞給應用服務，而不是執行主機重寫。

應用服務現在對指向應用程式閘道而不是其自己的同一原始主機標頭執行重定向（如果有）。

您必須擁有自訂域並遵循此過程：

- 將域註冊到應用服務的自訂域清單。 自訂域中必須具有指向應用服務的 FQDN 的 CNAME。 有關詳細資訊，請參閱[將現有自訂 DNS 名稱映射到 Azure 應用服務](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

    ![應用服務自訂域清單](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 你的應用服務已準備好接受主機名稱`www.contoso.com`。 在 DNS 中更改 CNAME 條目以將其指向應用程式閘道的 FQDN，例如。 `appgw.eastus.cloudapp.azure.com`

- 執行 DNS 查詢`www.contoso.com`時，請確保域解析為應用程式閘道的 FQDN。

- 設置自訂探測以禁用**從後端 HTTP 設置中選取主機名稱**。 在 Azure 門戶中，清除探測設置中的核取方塊。 在 PowerShell 中，不要在**Set-Az應用程式閘道探測配置**命令中使用 **-PickHostName 從後端HttpSettings 開關**。 在探測的主機名稱欄位中，輸入應用服務的 FQDN，example.azurewebsites.net。 從應用程式閘道發送的探測請求在主機標頭中攜帶此 FQDN。

  > [!NOTE]
  > 在下一步中，請確保您的自訂探測未與後端 HTTP 設置關聯。 此時，HTTP 設置仍然啟用**了"從後端位址"交換器中選取主機名稱**。

- 設置應用程式閘道的 HTTP 設置以禁用**從後端位址選取主機名稱**。 在 Azure 門戶中，清除該核取方塊。 在 PowerShell 中，不要在 **"設置-Az應用程式閘道後端Http設置"** 命令中使用 **-PickHostName 從後端位址**切換。

- 將自訂探測關聯回後端 HTTP 設置，並驗證後端是否正常。

- 應用程式閘道現在應將相同的主機名稱`www.contoso.com`轉發到應用服務。 重定向發生在同一主機名稱上。 檢查以下示例請求和回應標頭。

要使用 PowerShell 執行現有設置的上述步驟，請使用以下示例 PowerShell 腳本。 請注意，在探測器和 HTTP 設置配置中，我們如何使用 **-PickHostname**開關。

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

如果上述步驟未解決問題，則打開[支援票證](https://azure.microsoft.com/support/options/)。
