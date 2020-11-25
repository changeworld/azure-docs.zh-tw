---
title: 如何停止監視 Azure Kubernetes Service 叢集 | Microsoft Docs
description: 本文說明如何停止使用容器的 Azure 監視器來監視 Azure AKS 叢集。
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 15ec102632258870745f510a98773f70242118be
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96011637"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>如何停止使用容器的 Azure 監視器來監視 Azure Kubernetes Service (AKS)

您在對 AKS 叢集啟用監視後，如果決定不再進行監視，您可以停止監視該叢集。 本文將說明如何使用 Azure CLI 或所提供的 Azure Resource Manager 範本來完成這項作業。  


## <a name="azure-cli"></a>Azure CLI

使用 [az aks disable-addons](/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) 命令可停用容器的 Azure 監視。 此命令會從叢集節點移除代理程式，但不會移除解決方案或已收集並儲存在您 Azure 監視器資源中的資料。  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

若要重新啟用叢集的監視功能，請參閱[使用 Azure CLI 啟用監視功能](container-insights-enable-new-cluster.md#enable-using-azure-cli)。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

若要一致且重複地從資源群組中移除解決方案資源，有兩個 Azure Resource Manager 範本可供使用。 一個是 JSON 範本，用來指定停止監視的設定，而另一個範本包含參數值，您可設定來指定 AKS 叢集資源識別碼與叢集部署所在的資源群組。

若您不熟悉使用範本部署資源的概念，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>範本必須部署在叢集的相同資源群組中。 使用此範本時，如果您省略其他任何屬性或附加元件，可能會導致從叢集移除這些屬性或附加元件。 例如，在您的叢集中執行的 Kubernetes RBAC 原則 *enableRBAC* ，或如果已針對 AKS 叢集指定標記，則為 *aksResourceTagValues* 。  
>

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-template"></a>建立範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
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
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. 使用可所選取叢集的 [屬性] 頁面上找到之 AKS 叢集的值，來編輯 **aksResourceId** 和 **aksResourceLocation** 的值。

    ![容器屬性頁面](media/container-insights-optout/container-properties-page.png)

    當您在 [屬性] 頁面上時，亦請複製 [工作區資源識別碼]。 如果您稍後決定要刪除 Log Analytics 工作區，必須要有這個值。 這部分的程序並不會刪除 Log Analytics 工作區。

    編輯 **aksResourceTagValues** 的值，以符合針對 AKS 叢集指定的現有標記值。

5. 將此檔案儲存為本機資料夾的 **OptOutParam.json**。

6. 您已準備好部署此範本。

### <a name="remove-the-solution-using-azure-cli"></a>使用 Azure CLI 來移除解決方案

在 Linux 上使用 Azure CLI 執行下列命令，來移除解決方案並清除 AKS 叢集上的設定。

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

在包含範本的資料夾中執行下列 PowerShell 命令，來移除解決方案並清除 AKS 叢集中的設定。    

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

如果建立工作區只為了支援監視叢集，當您不再需要時，可以手動予以刪除。 如果您不熟悉如何刪除工作區，請參閱[使用 Azure 入口網站來刪除 Azure Log Analytics 工作區](../platform/delete-workspace.md)。 別忘了稍早在步驟4中複製的 **工作區資源識別碼** ，您將需要這麼做。
