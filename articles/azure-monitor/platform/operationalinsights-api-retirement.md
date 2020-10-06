---
title: Azure 監視器 API 淘汰
description: 描述舊版 OperationalInsights 資源提供者 API 的淘汰。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 708901dbc37daa075cf38d3f9ef046ae658ea979
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744763"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API 版本淘汰
Microsoft 在淘汰 API 之前，至少會提供12個月的通知，以順利轉換至較新/支援的版本。 我們發行了新版本 (2020-08-01) **OperationalInsights** 資源提供者 api，並將于2023年10月31日淘汰任何先前的 API 版本。 由於新的特性和功能和優化只會新增至目前的 API，因此您應該儘早升級至最新的 API 版本。

2023年10月31日之後，Azure 監視器將不再支援比2020-08-01 更早的 Api 版本。 如果您不想要升級，則在2023年10月31日前，Azure 監視器服務將繼續提供之前的版本所傳送的要求。

根據您使用的設定方法，您應該根據下列範例更新 REST 要求中的新版本和 Resource Manager 範本：


## <a name="rest"></a>REST
REST API 要求會在要求的 URL 中使用 API 版本。 以最新版本取代該版本，如下列範例所示。

```rest
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
```

## <a name="azure-resource-manager"></a>Azure Resource Manager
Resource Manager 範本會使用資源之 **apiVersion** 屬性中的 API 版本。 以最新版本取代該版本，如下列範例所示。


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

- 請參閱 [OPERATIONALINSIGHTS API 的參考](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions)。
