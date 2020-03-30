---
title: 將 Web 應用程式防火牆策略與現有 Azure 應用程式閘道關聯
description: 瞭解如何將 Web 應用程式防火牆策略與現有的 Azure 應用程式閘道相關聯。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083897"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>將 WAF 策略與現有應用程式閘道關聯

您可以使用 Azure PowerShell[創建 WAF 策略](create-waf-policy-ag.md)，但您可能已經具有應用程式閘道，並且只想將 WAF 策略關聯到該策略。 在本文中，您就這樣做;創建 WAF 策略並將其關聯到現有的應用程式閘道。 

1. 獲取應用程式閘道和防火牆策略。 如果沒有現有的防火牆策略，請參閱步驟 2。 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. （可選）創建防火牆策略。

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > 如果要創建此 WAF 策略以從 WAF 配置轉換為 WAF 策略，則該策略需要是舊配置的精確副本。這意味著每個排除、自訂規則、禁用規則組等都需要與 WAF 配置中完全相同的規則組相同。
3. （可選）您可以配置 WAF 策略以滿足您的需要。 這包括自訂規則、禁用規則/規則組、排除項、設置檔上載限制等。如果跳過此步驟，將選擇所有預設值。 
   
4. 保存策略，並將其附加到應用程式閘道。 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>後續步驟
[瞭解自訂規則。](configure-waf-custom-rules.md)
