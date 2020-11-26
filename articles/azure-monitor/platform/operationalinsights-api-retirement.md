---
title: Azure 監視器 API 淘汰
description: 描述舊版 OperationalInsights 資源提供者 API 的淘汰。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: e2b12d7a2206ab369328563af438c6ef1ea39327
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184105"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API 版本淘汰
Microsoft 在淘汰 API 之前，至少會提供12個月的通知，以順利轉換至較新/支援的版本。 我們發行了新版本 (2020-08-01) **OperationalInsights** 資源提供者 api，並將于2024年2月29日淘汰任何先前的 API 版本。

建議您立即開始使用2020-08-01 版，以獲得新功能的優點，例如 [專用](../log-query/logs-dedicated-clusters.md)叢集、 [客戶管理的金鑰](./customer-managed-keys.md)、 [私用連結](./private-link-security.md) 和 [資料匯出](./logs-data-export.md)。 此外，新的特性和功能和優化只會新增至目前的 API。

2024年2月29日之後，Azure 監視器將不再支援比2020-08-01 更早的 Api 版本。 如果您不想要升級，則在2024年2月29日前，Azure 監視器服務將繼續提供之前的版本傳送的要求。

## <a name="migration-steps"></a>移轉步驟
視您使用的設定方法而定，您應該更新 **REST** 要求和 **Resource Manager 範本** 中的新版本。 請遵循下列範例來更新 API 版本：

1. REST API 要求會在要求的 URL 中使用 API 版本。 將該版本取代為最新版本 (2020-08-01) ，如下列範例所示。

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager 範本會使用資源之 **apiVersion** 屬性中的 API 版本。 將該版本取代為最新版本 (2020-08-01) ，如下列範例所示。


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>後續步驟

- 請參閱 [OperationalInsights WORKSPACE API 的參考](/rest/api/loganalytics/workspaces)。