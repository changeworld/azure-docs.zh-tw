---
title: Azure 宇宙 DB 表 API 的資源管理器範本
description: 使用 Azure 資源管理器範本創建和配置 Azure Cosmos DB 表 API。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246704"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 資源管理器範本管理 Azure 宇宙資料庫表 API 資源

本文介紹如何使用 Azure 資源管理器範本執行不同的操作來自動管理 Azure Cosmos DB 帳戶、資料庫和容器。 本文僅具有表 API 帳戶的示例，用於查找其他 API 類型帳戶的示例，請參閱：使用 Azure Cosmos DB 的 API 的 Azure 資源管理器範本，用於[卡珊多拉](manage-cassandra-with-resource-manager.md)、[格雷姆林](manage-gremlin-with-resource-manager.md)[、MongoDB、SQL](manage-mongodb-with-resource-manager.md)文章。 [SQL](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>創建 Azure 宇宙帳戶和表<a id="create-resource"></a>

使用 Azure 資源管理器範本創建 Azure Cosmos 資料庫資源。 此範本將為表 API 創建一個 Azure Cosmos 帳戶，該表的輸送量為 400 RU/s。 複製範本並部署如下圖所示，或訪問[Azure 快速入門庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/)並從 Azure 門戶進行部署。 您還可以將範本下載到本地電腦或創建新範本，並使用 參數`--template-file`指定本地路徑。

> [!NOTE]
> 帳戶名稱必須小寫，字元數必須為 44 或更少。
> 要更新 RU/s，請重新提交具有更新輸送量屬性值的範本。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>通過 PowerShell 部署

要使用 PowerShell 部署資源管理器範本，**請複製**腳本並選擇 **"嘗試"** 以打開 Azure 雲外殼。 要粘貼腳本，請按右鍵 shell，然後選擇 **"粘貼**：

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

如果選擇使用本地安裝的 PowerShell 版本而不是 Azure 雲外殼，則必須[安裝](/powershell/azure/install-az-ps)Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。

### <a name="deploy-via-the-azure-cli"></a>通過 Azure CLI 部署

要使用 Azure CLI 部署 Azure 資源管理器範本，**請複製**腳本並選擇 **"嘗試它**以打開 Azure 雲外殼"。 要粘貼腳本，請按右鍵 shell，然後選擇 **"粘貼**：

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

該`az cosmosdb show`命令顯示新創建的 Azure Cosmos 帳戶在預配後。 如果選擇使用本地安裝的 Azure CLI 版本而不是使用雲外殼，請參閱[Azure CLI](/cli/azure/)一文。

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)
- [Azure 宇宙資料庫資來源提供者架構](/azure/templates/microsoft.documentdb/allversions)
- [Azure 宇宙 DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [排除常見的 Azure 資源管理器部署錯誤](../azure-resource-manager/templates/common-deployment-errors.md)