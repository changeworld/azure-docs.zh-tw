---
title: 以 Azure 防火牆威脅情報為主的篩選
description: 您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: e51cc8905a7b4a88bb7f7dabaf24bb30159ff86c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655083"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>以 Azure 防火牆威脅情報為主的篩選

您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 包括 Azure 資訊安全中心在內的多項服務皆使用 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)，其同時也支援 Microsoft 威脅情報的運作。<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="防火牆威脅情報" border="false":::

如已啟用威脅情報篩選，則會先處理相關聯的規則，再處理任何 NAT 規則、網路規則或應用程式規則。

您可選擇只在觸發規則時記錄警示，也可選擇警示並拒絕模式。

根據預設，警示模式下會啟用威脅情報篩選。 在區域中的入口網站介面變成可用為止之前，您都無法關閉此功能或變更模式。

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="威脅情報篩選的入口網站介面":::

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

- 請參閱 [Azure 防火牆 Log Analytics 範例](log-analytics-samples.md)
- 了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)
- 檢閱 [Microsoft 安全情報報告](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)