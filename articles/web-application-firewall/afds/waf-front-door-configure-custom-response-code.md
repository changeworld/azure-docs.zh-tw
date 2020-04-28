---
title: 使用 Azure Front WAF 設定自訂回應
description: 瞭解如何在 Web 應用程式防火牆（WAF）封鎖要求時設定自訂回應碼和訊息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185352"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>設定 Azure Web 應用程式防火牆的自訂回應

根據預設，當 Azure Web 應用程式防火牆（WAF）與 Azure Front 門板因符合規則而封鎖要求時，會傳回403狀態碼，並**顯示 [要求已封鎖**] 訊息。 本文說明如何在 WAF 封鎖要求時，設定自訂回應狀態碼和回應訊息。

## <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 請遵循頁面上的指示，使用 Azure 認證進行登入，並安裝 Az PowerShell 模組。

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用互動式登入對話方塊連線至 Azure
```
Connect-AzAccount
Install-Module -Name Az
```
請確定您已安裝目前的 PowerShellGet 版本。 執行下列命令，然後重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 在此範例中，您會使用[remove-azresourcegroup](/powershell/module/Az.resources/new-Azresourcegroup)來建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>使用自訂回應建立新的 WAF 原則 

以下範例說明如何建立新的 WAF 原則，並將自訂回應狀態碼設定為405，並封鎖訊息給**您。** 使用[新的-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

使用[AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)修改現有 WAF 原則的自訂回應碼或回應主體設定。

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>後續步驟
- 深入瞭解[使用 Azure Front 的 Web 應用程式防火牆](../afds/afds-overview.md)