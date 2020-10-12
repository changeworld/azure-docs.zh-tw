---
title: 使用 Azure Front Door 來設定 Web 應用程式防火牆 (WAF) 的自訂回應
description: 瞭解如何在 WAF 封鎖要求時設定自訂回應碼和訊息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: a995460793686d8293d77965e74e2cbf916925a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005594"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>設定 Azure Web 應用程式防火牆 (WAF) 的自訂回應

根據預設，當 WAF 因為符合規則而封鎖要求時，會傳回403狀態碼，並 **顯示要求被封鎖的** 訊息。 預設訊息也包含可用於連結至要求之 [記錄專案](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor) 的追蹤參考字串。  您可以針對您的使用案例，設定自訂的回應狀態碼和含有參考字串的自訂訊息。 本文說明如何在 WAF 封鎖要求時設定自訂回應頁面。

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>設定自訂回應狀態碼和訊息使用入口網站

您可以在 WAF 入口網站中，從 [原則設定] 下設定自訂的回應狀態碼和主體。

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF 原則設定":::

在上述範例中，我們將回應碼保持為403，並設定簡短的「請洽詢我們」訊息，如下圖所示：

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="WAF 原則設定" 會在回應主體中插入唯一的參考字串。 值符合和記錄中的 TrackingReference 欄位 `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` 。

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>設定自訂回應狀態碼和訊息使用 PowerShell

### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境

Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)，並將其用於任何 PowerShell 工作階段。 請遵循頁面上的指示，使用 Azure 認證進行登入，並安裝 Az PowerShell 模組。

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

### <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 我們在這裡使用 [>new-azresourcegroup](/powershell/module/Az.resources/new-Azresourcegroup)來建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>使用自訂回應建立新的 WAF 原則 

以下範例會建立新的 WAF 原則，並將自訂回應狀態碼設為405，並封鎖訊息給 **您**，並使用 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

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

使用 [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)修改現有 WAF 原則的自訂回應碼或回應主體設定。

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
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>接下來的步驟
- 深入瞭解 [具有 Azure Front Door 的 Web 應用程式防火牆](../afds/afds-overview.md)