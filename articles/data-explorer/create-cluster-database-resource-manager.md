---
title: 使用 Azure 資源管理器範本創建 Azure 資料資源管理器群集和資料庫
description: 瞭解如何使用 Azure 資源管理器範本創建 Azure 資料資源管理器群集和資料庫
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911955"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本創建 Azure 資料資源管理器群集和資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [電源外殼](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure 資源管理器範本](create-cluster-database-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 

在本文中，可以使用[Azure 資源管理器範本](../azure-resource-manager/management/overview.md)創建 Azure 資料資源管理器群集和資料庫。 本文說明如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。 有關創建範本的資訊，請參閱創作[Azure 資源管理器範本](/azure/azure-resource-manager/resource-group-authoring-templates)。 有關要在範本中使用的 JSON 語法和屬性，請參閱[Microsoft.Kusto 資源類型](/azure/templates/microsoft.kusto/allversions)。

如果沒有 Azure 訂閱，請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>用於群集和資料庫創建的 Azure 資源管理器範本

在本文中，您可以使用[現有的快速入門範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

若要尋找更多範本範例，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)。

## <a name="deploy-the-template-and-verify-template-deployment"></a>部署範本並驗證範本部署

可以使用[Azure 門戶](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment)或使用[powershell](#use-powershell-to-deploy-the-template-and-verify-template-deployment)來部署 Azure 資源管理器範本。

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>使用 Azure 門戶部署範本並驗證範本部署

1. 要創建群集和資料庫，請使用以下按鈕啟動部署。 按一下滑鼠右鍵並選取 [在新視窗中開啟]****，以便依照本文中的其餘步驟操作。

    [![部署到 Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    [部署至 Azure]**** 按鈕可將您帶往 Azure 入口網站，填寫部署表單。

    ![部署至 Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    可以使用[表單在 Azure 門戶中編輯和部署範本](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template)。

1. 完成**基礎知識**和**設置**部分。 選擇唯一的群集和資料庫名稱。
創建 Azure 資料資源管理器群集和資料庫需要幾分鐘時間。

1. 要驗證部署，請打開[Azure 門戶](https://portal.azure.com)中的資源組以查找新的群集和資料庫。 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>使用電源外殼部署範本並驗證範本部署

#### <a name="deploy-the-template-using-powershell"></a>使用電源外殼部署範本

1. 選取下列程式碼區塊中的 [試用]****，然後依照指示登入 Azure Cloud Shell。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 選取 [複製]**** 來複製 PowerShell 指令碼。
1. 以滑鼠右鍵按一下殼層主控台，然後選取 [貼上]****。
創建 Azure 資料資源管理器群集和資料庫需要幾分鐘時間。

#### <a name="verify-the-deployment-using-powershell"></a>使用 PowerShell 驗證部署

要驗證部署，請使用以下 Azure PowerShell 腳本。  如果雲外殼仍然打開，則無需複製/運行第一行（讀取主機）。 有關在 PowerShell 中管理 Azure 資料資源管理器資源的詳細資訊，請閱讀[Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0)。 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

[將資料引入 Azure 資料資源管理器群集和資料庫](ingest-data-overview.md)
