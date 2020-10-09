---
title: 使用 CLI 自訂規則-Azure Web 應用程式防火牆
description: 本文提供如何在應用程式閘道中使用 Azure CLI 自訂 Web 應用程式防火牆規則的相關資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74048521"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>使用 Azure CLI 自訂 Web 應用程式防火牆規則

Azure 應用程式閘道 Web 應用程式防火牆 (WAF) 提供 Web 應用程式的保護。 這些保護是由開放 Web 應用程式安全性專案 (OWASP) 的核心規則集 (CRS) 所提供。 某些規則可能會導致誤判，並封鎖真正的流量。 因此，應用程式閘道會提供功能以自訂規則群組與規則。 如需特定規則群組和規則的詳細資訊，請參閱 [Web 應用程式防火牆 CRS 規則群組和規則的清單](application-gateway-crs-rulegroups-rules.md)。

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

以下程式碼範例顯示如何檢視可設定的規則和規則群組。

### <a name="view-rule-groups"></a>檢視規則群組

下列範例顯示如何檢視規則群組：

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

以下輸出是上述範例的截斷回應：

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>檢視規則群組中的規則

下列範例顯示如何檢視指定規則群組中的規則：

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

以下輸出是上述範例的截斷回應：

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>停用規則

下列範例會停用應用程式閘道上的規則 `910018` 和 `910017`：

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>強制規則

下列清單中所包含的條件，會導致 WAF 在防護模式中封鎖要求， (在偵測模式中，會將它們記錄為) 例外狀況。 這些無法設定或停用：

* 如果無法剖析要求本文，會導致要求遭到封鎖，除非 (XML、JSON、表單資料中關閉主體檢查) 
* 沒有檔案的要求主體 () 資料長度大於設定的限制
* 要求主體 (包括) 的檔案大於限制
* WAF 引擎發生內部錯誤

CRS 3.x 專用：

* 輸入異常分數超過閾值

## <a name="next-steps"></a>後續步驟

設定已停用的規則之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
