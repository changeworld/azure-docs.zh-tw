---
title: '註冊對等互連服務連線 - Azure PowerShell '
description: 在本教學課程中，您將了解如何使用 PowerShell 註冊對等互連服務連線。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: 163f93ab93890f27eb2a9b18cc23804595fcb822
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84870615"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>教學課程：使用 Azure PowerShell 註冊對等互連服務連線

在本教學課程中，您將了解如何使用 Azure PowerShell 註冊對等互連服務。

Azure 對等互連服務是一項網路服務，可增強客戶對 Microsoft 雲端服務，例如 Office 365、Dynamics 365、軟體即服務 (SaaS) 服務、Azure，或可透過公用網際網路存取的任何 Microsoft 服務的連線能力。 在本文中，您將了解如何使用 Azure PowerShell 來註冊對等互連服務連線。

如果您沒有 Azure 訂用帳戶，請立即建立[帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定在本機安裝和使用 PowerShell，則在進行此快速入門時，您必須使用 Azure PowerShell 模組 1.0.0 版或更新版本。 若要尋找已安裝的版本，請執行 `Get-Module -ListAvailable Az`。 如需安裝和升級資訊，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

最後，如果您在本機執行 PowerShell，也需要執行 `Connect-AzAccount`。 該命令會建立與 Azure 的連線。

使用 Azure PowerShell 模組註冊和管理對等互連服務。 您可以透過 PowerShell 命令列或在指令碼中註冊或管理對等互連服務。


## <a name="prerequisites"></a>必要條件  
您必須滿足以下條件：

### <a name="azure-account"></a>Azure 帳戶

您必須具有有效且作用中的 Microsoft Azure 帳戶。 需要此帳戶才能設定對等互連服務連線。 對等互連服務是 Azure 訂用帳戶內的資源。

### <a name="connectivity-provider"></a>連線提供者

您可以使用網際網路服務提供者或網際網路交換合作夥伴來取得對等互連服務，以將您的網路與 Microsoft 網路連線。

請確定連線提供者已與 Microsoft 合作。

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>向資源提供者和功能旗標註冊訂用帳戶

在繼續進行註冊對等互連服務的步驟之前，請使用 Azure PowerShell，向資源提供者和功能旗標註冊您的訂用帳戶。 以下為指定的 Azure PowerShell 命令：

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>擷取位置和服務提供者 

在 Azure PowerShell 中執行下列命令，以取得應啟用對等互連服務的位置和服務提供者。 

取得對等互連服務位置：

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

取得對等互連服務提供者：
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>註冊對等互連服務連線

透過 Azure PowerShell 使用下列命令集來註冊對等互連服務連線。 此範例會註冊名為 myPeeringService 的對等互連服務。

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>註冊對等互連服務字首

透過 Azure PowerShell 執行下列命令，以註冊連線提供者所提供的字首。 此範例會註冊名為 myPrefix 的字首。

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>列出所有對等互連服務連線

若要檢視所有對等互連服務的清單，請執行下列命令：

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>列出所有對等互連服務字首

若要檢視所有對等互連服務字首的清單，請執行下列命令：

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>移除對等互連服務字首

若要移除對等互連服務字首，請執行下列命令：

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>後續步驟

- 若要深入了解對等互連服務連線，請參閱[對等互連服務連線](connection.md)。
- 若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。
- 若要使用 Azure 入口網站註冊對等互連服務連線，請參閱[註冊對等互連服務連線 - Azure 入口網站](azure-portal.md)。
- 若要使用 Azure CLI 註冊對等互連服務連線，請參閱[註冊對等互連服務連線 - Azure CLI](cli.md)。
