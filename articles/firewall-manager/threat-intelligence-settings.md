---
title: Azure 防火牆威脅情報設定
description: 您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 136ceeb271bec29bdbfc4572626936ee67f05556
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567335"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure 防火牆威脅情報設定

以威脅情報為基礎的篩選可以針對您的 Azure 防火牆原則進行設定，以警示和拒絕來自和的已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 包括 Azure 資訊安全中心在內的多項服務皆使用 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)，其同時也支援 Microsoft 威脅情報的運作。<br>

如果您已設定以威脅情報為基礎的篩選，則會在任何 NAT 規則、網路規則或應用程式規則之前處理相關聯的規則。

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="威脅情報原則":::

## <a name="threat-intelligence-mode"></a>威脅情報模式

您可以選擇只在觸發規則時記錄警示，也可以選擇 [警示] 和 [拒絕] 模式。

根據預設，警示模式下會啟用威脅情報篩選。

## <a name="allowed-list-addresses"></a>允許的清單位址

您可以設定允許的 IP 位址清單，讓威脅情報不會篩選您指定的任何位址、範圍或子網。



## <a name="logs"></a>記錄

下列記錄摘錄顯示觸發的規則：

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>測試

- **輸出測試**：輸出流量警示極為罕見，因為這表示環境已遭入侵。 已建立可觸發警示的測試 FQDN，以協助測試輸出警示能否正常運作。 對輸出測試使用 **testmaliciousdomain.eastus.cloudapp.azure.com**。

- **輸入測試**：如果防火牆中已設定 DNAT 規則，則應該會看到連入流量的警示。 即使 DNAT 規則僅允許特定來源也一樣，流量還是會被拒絕。 Azure 防火牆不會對所有已知的埠掃描器發出警示，僅對已知也會參與惡意活動的掃描器發出警示。

## <a name="next-steps"></a>後續步驟

- 檢閱 [Microsoft 安全情報報告](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)