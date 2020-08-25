---
title: 如何停止監視您的 Azure Red Hat OpenShift v3 叢集 |Microsoft Docs
description: 本文說明如何使用適用于容器的 Azure 監視器來停止監視您的 Azure Red Hat OpenShift 叢集。
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6ed92cd4cda1f3b5d43cc605d7224236528b94bf
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815431"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>如何停止監視您的 Azure Red Hat OpenShift v3 叢集 

在您啟用 Azure Red Hat OpenShift 3.x 版叢集的監視之後，如果您決定不想再監視叢集，可以使用容器的 Azure 監視器停止監視叢集。 本文說明如何使用提供的 Azure Resource Manager 範本來完成這項工作。 

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

若要一致且重複地從資源群組中移除解決方案資源，有兩個 Azure Resource Manager 範本可供使用。 其中一個是 JSON 範本，用來指定停止監視的設定，另一個則包含參數值，您可以設定這些參數值來指定 OpenShift 叢集資源識別碼與叢集部署所在的 Azure 區域。

若您不熟悉使用範本部署資源的概念，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.65 版版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

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

4. 使用 OpenShift 叢集的值來編輯 **aroResourceId** 和 **aroResourceLocation** 的值，您可以在所選取叢集的 [ **屬性** ] 頁面上找到這些值。

    ![容器屬性頁面](media/container-insights-optout-openshift/cluster-properties-page.png)

5. 將此檔案儲存為本機資料夾的 **OptOutParam.json**。

6. 您已準備好部署此範本。

### <a name="remove-the-solution-using-azure-cli"></a>使用 Azure CLI 來移除解決方案

使用 Linux 上的 Azure CLI 來執行下列命令，以移除解決方案並清除叢集上的設定。

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

在包含範本的資料夾中執行下列 PowerShell 命令，以移除方案並從您的叢集中清除設定。    

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

如果建立工作區只為了支援監視叢集，當您不再需要時，可以手動予以刪除。 如果您不熟悉如何刪除工作區，請參閱 [刪除 Azure Log Analytics 工作區](../platform/delete-workspace.md)。
