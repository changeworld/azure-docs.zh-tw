---
title: Azure ExpressRoute：將傳統電路移至 Resource Manager
description: 本頁面會描述如何使用 PowerShell 將傳統的電路移至 Resource Manager 部署模型。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: charwen
ms.openlocfilehash: 5c4bbdde04176edf693fc58d172a7dcc250c495a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84724996"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>使用 PowerShell 將 ExpressRoute 線路從傳統部署模型移至 Resource Manager 部署模型

若要在傳統和 Resource Manager 兩種部署模型中使用 ExpressRoute，您必須將電路移至 Resource Manager 部署模型。 下列章節協助您使用 PowerShell 來移動線路。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* 確認您已在本機電腦上安裝傳統和 Az Azure PowerShell 模組。 如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。
* 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)和[工作流程](expressroute-workflows.md)。
* 請檢閱[將 ExpressRoute 電路從傳統移至 Resource Manager](expressroute-move.md) 下提供的資訊。 請確定您已完整了解各項限制。
* 請確認電路在傳統部署模型中的運作完全正常。
* 請確定您擁有建立在 Resource Manager 部署模型中建立的資源群組。

## <a name="move-an-expressroute-circuit"></a>移動 ExpressRoute 電路

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>步驟 1︰從傳統部署模型收集電路詳細資訊

登入 Azure 傳統環境並收集服務金鑰。

1. 登入您的 Azure 帳戶。

   ```powershell
   Add-AzureAccount
   ```

2.  選取適當的 Azure 訂用帳戶。

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. 匯入 Azure 和 ExpressRoute 的 PowerShell 模組。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. 使用下列 Cmdlet 來取得所有 ExpressRoute 電路的服務金鑰。 在取得金鑰之後，請複製電路的「服務金鑰」****，這個電路就是您想要移至 Resource Manager 部署模型的電路。

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>步驟 2：登入並建立資源群組

登入 Resource Manager 環境並建立新的資源群組。

1. 登入您的 Azure Resource Manager 環境。

   ```powershell
   Connect-AzAccount
   ```

2.  選取適當的 Azure 訂用帳戶。

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. 修改下列程式碼片段以建立新的資源群組 (如果您尚未擁有資源群組)。

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>步驟 3：將 ExpressRoute 電路移至 Resource Manager 部署模型

您已準備就緒，可將 ExpressRoute 電路從傳統部署模型移至 Resource Manager 部署模型。 更進一步之前，請先檢閱[將 ExpressRoute 電路從傳統移至 Resource Manager 部署模型](expressroute-move.md)下提供的資訊。

若要移動電路，請修改並執行下列程式碼片段：

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

在傳統模式中，ExpressRoute 電路沒有繫結至區域的概念。 不過，在 Resource Manager 中，每個資源都必須對應至 Azure 區域。 在技術上，AzExpressRouteCircuit Cmdlet 中指定的區域可以是任何區域。 基於組織目的，您可能想要選擇最能代表您對等互連位置的區域。

> [!NOTE]
> 移動完成之後，列在前一個 Cmdlet 中的新名稱會用來處理資源。 電路基本上會重新命名。
> 

## <a name="modify-circuit-access"></a>修改電路存取

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>為兩種部署模型啟用 ExpressRoute 電路存取

在將傳統 ExpressRoute 電路移至 Resource Manager 部署模型之後，您可以為這兩種部署模型啟用存取。 執行下列的 Cmdlet 以存取這兩種部署模型︰

1. 取得電路詳細資料。

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. 將 [允許傳統作業] 設定為 TRUE。

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. 更新電路。 成功完成這項作業後，您就可以在傳統部署模型中檢視電路。

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. 執行下列 Cmdlet 以取得 ExpressRoute 電路的詳細資料。 您必須能夠看到列出的服務金鑰。

   ```powershell
   get-azurededicatedcircuit
   ```

5. 您現在可以使用適用於傳統 VNet 的傳統部署模型命令，以及適用於 Resource Manager VNet 的 Resource Manager 命令，來管理 ExpressRoute 電路的連結。 下列文件會協助您管理 ExpressRoute 線路的連結︰

    * [在 Resource Manager 部署模型中將虛擬網路連結到 ExpressRoute 電路](expressroute-howto-linkvnet-arm.md)
    * [在傳統部署模型中將虛擬網路連結到 ExpressRoute 電路](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>停用傳統部署模型的 ExpressRoute 電路存取

執行下列的 Cmdlet 以停止傳統部署模型的存取。

1. 取得 ExpressRoute 電路的詳細資料。

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. 將 [允許傳統作業] 設定為 FALSE。

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. 更新電路。 成功完成這項作業後，您就不能在傳統部署模型中檢視電路。

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>後續步驟

* [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-arm.md)
* [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
