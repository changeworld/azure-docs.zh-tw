---
title: 快速入門：建立適用於 MySQL 的 Azure DB - 彈性伺服器 - ARM 範本
description: 在本快速入門中，了解如何使用 ARM 範本建立適用於 MySQL 的 Azure 資料庫 - 彈性伺服器。
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 18366069fd7273afe33b538d2bd69ac8a99689c6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943879"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>快速入門：使用 ARM 範本建立適用於 MySQL 的 Azure 資料庫 - 彈性伺服器 (預覽)


> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態

適用於 MySQL 的 Azure 資料庫 - 彈性伺服器 (預覽) 是一個受控服務，您可用來在雲端執行、管理及調整高可用性 MySQL 資料庫。 您可以使用 ARM 範本佈建彈性伺服器，以便在伺服器上部署多個伺服器或多個資料庫。

[ARM 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)是一個 JavaScript 物件標記法 (JSON) 檔案，會定義專案的基礎結構和設定。 範本會使用宣告式語法，可讓您陳述您要部署的項目，而不需要撰寫一連串程式設計命令來加以建立。

## <a name="prerequisites"></a>Prerequisites

具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>檢閱範本

適用於 MySQL 的 Azure 資料庫彈性伺服器是區域內一或多個資料庫的父資源， 提供適用於其資料庫的管理原則範圍︰登入、防火牆、使用者、角色、設定等等。

範本中定義了下列資源：

- Microsoft.DBforMySQL/flexibleServers

建立 ```mysql-flexible-server-template.json``` 檔案，並將此 ```json``` 指令碼複製到其中。

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
            "type": "Microsoft.DBforMySQL/flexibleServers",
            "apiVersion": "[variables('api')]",
            "name": "[parameters('serverName')]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tags')]",
            "sku": {
                "name": "Standard_D4ds_v4",
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
                "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
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

從下列 PowerShell 程式碼區塊中選取 [試試看]，以開啟 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="view-the-deployed-resources"></a>檢閱已部署的資源

請遵循下列步驟，確認是否已在 Azure 中建立您的伺服器。

### <a name="azure-portal"></a>Azure 入口網站

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [適用於 MySQL 的 Azure 資料庫伺服器]。

2. 在資料庫清單中，選取您的新伺服器。 新適用於 MySQL 的 Azure 資料庫伺服器的 [概觀] 頁面隨即出現。

### <a name="powershell"></a>PowerShell

您必須輸入新伺服器的名稱，才能檢視適用於 MySQL 的 Azure 資料庫彈性伺服器詳細資料。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

您必須輸入新伺服器的名稱和資源群組，才能檢視適用於 MySQL 的 Azure 資料庫彈性伺服器詳細資料。

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行[後續步驟](#next-steps)，請保留此資源群組、伺服器和單一資料庫。 後續步驟會示範如何使用不同的方法連接及查詢您的資料庫。

若要刪除資源群組：

### <a name="azure-portal"></a>Azure 入口網站

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [資源群組]。

2. 在 [資源群組] 清單中，選擇資源群組的名稱。

3. 在資源群組的 [概觀] 頁面中，選取 [刪除資源群組]。

4. 在確認對話方塊凹輸入您的資源群組名稱，然後選取 [刪除]。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>後續步驟

如需逐步教學課程，以引導您完成建立 ARM 範本的流程，請參閱：

> [!div class="nextstepaction"]
> [教學課程：建立及部署您的第一個 ARM 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

如需使用 MySQL 建置具有 App Service 之應用程式的逐步教學課程，請參閱：

> [!div class="nextstepaction"]
>[使用 MySQL 建置 PHP (Laravel) Web 應用程式](tutorial-php-database-app.md)
