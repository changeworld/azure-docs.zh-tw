---
title: 'Azure 監視器 (Preview 的疑難排解記錄) '
description: 使用 Azure 監視器快速或定期調查問題、對程式碼或設定問題進行疑難排解，或解決支援案例，這通常依賴于大量資料的搜尋，以取得特定的見解。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067358"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Azure 監視器 (Preview 的疑難排解記錄) 
使用 Azure 監視器來快速及/或定期調查問題、對程式碼或設定問題進行疑難排解，或解決支援案例，這通常依賴于大量資料的搜尋，以取得特定的見解。

>[!NOTE]
> * 疑難排解記錄檔處於預覽模式。
>* 請與 [Log Analytics 小組](mailto:orens@microsoft.com) 聯繫任何問題或套用此功能。
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>針對您的程式碼或設定問題進行疑難排解並進行查詢
使用 Azure 監視器疑難排解記錄檔來提取記錄，並以更簡單且更便宜的方式使用 KQL 來調查問題和問題。
疑難排解記錄會提供您疑難排解的基本功能，decrees 您的費用。

> [!NOTE]
>* 疑難排解模式的決定是可設定的。
>* 疑難排解記錄可以套用至特定的資料表（目前位於「容器記錄」和「應用程式追蹤」資料表）。
>* 您可以在4天的免費保留期間內延長費用。
>* 依預設，資料表會繼承工作區保留。 為了避免產生額外費用，建議您變更這些資料表保留期。 [按一下這裡以瞭解如何變更資料表保留](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage)。

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>開啟資料表的疑難排解記錄

若要在您的工作區中開啟疑難排解記錄，您必須使用下列 API 呼叫。
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>檢查給定資料表是否已啟用疑難排解記錄功能
若要檢查給定資料表的疑難排解記錄是否已啟用，您可以使用下列 API 呼叫。

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>檢查工作區中所有資料表的 [疑難排解記錄] 功能是否已啟用
若要檢查哪些資料表已啟用疑難排解記錄，您可以使用下列 API 呼叫。

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>關閉資料表的疑難排解記錄

若要關閉工作區中的疑難排解記錄，您必須使用下列 API 呼叫。
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* 您可以使用任何 REST API 工具來執行命令。 [閱讀更多資訊](https://docs.microsoft.com/rest/api/azure/)
>* 您必須使用持有人權杖進行驗證。 [閱讀更多資訊](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* "IsTroubleshootingAllowed" 旗標–描述服務中是否允許資料表
>* "IsTroubleshootEnabled" 指出是否已啟用資料表的功能-可以開啟或關閉 (true 或 false) 
>* 停用特定資料表的 [isTroubleshootEnabled] 旗標時，重新啟用該旗標的時間可能只有在先前啟用日期之後的一周。
>* 目前只有 (下的資料表才支援此功能。未來的其他 Sku 也將提供支援) -深入 [瞭解定價](https://docs.microsoft.com/services-hub/health/azure_pricing)。

## <a name="query-limitations-for-troubleshooting"></a>疑難排解的查詢限制
標示為「疑難排解記錄」的資料表有一些限制：
*   會取得較少的處理資源，因此不適合大型儀表板、複雜的分析或許多並行 API 呼叫。
*   查詢限制為兩天的時間範圍。
* 清除作業將無法運作–請 [閱讀更多有關清除的資訊](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge)。
* 此服務不支援警示。
## <a name="next-steps"></a>後續步驟
* [撰寫查詢](https://docs.microsoft.com/azure/data-explorer/write-queries)


