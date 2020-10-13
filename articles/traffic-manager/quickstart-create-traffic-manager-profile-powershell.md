---
title: 快速入門：建立設定檔以讓應用程式具有高可用性 - Azure PowerShell - Azure 流量管理員
description: 本快速入門文章會說明如何建立流量管理員設定檔，以建置高可用性的 Web 應用程式。
services: traffic-manager
author: duongau
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: 9b34a17cc9add0bed4bffb7677aa81bb17f3125b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631557"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立流量管理員設定檔以獲得高可用性的 Web 應用程式

本快速入門會說明如何建立流量管理員設定檔，以便為 Web 應用程式提供高可用性。

在本快速入門中，您會建立 Web 應用程式的兩個執行個體。 每個執行個體會在不同的 Azure 區域中執行。 您會建立以[端點優先順序](traffic-manager-routing-methods.md#priority-traffic-routing-method)為基礎的流量管理員設定檔。 此設定檔會將使用者流量導向執行 Web 應用程式的主要網站。 流量管理員會持續監視 Web 應用程式。 如果主要網站無法使用，它會提供自動容錯移轉至備份網站。

## <a name="prerequisites"></a>先決條件

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組
使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組。

```azurepowershell-interactive

# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

使用 [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) 建立流量管理員設定檔，以根據端點優先順序引導使用者流量。

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>建立 Web Apps

在本快速入門中，您必須在兩個不同的 Azure 區域 (*美國西部*和*美國東部*) 中部署 Web 應用程式的兩個執行個體。 每個執行個體都會作為流量管理員的主要和容錯移轉端點。

### <a name="create-web-app-service-plans"></a>建立 Web App Service 方案
針對您將在兩個不同 Azure 區域中部署的兩個 Web 應用程式執行個體，請使用 [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) 建立 Web App Service 方案。

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>在 App Service 方案中建立 Web 應用程式
使用 [New-AzWebApp](/powershell/module/az.websites/new-azwebapp)，在「美國西部」** 和「美國東部」** Azure 區域的 App Service 方案中建立 Web 應用程式的兩個執行個體。

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>新增流量管理員端點
使用 [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint)，將兩個 Web 應用程式當作流量管理員端點新增到流量管理員設定檔，如下所示：
- 將位於「美國西部」** Azure 區域中的 Web 應用程式新增為主要端點，以便路由傳送所有的使用者流量。 
- 將位於「美國東部」** Azure 區域中的 Web 應用程式新增為容錯移轉端點。 當主要端點無法使用時，流量就會自動路由傳送到容錯移轉端點。

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>測試流量管理員設定檔

在本節中，您會檢查流量管理員設定檔的網域名稱。 您也會將主要端點設定為無法使用。 最後，您可以看到 Web 應用程式仍可使用。 這是因為流量管理員將流量傳送至容錯移轉端點。

### <a name="determine-the-dns-name"></a>確定 DNS 名稱

使用 [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)，判斷流量管理員設定檔的 DNS 名稱。

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

複製 **RelativeDnsName** 值。 流量管理員設定檔的 DNS 名稱為 *http://<* relativednsname *>.trafficmanager.net*。 

### <a name="view-traffic-manager-in-action"></a>檢視流量管理員的運作
1. 在網頁瀏覽器中，輸入流量管理員設定檔的 DNS 名稱 (*http://<* relativednsname *>.trafficmanager.net*)，以檢視 Web 應用程式的預設網站。

    > [!NOTE]
    > 在此快速入門案例中，所有要求都會路由傳送至主要端點。 它會設定為 [優先順序 1]****。
2. 若要檢視進行中的流量管理員容錯移轉，請使用 [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint) 來停用主要網站。

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. 複製流量管理員設定檔的 DNS 名稱 (*http://<* relativednsname *>.trafficmanager.net*)，以在新的網頁瀏覽器工作階段中檢視網站。
4. 確認 Web 應用程式仍可使用。

## <a name="clean-up-resources"></a>清除資源

完成時，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 刪除資源群組、Web 應用程式和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立流量管理員設定檔，以便為 Web 應用程式提供高可用性。 若要深入了解如何路由傳送流量，請繼續進行流量管理員的教學課程。

> [!div class="nextstepaction"]
> [流量管理員教學課程](tutorial-traffic-manager-improve-website-response.md)
