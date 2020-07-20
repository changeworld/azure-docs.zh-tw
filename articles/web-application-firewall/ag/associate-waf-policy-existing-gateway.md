---
title: 將 Web 應用程式防火牆原則與現有的 Azure 應用程式閘道建立關聯
description: 瞭解如何將 Web 應用程式防火牆原則與現有的 Azure 應用程式閘道建立關聯。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74083897"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>建立 WAF 原則與現有應用程式閘道的關聯

您可以使用 Azure PowerShell 來[建立 WAF 原則](create-waf-policy-ag.md)，但您可能已經有應用程式閘道，而只是想要將 WAF 原則與它建立關聯。 在本文中，您只需要這麼做;您會建立 WAF 原則，並將它與現有的應用程式閘道建立關聯。 

1. 取得您的應用程式閘道和防火牆原則。 如果您沒有現有的防火牆原則，請參閱步驟2。 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. 選擇性建立防火牆原則。

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > 如果您要建立此 WAF 原則，以從 WAF 設定轉換為 WAF 原則，則原則必須是舊設定的完全相同複本。這表示每個排除、自訂規則、停用的規則群組等都必須與 WAF 設定中的完全相同。
3. 選擇性您可以設定 WAF 原則以符合您的需求。 這包括自訂規則、停用規則/規則群組、排除專案、設定檔案上傳限制等。如果您略過此步驟，則會選取所有預設值。 
   
4. 儲存原則，並將它附加至您的應用程式閘道。 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>後續步驟
[深入瞭解自訂規則。](configure-waf-custom-rules.md)
