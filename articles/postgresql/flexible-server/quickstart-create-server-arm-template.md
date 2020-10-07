---
title: 快速入門：建立適用於 PostgresSQL 的 Azure DB 彈性伺服器 - ARM 範本
description: 在本快速入門中，了解如何使用 ARM 範本建立適用於 PostgresSQL 的 Azure 資料庫彈性伺服器。
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: a52f88f5ca325027cbac53d2c7e927bcf30da994
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943906"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>快速入門：使用 ARM 範本建立適用於 PostgreSQL 的 Azure 資料庫彈性伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

彈性伺服器是一種受控服務，您用來在雲端執行、管理及調整高可用性的 PostgreSQL 資料庫。 您可以使用 ARM 範本佈建 PostgreSQL 彈性伺服器，以便在伺服器上部署多個伺服器或多個資料庫。

Azure Resource Manager 是 Azure 的部署和管理服務。 其提供管理層，可讓您建立、更新和刪除您 Azure 帳戶中的資源。 您可以使用存取控制、鎖定和標記等管理功能，在部署後保護及組織您的資源。

若要了解 Azure Resource Manager 範本，請參閱[範本部署概觀](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)。

## <a name="prerequisites"></a>Prerequisites

具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>檢閱範本

適用於 PostgresSQL 的 Azure 資料庫伺服器是區域內一或多個資料庫的父資源， 提供適用於其資料庫的管理原則範圍︰登入、防火牆、使用者、角色、設定等等。

範本中定義了下列資源：

- Microsoft.DBforPostgreSQL/flexibleServers

建立 ```postgres-flexible-server-template.json``` 檔案，並將此 ```json``` 指令碼複製到其中。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "String"
        },
        "administratorLoginPassword": {
            "type": "SecureString"
        },
        "location": {
            "type": "String"
        },
        "serverName": {
            "type": "String"
        },
        "serverEdition": {
            "type": "String"
        },
        "vCores": {
            "type": "Int"
        },
        "storageSizeMB": {
            "type": "Int"
        },
        "standbyCount": {
            "type": "Int"
        },
        "availabilityZone": {
            "type": "String"
        },
        "version": {
            "type": "String"
        },
        "tags": {
            "defaultValue": {},
            "type": "Object"
        },
        "firewallRules": {
            "defaultValue": {},
            "type": "Object"
        },
        "vnetData": {
            "defaultValue": {},
            "type": "Object"
        },
        "backupRetentionDays": {
            "type": "Int"
        }
    },
    "variables": {
        "api": "2020-02-14-privatepreview",
        "firewallRules": "[parameters('firewallRules').rules]",
        "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
        "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"vnetId\": \"\", \"vnetName\": \"\", \"vnetResourceGroup\": \"\", \"subnetName\": \"\" }'), parameters('vnetData'))]",
        "finalVnetData": "[json(concat('{ \"DelegatedVnetID\": \"', variables('vnetDataSet').vnetId, '\", \"DelegatedVnetName\": \"', variables('vnetDataSet').vnetName, '\", \"DelegatedVnetResourceGroup\": \"', variables('vnetDataSet').vnetResourceGroup, '\", \"DelegatedSubnetName\": \"', variables('vnetDataSet').subnetName, '\"}'))]"
    },
    "resources": [
        {
            "type": "Microsoft.DBforPostgreSQL/flexibleServers",
            "apiVersion": "[variables('api')]",
            "name": "[parameters('serverName')]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tags')]",
            "sku": {
                "name": "GP_D4s_v3",
                "tier": "[parameters('serverEdition')]",
                "capacity": "[parameters('vCores')]"
            },
            "properties": {
                "version": "[parameters('version')]",
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "publicNetworkAccess": "[variables('publicNetworkAccess')]",
                "VnetInjArgs": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
                "standbyCount": "[parameters('standbyCount')]",
                "storageProfile": {
                    "storageMB": "[parameters('storageSizeMB')]",
                    "backupRetentionDays": "[parameters('backupRetentionDays')]"
                },
                "availabilityZone": "[parameters('availabilityZone')]"
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-08-01",
            "name": "[concat('firewallRules-', copyIndex())]",
            "dependsOn": [
                "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
                            "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
                            "apiVersion": "[variables('api')]",
                            "properties": {
                                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
                            }
                        }
                    ]
                }
            },
            "copy": {
                "name": "firewallRulesIterator",
                "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
                "mode": "Serial"
            },
            "condition": "[greater(length(variables('firewallRules')), 0)]"
        }
    ]
}
```

## <a name="deploy-the-template"></a>部署範本

從下列 PowerShell 程式碼區塊中選取 [試試看]，以開啟 Azure Cloud Shell。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---
## <a name="view-the-deployed-resources"></a>檢閱已部署的資源

請遵循下列步驟，確認是否已在 Azure 中建立您的伺服器。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [適用於 PostgreSQL 的 Azure 資料庫彈性伺服器 (預覽)]。

2. 在資料庫清單中，選取您的新伺服器以檢視**概觀**頁面來管理伺服器。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

您必須輸入新伺服器的名稱，才能檢視適用於 MySQL 的 Azure 資料庫彈性伺服器詳細資料。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

您必須輸入新伺服器的名稱和資源群組，才能檢視適用於 MySQL 的 Azure 資料庫彈性伺服器詳細資料。

```azurecli-interactive
echo "Enter your Azure Database for MySQL Flexible server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL Flexible server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

---

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行[後續步驟](#next-steps)，請保留此資源群組、伺服器和單一資料庫。 後續步驟會示範如何使用不同的方法連接及查詢您的資料庫。

若要刪除資源群組：

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在[入口網站](https://portal.azure.com)中選取要刪除的資源群組。

1. 選取 [刪除資源群組]。
2. 輸入資源群組名稱以確認刪除

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>後續步驟 
> [!div class="nextstepaction"]
> [使用傾印和還原遷移資料庫](../howto-migrate-using-dump-and-restore.md)
