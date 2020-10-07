---
title: 快速入門：使用 Azure Front Door 設定高可用性 - Azure PowerShell
description: 本快速入門將說明如何使用 Azure Front Door，利用 Azure PowerShell 來建立高可用性和高效能的全域 Web 應用程式。
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347842"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 為高可用性的全域 Web 應用程式建立 Front Door

使用 Azure PowerShell 來建立高度可用且高效能的全域 Web 應用程式，以開始使用 Azure Front Door。

Front Door 會將網路流量導向後端集區中的特定資源。 您已定義前端網域、將資源新增至後端集區，以及建立路由規則。 本文使用具有兩個 Web 應用程式資源的一個後端集區簡單設定，以及使用符合 "/*" 預設路徑的單一路由規則。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure PowerShell 或 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 您可以使用現有的資源群組，或建立一個新的群組。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>建立 Web 應用程式的兩個執行個體

本快速入門需要兩個在不同 Azure 區域中執行的 Web 應用程式執行個體。 這兩個 Web 應用程式執行個體都會以「主動/主動」模式執行，因此其中任一個都可以接受流量。 此設定不同於「主動/待命」設定，也就是其中一個執行個體會用來作為容錯移轉。

如果您還沒有 Web 應用程式，請使用下列指令碼來設定兩個 Web 應用程式範例。

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>建立前端服務

本節將詳細說明如何建立及設定下列 Front Door 元件：
    
* 前端物件包含 Front Door 預設網域。
* 後端集區是一組對等後端，Front Door 會對其進行用戶端要求的負載平衡。
* 路由規則會將前端主機和相符的 URL 路徑模式對應到特定的後端集區。

### <a name="create-a-frontend-object"></a>建立前端物件

前端物件會設定 Front Door 的主機名稱。 根據預設，主機名稱會具有尾碼 * *.azurefd.net*。

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>建立後端集區

後端集區包含兩個在本快速入門一開始時建立的 Web 應用程式。 此步驟中定義的健全狀態探查和負載平衡設定會使用預設值。

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>建立路由規則

路由規則會將後端集區對應至前端網域，並將預設的路徑比對值設定為 "/*"。

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>建立 Front Door

現在您已建立必要的物件，接下來請建立 Front Door：

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

部署成功後，您可以依照下一節中的步驟加以測試。

## <a name="test-the-front-door"></a>測試 Front Door

執行下列命令以取得 Front Door 的主機名稱。

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

開啟網頁瀏覽器，並輸入從命令取得的主機名稱。 Front Door 會將您的要求導向其中一個後端資源。 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door 測試頁面":::

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為 Front Door 建立的資源時，請刪除資源群組。 在刪除資源群組時，將同時刪除 Front Door 及其所有相關資源。 

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：
* Front Door
* 兩個 Web 應用程式

若要了解如何將自訂網域新增至您的 Front Door，請繼續進行 Front Door 教學課程。

> [!div class="nextstepaction"]
> [新增自訂網域](front-door-custom-domain.md)
