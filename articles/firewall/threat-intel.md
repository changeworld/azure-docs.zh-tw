---
title: 以 Azure 防火牆威脅情報為基礎的篩選
description: 您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74168666"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>以 Azure 防火牆威脅情報為基礎的篩選

您可為防火牆啟用威脅情報型篩選，以警示並拒絕來自/傳向已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence)支援 Microsoft 威脅情報，並由多項服務所使用，包括 Azure 資訊安全中心。

![防火牆威脅情報](media/threat-intel/firewall-threat.png)

如果您已啟用以威脅情報為基礎的篩選，則會在任何 NAT 規則、網路規則或應用程式規則之前處理相關聯的規則。

您可以選擇只在觸發規則時記錄警示，也可以選擇 [警示] 和 [拒絕] 模式。

根據預設，會在警示模式中啟用以威脅情報為基礎的篩選。 您無法關閉此功能或變更模式，直到您的區域中的入口網站介面變成可用為止。

![以威脅情報為基礎的篩選入口網站介面](media/threat-intel/threat-intel-ui.png)

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

- **輸出測試**-輸出流量警示應該很罕見發生，因為這表示您的環境已遭入侵。 為了協助測試輸出警示運作，已建立測試 FQDN 來觸發警示。 針對您的輸出測試使用**testmaliciousdomain.eastus.cloudapp.azure.com** 。

- **輸入測試**-如果防火牆上已設定 DNAT 規則，您可以預期會看到連入流量的警示。 即使 DNAT 規則上僅允許特定來源，且流量遭到拒絕，也是如此。 Azure 防火牆不會在所有已知的埠掃描器上發出警示;僅適用于已知也會參與惡意活動的掃描器。

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 防火牆 Log Analytics 範例](log-analytics-samples.md)
- 瞭解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)
- 審查[Microsoft 安全性情報報告](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)