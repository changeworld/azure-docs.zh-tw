---
title: 使用 PowerShell 自訂規則
titleSuffix: Azure Web Application Firewall
description: 本文提供有關如何使用 PowerShell 在應用程式閘道中自訂 Web 應用程式防火牆規則的資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048515"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>使用 PowerShell 自訂 Web 應用程式防火牆規則

Azure 應用程式閘道 Web 應用程式防火牆 （WAF） 為 Web 應用程式提供保護。 這些保護是由開放 Web 應用程式安全性專案 (OWASP) 的核心規則集 (CRS) 所提供。 某些規則可能會導致誤判，並封鎖真正的流量。 因此，應用程式閘道會提供功能以自訂規則群組與規則。 有關特定規則組和規則的詳細資訊，請參閱 Web[應用程式防火牆 CRS 規則組和規則清單](application-gateway-crs-rulegroups-rules.md)。

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

以下程式碼範例顯示如何檢視可在啟用 WAF 的應用程式閘道上設定的規則和規則群組。

### <a name="view-rule-groups"></a>檢視規則群組

下列範例顯示如何檢視規則群組：

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

以下輸出是上述範例的截斷回應：

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>停用規則

下列範例會停用應用程式閘道上的規則 `911011` 和 `911012`：

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>強制性規則

以下清單包含導致 WAF 在"預防模式"中阻止請求的條件（在檢測模式下，它們記錄為異常）。 無法配置或禁用這些功能：

* 如果無法分析請求正文會導致請求被阻止，除非關閉正文檢查（XML、JSON、表單資料）
* 請求正文（沒有檔）資料長度大於配置的限制
* 請求正文（包括檔）大於限制
* WAF 引擎中發生內部錯誤

CRS 3.x 具體：

* 入站異常分數超過閾值

## <a name="next-steps"></a>後續步驟

設定已停用的規則之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
