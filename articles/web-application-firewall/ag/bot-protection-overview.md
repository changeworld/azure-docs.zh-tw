---
title: Azure 應用程式閘道上的 WAF Bot 保護概觀
titleSuffix: Azure Web Application Firewall
description: 本文概述應用程式閘道上的 Web 應用程式防火牆 (WAF) Bot 保護
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83714894"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 應用程式閘道上的 Azure Web 應用程式防火牆 Bot 保護概觀

大約 20% 的網際網路流量來自惡意的 Bot。 它們會抓取、掃描、尋找 Web 應用程式中的弱點。 當這些 Bot 被停在 Web 應用程式防火牆 (WAF) 之外，就無法攻擊您。 它們也不能使用您的資源和服務，例如您的後端和其他基礎結構。

您可以為 WAF 啟用受控的 Bot 保護規則集，以封鎖或記錄來自已知惡意 IP 位址的要求。 這些 IP 位址皆來自 Microsoft 威脅情報摘要。 包括 Azure 資訊安全中心在內的多項服務皆使用 Intelligent Security Graph，其同時也支援 Microsoft 威脅情報的運作。

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，並且提供預覽服務等級的協定。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱  [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 。

## <a name="use-with-owasp-rulesets"></a>使用 OWASP 規則集

您可以使用 Bot 保護規則集搭配任何 OWASP 規則集 (2.2.9、3.0 和 3.1)。 在任何指定的時間都只能使用一個 OWASP 規則集。 Bot 保護規則集包含一個額外的規則，此規則有其自己的規則集。 其標題是 **Microsoft_BotManagerRuleSet_0.1**，您可以像其他 OWASP 規則一樣加以啟用或停用。

![Bot 規則集](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>規則集更新

已知惡意 IP 位址的 Bot 風險降低規則集清單會每天更新一次，更新來自 Microsoft 威脅情報摘要，以維持與 Bot 同步。 即使 Bot 攻擊媒介變更，您的 Web 應用程式仍會持續受到保護。

## <a name="log-example"></a>記錄範例

以下是 Bot 保護的範例記錄：

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>後續步驟

- [在 Azure 應用程式閘道上的 Web 應用程式防火牆設定 Bot 保護 (預覽)](bot-protection.md)
