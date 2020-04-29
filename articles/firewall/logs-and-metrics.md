---
title: Azure 防火牆記錄和計量的總覽
description: 您可以使用防火牆記錄來監視 Azure 防火牆。 您也可以使用活動記錄來稽核 Azure 防火牆資源上的作業。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76315026"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure 防火牆記錄和計量

您可以使用防火牆記錄來監視 Azure 防火牆。 您也可以使用活動記錄來稽核 Azure 防火牆資源上的作業。

您可以透過入口網站存取其中一些記錄。 您可以將記錄傳送到 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)、儲存體和事件中樞，並在 Azure 監視器記錄中或透過不同的工具 (例如 Excel 和 Power BI) 來分析記錄。

計量非常輕量，而且可以支援近乎即時的案例，使其適用于警示和快速的問題偵測。

## <a name="diagnostic-logs"></a>診斷記錄

 下列診斷記錄可供 Azure 防火牆使用：

* **應用程式規則記錄**

   應用程式規則記錄檔會儲存至儲存體帳戶、串流處理至事件中樞，並（或）只有在您已針對每個 Azure 防火牆啟用時，才會傳送至 Azure 監視器記錄。 符合其中一個已設定應用程式規則的每個新連線，都會產生接受/拒絕連線的記錄。 資料會以 JSON 格式記錄下來，如下列範例所示：

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **網路規則記錄**

   網路規則記錄會儲存至儲存體帳戶、串流處理至事件中樞，並（或）只有在您已針對每個 Azure 防火牆啟用時，才會傳送至 Azure 監視器記錄。 符合其中一個已設定網路規則的每個新連線，都會產生接受/拒絕連線的記錄。 資料會以 JSON 格式記錄下來，如下列範例所示：

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

您有三個選項可用來排序您的記錄：

* **儲存體帳戶**：如果記錄會儲存一段較長的持續期間，並在需要時加以檢閱，則最好針對記錄使用儲存體帳戶。
* **事件中樞**：如果要整合其他安全性資訊和事件管理 (SEIM) 工具以便在資源上取得警示，則事件中樞是絕佳的選項。
* **Azure 監視器記錄**： Azure 監視器記錄最適合用來進行應用程式的一般即時監視，或查看趨勢。

## <a name="activity-logs"></a>活動記錄

   預設會收集活動記錄，您可在 Azure 入口網站中檢視它們。

   您可以使用[azure 活動記錄](../azure-resource-manager/management/view-activity-logs.md)（先前稱為「作業記錄」和「審核記錄」）來查看提交至您的 Azure 訂用帳戶的所有作業。

## <a name="metrics"></a>計量

Azure 監視器中的計量是數值，可描述系統在特定時間的某些層面。 計量會每分鐘收集一次，而且很適合用於警示，因為它們可以經常進行取樣。 您可以使用相對簡單的邏輯快速地引發警示。

下列計量適用于 Azure 防火牆：

- **應用程式規則計數**-已達到應用程式規則的次數。

    單位：計數

- **網路規則計數**-已達到網路規則的次數。

    單位：計數

- 已**處理的資料**-遍歷防火牆的資料量。

    單位：位元組

- **防火牆健全狀況狀態**-根據 SNAT 埠可用性指出防火牆的健全狀況。

    單位：百分比

   此度量有兩個維度：
  - 狀態：可能的值為*狀況良好*、已*降級*、*狀況不良*。
  - 原因：指出防火牆的對應狀態的原因。 

     如果使用 SNAT 埠 > 95%，則會將其視為已耗盡，而健全狀況為50%，狀態 = 已**降級**，原因 =**SNAT 埠**。 防火牆會持續處理流量，而且現有的連線不會受到影響。 不過，可能不會間歇地建立新的連接。

     如果使用 SNAT 埠 < 95%，則會將防火牆視為狀況良好，且健康情況會顯示為100%。

     如果未回報 SNAT 埠的使用方式，健康狀態會顯示為0%。 

- **Snat 埠使用率**-防火牆已使用的 snat 埠百分比。

    單位：百分比

   當您將更多公用 IP 位址新增至防火牆時，會有更多 SNAT 埠可供使用，以減少 SNAT 埠使用率。 此外，當防火牆因不同原因（例如 CPU 或輸送量）而相應放大時，其他 SNAT 埠也會變成可用。 如此一來，指定的 SNAT 埠使用率百分比可能會在您不新增任何公用 IP 位址的情況下關閉，這是因為服務已相應放大。您可以直接控制可用來增加防火牆上可用埠的公用 IP 位址數目。 但是，您無法直接控制防火牆調整。 目前，只會針對前五個公用 IP 位址新增 SNAT 埠。   


## <a name="next-steps"></a>後續步驟

- 若要了解如何監視 Azure 防火牆記錄和計量，請參閱[教學課程：監視 Azure 防火牆記錄](tutorial-diagnostics.md)。

- 若要深入瞭解 Azure 監視器中的計量，請參閱[Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)。
