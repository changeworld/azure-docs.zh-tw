---
title: 基於 Azure 防火牆威脅篩選
description: 您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168666"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure 防火牆威脅基於智慧的篩選

您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 [智慧安全圖](https://www.microsoft.com/en-us/security/operations/intelligence)為 Microsoft 威脅智慧提供電源，並且由多個服務（包括 Azure 安全中心）使用。

![防火牆威脅情報](media/threat-intel/firewall-threat.png)

如果啟用了基於威脅智慧的篩選，則關聯的規則將處理在任何 NAT 規則、網路規則或應用程式規則之前。

您可以選擇在觸發規則時只記錄警報，也可以選擇警報和拒絕模式。

預設情況下，在警報模式下啟用基於威脅智慧的篩選。 在門戶介面在您所在區域中可用之前，不能關閉此功能或更改模式。

![基於威脅情報的篩選門戶介面](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>記錄

以下日誌摘錄顯示了觸發的規則：

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

- **出站測試**- 出站流量警報應很少發生，因為這意味著您的環境已遭到破壞。 為了説明測試出站警報工作，已創建一個測試 FQDN 以觸發警報。 對出站測試使用**testmaliciousdomain.eastus.cloudapp.azure.com。**

- **入站測試**- 如果在防火牆上配置了 DNAT 規則，則可以在傳入流量上看到警報。 即使 DNAT 規則上只允許特定源，否則流量也會被拒絕，也是如此。 Azure 防火牆不在所有已知的埠掃描程式上發出警報;因此，Azure 防火牆不會在所有已知的埠掃描程式上發出警報。僅在已知也從事惡意活動的掃描器上。

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 防火牆日誌分析示例](log-analytics-samples.md)
- 瞭解如何[部署和配置 Azure 防火牆](tutorial-firewall-deploy-portal.md)
- 查看[Microsoft 安全智慧報告](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)