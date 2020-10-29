---
title: 教學課程：建立適用於 MySQL 的 Azure 資料庫 - Azure Resource Manager 範本
description: 本教學課程說明如何使用 Azure Resource Manager 範本佈建和自動化「適用於 MySQL 的 Azure 資料庫」伺服器的部署。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc
ms.openlocfilehash: 66d09503f5db95811f807aa7faa83b92facca992
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543690"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>教學課程：使用 Azure Resource Manager 範本佈建「適用於 MySQL 的 Azure 資料庫」伺服器

[適用於 MySQL 的 Azure 資料庫 REST API](/rest/api/mysql/) 可讓 DevOps 工程師在 Azure 中自動化及整合受控 MySQL 伺服器和資料庫的佈建、設定和作業。  此 API 可用來建立、列舉、管理和刪除「適用於 MySQL 的 Azure 資料庫」服務的 MySQL 伺服器和資料庫。

Azure Resource Manager 會利用基礎的 REST API 來宣告和程式化大規模部署所需的 Azure 資源，使其與「基礎結構即程式碼」的概念相一致。 此範本會將 Azure 資源名稱、SKU、網路、防火牆組態和設定參數化，使其在建立之後可以使用多次。  Azure Resource Manager 範本可使用 [Azure 入口網站](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)或 [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI) 輕易建立。 這些範本可用於應用程式的封裝、標準化和部署自動化，而隨後應用程式可整合到 DevOps CI/CD 管線中。  例如，如果您想要在「適用於 MySQL 的 Azure 資料庫」後端快速部署 Web 應用程式，您可以從 GitHub 資源庫使用此[快速入門範本](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/)執行端對端部署。

在本教學課程中，您將使用 Azure Resource Manager 範本和其他公用程式來了解如何：

> [!div class="checklist"]
> * 使用 Azure Resource Manager 範本在 VNet 服務端點建立「適用於 MySQL 的 Azure 資料庫」伺服器
> * 使用 [mysql 命令列工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)建立資料庫
> * 載入範例資料
> * 查詢資料
> * 更新資料

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本在 VNet 服務端點建立「適用於 MySQL 的 Azure 資料庫」伺服器

若要取得「適用於 MySQL 的 Azure 資料庫」伺服器的 JSON 範本參考，請移至 [Microsoft.DBforMySQL 伺服器](/azure/templates/microsoft.dbformysql/servers)範本參考。 下列範例 JSON 範本可用來建立在 VNet 服務端點上執行「適用於 MySQL 的 Azure 資料庫」的新伺服器。
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
在此要求中，需要自訂的值包括：
+   `name` - 指定 MySQL 伺服器的名稱 (不含網域名稱)。
+   `location` - 為您的 MySQL 伺服器指定有效的 Azure 資料中心區域。 例如 westus2。
+   `properties/version` - 指定要部署的 MySQL 伺服器版本。 例如 5.6 或 5.7。
+   `properties/administratorLogin` - 指定伺服器的 MySQL 管理員登入資料。 管理員登入名稱不可以是 azure_superuser、admin、administrator、root、guest 或 public。
+   `properties/administratorLoginPassword` - 為上述指定的 MySQL 管理員使用者指定密碼。
+   `properties/sslEnforcement` - 指定 [啟用/停用] 以啟用/停用 sslEnforcement。
+   `storageProfile/storageMB` - 指定伺服器所需的最大佈建儲存體大小 (以 MB 為單位)。 例如 5120。
+   `storageProfile/backupRetentionDays` - 指定所需的備份保留期限天數。 例如 7。 
+   `storageProfile/geoRedundantBackup` - 根據異地災害復原需求指定 [啟用/停用]。
+   `sku/tier` - 指定 Basic、GeneralPurpose 或 MemoryOptimized 層的部署。
+   `sku/capacity` - 指定虛擬核心容量。 可能的值包括 2、4、8、16、32 或 64。
+   `sku/family` -指定 Gen5 以選擇伺服器部署的硬體世代。
+   `sku/name` - 指定 TierPrefix_family_capacity。 例如 B_Gen5_1、GP_Gen5_16、MO_Gen5_32。 請參閱[定價層](./concepts-pricing-tiers.md)文件，以了解每個區域和每一層的有效值。
+   `resources/properties/virtualNetworkSubnetId` - 指定 VNet 中要用來放置 Azure MySQL 伺服器的子網路所具備的 Azure 識別碼。 
+   `tags(optional)` - 指定選擇性標記，以標示您用來分類計費用資源的索引鍵值組。

如果您想要建置可為組織自動部署「適用於 MySQL 的 Azure 資料庫」的 Azure Resource Manager 範本，建議您先使用 Azure 快速入門 GitHub 資源庫中的 [Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet)，並以此為基礎進一步建置。 

如果您未曾 Azure Resource Manager 範本，而想要試用，您可以依照下列步驟著手使用：
+   從 Azure 快速入門資源庫中複製或下載範例 [Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet)。  
+   根據您的偏好修改 azuredeploy.parameters.json 以更新參數值，並儲存檔案。 
+   在 Azure CLI 中使用下列命令建立 Azure MySQL 伺服器

您可以在瀏覽器中使用 Azure Cloud Shell 或在自己的電腦上安裝 Azure CLI，以執行本教學課程中的程式碼區塊。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>取得連線資訊
若要連線到您的伺服器，您必須提供主機資訊和存取認證。
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

結果會採用 JSON 格式。 請記下 **fullyQualifiedDomainName** 和 **administratorLogin** 。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>使用 mysql 來連線到伺服器
使用 [mysql 命令列工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)建立對「適用於 MySQL 的 Azure 資料庫」伺服器的連線。 在此範例中，命令是：
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>建立空白資料庫
連線到伺服器之後，請建立一個空白資料庫。
```sql
mysql> CREATE DATABASE mysampledb;
```

在提示字元，執行下列命令以將連線切換到這個新建立的資料庫：
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>在資料庫中建立資料表
既然您已經知道如何連線到適用於 MySQL 資料庫的 Azure 資料庫，請完成一些基本工作。

首先，建立資料表並在其中載入一些資料。 我們將建立一個儲存清查資訊的資料表。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>將資料載入到資料表
既然您已經有資料表，請在其中插入一些資料。 在開啟的命令提示字元視窗，執行下列查詢以插入幾列資料。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

您現在已將兩列範例資料插入到先前建立的資料表。

## <a name="query-and-update-the-data-in-the-tables"></a>查詢並更新資料表中的資料
執行下列查詢，以從資料庫資料表中擷取資訊。
```sql
SELECT * FROM inventory;
```

您也可以更新資料表中的資料。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

當您擷取資料時，資料列會相應地更新。
```sql
SELECT * FROM inventory;
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解：
> [!div class="checklist"]
> * 使用 Azure Resource Manager 範本在 VNet 服務端點建立「適用於 MySQL 的 Azure 資料庫」伺服器
> * 使用 [mysql 命令列工具](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)建立資料庫
> * 載入範例資料
> * 查詢資料
> * 更新資料
> 
> [如何將應用程式連線至 Azure Database for MySQL](./howto-connection-string.md)