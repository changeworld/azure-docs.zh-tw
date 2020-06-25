---
title: Azure 監視器的 Resource Manager 範本範例
description: 使用 Resource Manager 範本部署和設定 Azure 監視器功能
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: e71cd5f3989a175f883f3a08b4f55d36a154dcee
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753480"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Azure 監視器的 Resource Manager 範本範例

Azure 監視器可使用 [Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)進行大規模的部署和設定。 下列文章提供不同 Azure 監視器功能的範例範本。 這些範例可針對您的特定需求進行修改，並使用任何部署 Resource Manager 範本的標準方法進行部署。 

## <a name="deploying-the-sample-templates"></a>部署範例範本
使用範例的基本步驟如下：

1. 複製範本，並儲存為 JSON 檔案。
2. 修改環境的參數，並儲存為 JSON 檔案。
4. 使用 [Resource Manager 範本的任何部署方法](../../azure-resource-manager/templates/deploy-powershell.md)來部署範本。 

例如，使用下列命令，透過 PowerShell 或 Azure CLI 將範本和參數檔案部署至資源群組。


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>範例範本清單

- [代理程式](resource-manager-agent.md) - 部署和設定 Log Analytics 代理程式和診斷擴充功能。
- 警示
  - [記錄警示規則](resource-manager-alerts-log.md) - 來自記錄查詢和 Azure 活動記錄的警示。
  - [計量警示規則](resource-manager-alerts-metric.md) - 來自計量、使用不同邏輯類型的警示。
- Application Insights - 即將推出。
- [診斷設定](resource-manager-diagnostic-settings.md) - 建立診斷設定，以轉送來自不同資源類型的記錄和計量。
- [記錄查詢](resource-manager-log-queries.md) - 在 Log Analytics 工作區中建立已儲存的記錄查詢。
- [Log Analytics 工作區](resource-manager-workspace.md) - 建立 Log Analytics 工作區，並從 Log Analytics 代理程式設定不同資料來源的集合。
- [活頁簿](resource-manager-workbooks.md) - 建立活頁簿。
- [適用於容器的 Azure 監視器](resource-manager-container-insights.md) - 將叢集上線至適用於容器的 Azure 監視器。
- [適用於 VM 的 Azure 監視器](resource-manager-vminsights.md) - 將虛擬機器上線至適用於 VM 的 Azure 監視器。



## <a name="next-steps"></a>後續步驟

- 深入了解 [Resource Manager 範本](../../azure-resource-manager/templates/overview.md)