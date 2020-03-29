---
title: Azure 防火牆日誌和指標概述
description: 您可以使用防火牆記錄來監視 Azure 防火牆。 您也可以使用活動記錄來稽核 Azure 防火牆資源上的作業。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315026"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure 防火牆記錄和計量

您可以使用防火牆記錄來監視 Azure 防火牆。 您也可以使用活動記錄來稽核 Azure 防火牆資源上的作業。

您可以透過入口網站存取其中一些記錄。 您可以將記錄傳送到 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)、儲存體和事件中樞，並在 Azure 監視器記錄中或透過不同的工具 (例如 Excel 和 Power BI) 來分析記錄。

指標是羽量級的，可以支援接近即時的方案，使它們可用於警報和快速問題檢測。

## <a name="diagnostic-logs"></a>診斷記錄

 下列診斷記錄可供 Azure 防火牆使用：

* **應用程式規則記錄**

   應用程式規則日誌將保存到存儲帳戶，資料流到事件中心和/或發送到 Azure 監視器日誌，僅當您為每個 Azure 防火牆啟用它時。 符合其中一個已設定應用程式規則的每個新連線，都會產生接受/拒絕連線的記錄。 資料會以 JSON 格式記錄下來，如下列範例所示：

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

   網路規則日誌將保存到存儲帳戶，資料流到事件中心和/或發送到 Azure 監視器日誌，僅當您為每個 Azure 防火牆啟用它時。 符合其中一個已設定網路規則的每個新連線，都會產生接受/拒絕連線的記錄。 資料會以 JSON 格式記錄下來，如下列範例所示：

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
* **Azure 監視器日誌**：Azure 監視器日誌最適合用於應用程式的總體即時監視或查看趨勢。

## <a name="activity-logs"></a>活動記錄

   預設會收集活動記錄，您可在 Azure 入口網站中檢視它們。

   可以使用[Azure 活動日誌](../azure-resource-manager/management/view-activity-logs.md)（以前稱為動作記錄和稽核記錄）查看提交到 Azure 訂閱的所有操作。

## <a name="metrics"></a>計量

Azure 監視器中的指標是描述特定時間系統某些方面的數值。 指標每分鐘收集一次，並且可用於警報，因為它們可以經常採樣。 使用相對簡單的邏輯可以快速觸發警報。

以下指標可用於 Azure 防火牆：

- **應用程式規則命中計數**- 應用程式規則被命中的次數。

    單位：計數

- **網路規則命中計數**- 網路規則被擊中的次數。

    單位：計數

- **已處理的資料**- 遍歷防火牆的資料量。

    單位：位元組

- **防火牆運行狀況狀態**- 指示基於 SNAT 埠可用性的防火牆運行狀況。

    單位：百分比

   此指標有兩個維度：
  - 狀態：可能的值是 *"正常**"、降級*、*不正常*。
  - 原因：指示防火牆相應狀態的原因。 

     如果使用 SNAT 埠> 95%，則它們被視為已耗盡，運行狀況為 50%，狀態 =**降級**和原因=**SNAT 埠**。 防火牆持續處理流量，現有連接不受影響。 但是，新連接可能不會間歇性地建立。

     如果 sNAT 埠< 95% 使用，則防火牆被視為正常，運行狀況顯示為 100%。

     如果未報告 SNAT 埠使用方式，則運行狀況顯示為 0%。 

- **SNAT 埠利用率**- 防火牆已使用的 SNAT 埠的百分比。

    單位：百分比

   當您向防火牆添加更多公共 IP 位址時，可以使用更多 SNAT 埠，從而降低 SNAT 埠利用率。 此外，當防火牆因不同原因（例如 CPU 或輸送量）而擴展時，其他 SNAT 埠也會可用。 因此，實際上，給定比例的 SNAT 埠利用率可能會下降，而無需添加任何公共 IP 位址，只是因為服務已擴展。您可以直接控制可用公共 IP 位址的數量，以增加防火牆上的可用埠。 但是，您無法直接控制防火牆擴展。 目前，僅為前五個公共 IP 位址添加了 SNAT 埠。   


## <a name="next-steps"></a>後續步驟

- 若要了解如何監視 Azure 防火牆記錄和計量，請參閱[教學課程：監視 Azure 防火牆記錄](tutorial-diagnostics.md)。

- 要瞭解有關 Azure 監視器中的指標的更多詳細資訊，請參閱[Azure 監視器 中的指標](../azure-monitor/platform/data-platform-metrics.md)。
