---
title: 使用 Resource Manager 範本來建立和管理 Azure Cosmos DB
description: 使用 Azure Resource Manager 範本來建立和設定適用于 SQL （核心） API 的 Azure Cosmos DB
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 72a87c3b23e0eed6cfbf1614388702443f4e99d0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388014"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本來管理 Azure Cosmos DB SQL （核心） API 資源

在本文中，您會了解如何使用 Azure Resource Manager 範本，協助將 Azure Cosmos DB 帳戶、資料庫和容器的管理自動化。

本文只會顯示 SQL API 帳戶 Azure Resource Manager 範本範例。 您也可以尋找[Cassandra](manage-cassandra-with-resource-manager.md)、 [Gremlin](manage-gremlin-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)和[資料表](manage-table-with-resource-manager.md)api 的範本範例。

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>建立 Azure Cosmos 帳戶、資料庫和容器

下列 Azure Resource Manager 範本會建立 Azure Cosmos 帳戶，其中包含：

* 兩個在資料庫層級共用400要求單位/秒（RU/秒）輸送量的容器。
* 一個容器具有專用的 400 RU/秒輸送量。

若要建立 Azure Cosmos DB 資源，請複製下列範例範本，並依照所述方式部署（透過[PowerShell](#deploy-via-powershell)或[Azure CLI](#deploy-via-azure-cli)）。

* （選擇性）您可以造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/)，並從 Azure 入口網站部署範本。
* 您也可以將範本下載到本機電腦，或建立新的範本，並使用 `--template-file` 參數指定本機路徑。

> [!IMPORTANT]
>
> * 當您新增或移除 Azure Cosmos 帳戶的位置時，您無法同時修改其他屬性。 這些作業必須分別執行。
> * 帳戶名稱限制為44個字元，全部小寫。
> * 若要變更輸送量值，請以已更新的 RU/秒重新提交範本。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> 若要建立具有大型分割區索引鍵的容器，請修改先前的範本，以在 `partitionKey` 物件內包含 `"version":2` 屬性。

### <a name="deploy-via-powershell"></a>透過 PowerShell 部署

若要使用 PowerShell 來部署 Azure Resource Manager 範本：

1. **複製**腳本。
2. 選取 [**試試看**] 以開啟 Azure Cloud Shell。
3. 在 [Azure Cloud Shell] 視窗中按一下滑鼠右鍵，然後選取 [**貼**上]。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

您可以選擇使用本機安裝的 PowerShell 版本來部署範本，而不是 Azure Cloud Shell。 您必須[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 執行 `Get-Module -ListAvailable Az` 來尋找所需的版本。

### <a name="deploy-via-azure-cli"></a>透過 Azure CLI 部署

若要使用 Azure CLI 部署 Azure Resource Manager 範本：

1. **複製**腳本。
2. 選取 [**試試看**] 以開啟 Azure Cloud Shell。
3. 在 [Azure Cloud Shell] 視窗中按一下滑鼠右鍵，然後選取 [**貼**上]。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 命令會在布建之後顯示新建立的 Azure Cosmos 帳戶。 您可以選擇使用本機安裝的 Azure CLI 版本來部署範本，而不是 Azure Cloud Shell。 如需詳細資訊，請參閱[Azure 命令列介面（CLI）一](/cli/azure/)文。

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>建立具有伺服器端功能的 Azure Cosmos DB 容器

您可以使用 Azure Resource Manager 範本來建立具有預存程式、觸發程式和使用者定義函數的 Azure Cosmos DB 容器。

複製下列範例範本，並使用[PowerShell](#deploy-with-powershell)或[Azure CLI](#deploy-with-azure-cli)來加以部署。

* （選擇性）您可以前往[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/)，並從 Azure 入口網站部署範本。
* 您也可以將範本下載到本機電腦，或建立新的範本，並使用 `--template-file` 參數指定本機路徑。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>使用 PowerShell 部署

若要使用 PowerShell 來部署 Azure Resource Manager 範本：

1. **複製**腳本。
1. 選取 [**試試看**] 以開啟 Azure Cloud Shell。
1. 以滑鼠右鍵按一下 [Azure Cloud Shell] 視窗，然後選取 [**貼**上]。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

您可以選擇使用本機安裝的 PowerShell 版本來部署範本，而不是 Azure Cloud Shell。 您必須[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 執行 `Get-Module -ListAvailable Az` 來尋找所需的版本。

### <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

若要使用 Azure CLI 部署 Azure Resource Manager 範本：

1. **複製**腳本。
2. 選取 [**試試看**] 以開啟 Azure Cloud Shell。
3. 在 [Azure Cloud Shell] 視窗中按一下滑鼠右鍵，然後選取 [**貼**上]。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

* [Azure Resource Manager 文件](/azure/azure-resource-manager/)
* [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)
