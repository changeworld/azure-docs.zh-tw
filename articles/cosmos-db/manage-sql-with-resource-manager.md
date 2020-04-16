---
title: 使用資源管理員樣本建立與管理 Azure 宇宙資料庫
description: 使用 Azure 資源管理員樣本為 SQL(核心)API 建立及設定 Azure 宇宙資料庫
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390897"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>使用 Azure 資源管理員樣本管理 Azure 宇宙資料庫 SQL(核心)API 資源

在本文中，您會了解如何使用 Azure Resource Manager 範本，協助將 Azure Cosmos DB 帳戶、資料庫和容器的管理自動化。

本文僅顯示 SQL API 帳戶的 Azure 資源管理器範本範例。 您還可以找到[卡桑德拉](manage-cassandra-with-resource-manager.md)、[格雷姆林](manage-gremlin-with-resource-manager.md)、[蒙戈DB](manage-mongodb-with-resource-manager.md)和[表](manage-table-with-resource-manager.md)API 的範本範例。

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>建立 Azure Cosmos 帳號、資料庫和容器

以下 Azure 資源管理員樣本建立具有以下功能的 Azure Cosmos 帳戶:

* 在資料庫等級共用每秒 400 個請求單位 (RU/s) 輸送量的兩個容器。
* 一個容器,具有專用的 400 RU/s 輸送量。

要建立 Azure Cosmos DB 資源,請複製以下範例樣本,然後按照描述部署它,透過[PowerShell](#deploy-via-powershell)或[Azure CLI](#deploy-via-azure-cli)。

* 或者,您可以訪問 Azure[快速入門庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/)並從 Azure 門戶部署範本。
* 您還可以將範本下載到本地電腦或創建新範本,並使用參數`--template-file`指定本地路徑。

> [!IMPORTANT]
>
> * 向 Azure Cosmos 帳戶添加或刪除位置時,不能同時修改其他屬性。 這些操作必須單獨完成。
> * 帳戶名稱限制為 44 個字元,所有小寫字母。
> * 要更改輸送量值,請使用更新的 RU/s 重新提交範本。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> 要創建具有大分區鍵的容器,請修改以前的範本以在`"version":2``partitionKey`物件中包括該屬性。

### <a name="deploy-via-powershell"></a>透過 PowerShell 部署

要使用 PowerShell 部署 Azure 資源管理員樣本,請使用以下指令:

1. **複製**腳本。
2. 選擇 **「嘗試」** 以打開 Azure 雲外殼。
3. 右鍵按一下 Azure 雲殼視窗,然後選擇 **「粘貼**」。

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

您可以選擇使用本地安裝的 PowerShell 版本而不是 Azure 雲外殼部署範本。 您需要安裝 Azure [PowerShell 模組](/powershell/azure/install-az-ps)。 運行`Get-Module -ListAvailable Az`以查找所需的版本。

### <a name="deploy-via-azure-cli"></a>透過 Azure CLI 部署

要使用 Azure CLI 部署 Azure 資源管理員樣本,請使用以下人員:

1. **複製**腳本。
2. 選擇 **「嘗試」** 以打開 Azure 雲外殼。
3. 右鍵按一下 Azure 雲殼視窗,然後選擇 **「粘貼**」。

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

該`az cosmosdb show`命令顯示新創建的 Azure Cosmos 帳戶在預配後。 您可以選擇使用本地安裝的 Azure CLI 版本而不是 Azure 雲端式電腦部署樣本。 有關詳細資訊,請參閱 Azure[命令列介面 (CLI)](/cli/azure/)一文。

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>使用伺服器端功能建立 Azure Cosmos DB 容器

可以使用 Azure 資源管理器樣本建立具有儲存過程、觸發器和使用者定義的函數的 Azure Cosmos DB 容器。

複製以下範例樣本,並按照描述部署它,無論是使用[PowerShell](#deploy-with-powershell)還是[Azure CLI](#deploy-with-azure-cli)。

* 或者,您可以訪問[Azure 快速入門庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/)並從 Azure 門戶部署範本。
* 您還可以將範本下載到本地電腦或創建新範本,並使用參數`--template-file`指定本地路徑。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>使用 PowerShell 部署

要使用 PowerShell 部署 Azure 資源管理員樣本,請使用以下指令:

1. **複製**腳本。
1. 選擇 **「嘗試」** 以打開 Azure 雲外殼。
1. 右鍵按一下 Azure 雲殼體視窗,然後選擇 **「粘貼**」。

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

您可以選擇使用本地安裝的 PowerShell 版本而不是 Azure 雲外殼部署範本。 您需要安裝 Azure [PowerShell 模組](/powershell/azure/install-az-ps)。 運行`Get-Module -ListAvailable Az`以查找所需的版本。

### <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

要使用 Azure CLI 部署 Azure 資源管理員樣本,請使用以下人員:

1. **複製**腳本。
2. 選擇 **「嘗試」** 以打開 Azure 雲外殼。
3. 右鍵按一下 Azure 雲殼視窗,然後選擇 **「粘貼**」。

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
* [Azure 宇宙資料庫資源提供程式架構](/azure/templates/microsoft.documentdb/allversions)
* [Azure 宇宙 DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [排除常見的 Azure 資源管理員部署錯誤](../azure-resource-manager/templates/common-deployment-errors.md)
