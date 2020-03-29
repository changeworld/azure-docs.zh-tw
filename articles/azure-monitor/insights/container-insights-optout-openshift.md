---
title: 如何停止監視 Azure 紅帽開放移位群集 |微軟文檔
description: 本文介紹如何停止監視 Azure 紅帽 OpenShift 群集，使用 Azure 監視器進行容器。
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: eff5203aeedd3c7ad283b55ba12f0e45a556460d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250725"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>如何停止監視 Azure 紅帽 OpenShift 群集，使用 Azure 監視器監視容器

啟用 Azure 紅帽 OpenShift 群集的監視後，如果您決定不再監視群集，則可以停止監視群集。 本文演示如何使用提供的 Azure 資源管理器範本實現此目的。  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

若要一致且重複地從資源群組中移除解決方案資源，有兩個 Azure Resource Manager 範本可供使用。 一個是 JSON 範本，指定要停止監視的配置，另一個包含您配置為指定群集部署的 OpenShift 群集資源識別碼 和 Azure 區域的參數值。

若您不熟悉使用範本部署資源的概念，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須運行 Azure CLI 版本 2.0.65 或更高版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="create-template"></a>建立範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. 將此檔案儲存為本機資料夾的 **OptOutTemplate.json**。

3. 將下列 JSON 語法貼到您的檔案中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. 使用 OpenShift 群集的值編輯**aroResourceId**和**aroResourceLocation 定位的值**，您可以在所選群集的 **"屬性**"頁上找到該值。

    ![容器屬性頁面](media/container-insights-optout-openshift/cluster-properties-page.png)

5. 將此檔案儲存為本機資料夾的 **OptOutParam.json**。

6. 您已準備好部署此範本。

### <a name="remove-the-solution-using-azure-cli"></a>使用 Azure CLI 來移除解決方案

在 Linux 上使用 Azure CLI 執行以下命令，以刪除解決方案並清理群集上的配置。

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

可能需要幾分鐘的時間才能完成設定變更。 完成後，系統會傳回類似下列包含結果的訊息：

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>使用 PowerShell 移除解決方案

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在包含範本的資料夾中執行以下 PowerShell 命令，以刪除解決方案並從群集中清除配置。    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

可能需要幾分鐘的時間才能完成設定變更。 完成後，系統會傳回類似下列包含結果的訊息：

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>後續步驟

如果建立工作區只為了支援監視叢集，當您不再需要時，可以手動予以刪除。 如果您不熟悉如何刪除工作區，請參閱[刪除 Azure 日誌分析工作區](../../log-analytics/log-analytics-manage-del-workspace.md)。
