---
title: 快速入門：建立伺服器 - Azure CLI - 適用於 MySQL 的 Azure 資料庫
description: 本快速入門說明如何使用 Azure CLI 在 Azure 資源群組中建立 Azure Database for MySQL 伺服器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0c43f1ea81bbc29817e6a2509c9967bf3b5782c9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337280"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure Database for MySQL 伺服器

> [!TIP]
> 請考慮使用較簡單的 [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI 命令 (目前在預覽狀態)。 試用[快速入門](./quickstart-create-server-up-azure-cli.md)。

本快速入門說明如何使用 [Azure Cloud Shell](https://shell.azure.com) 中的 [Azure CLI](/cli/azure/get-started-with-azure-cli) 命令，在五分鐘內建立適用於 MySQL 的 Azure 資料庫伺服器。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> 如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="prerequisites"></a>Prerequisites
本文需要您以本機方式執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須使用 [az login](/cli/azure/reference-index#az-login) 命令登入您的帳戶。 請注意 **id** 屬性，這是指您的 Azure 帳戶的 **訂用帳戶識別碼** 。 

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令來選取您帳戶底下的特定訂用帳戶。 記下 **az login** 輸出中的 **id** 值，作為命令中 **訂用帳戶** 引數的值。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 若要取得您的所有訂用帳戶，請使用 [az account list](/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器
使用 [az group create](/cli/azure/group) 命令建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)，然後在此資源群組內建立您的 MySQL 伺服器。 請提供唯一的名稱。 下列範例會在 `westus` 位置建立名為 `myresourcegroup` 的資源群組。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

使用 [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) 命令建立 Azure Database for MySQL 伺服器。 一部伺服器可以包含多個資料庫。

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

上述引數的詳細資料如下： 

**設定** | **範例值** | **說明**
---|---|---
NAME | mydemoserver | 輸入 Azure Database for MySQL 伺服器的唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 此名稱必須包含 3 到 63 個字元。
resource-group | myresourcegroup | 提供 Azure 資源群組的名稱。
location | westus | 伺服器的 Azure 位置。
admin-user | myadmin | 適用於系統管理員登入的使用者名稱。 此名稱不得為 **azure_superuser** 、 **admin** 、 **administrator** 、 **root** 、 **guest** 或 **public** 。
admin-password | *安全密碼* | 系統管理員使用者的密碼。 其必須包含 8 到 128 個字元。 您的密碼必須包含下列類別中三種類別的字元：英文大寫字母、英文小寫字母、數字及非英數字元。
sku-name|GP_Gen5_2|輸入定價層和計算組庇的名稱。 遵循簡短形式的慣例 {pricing tier} _{compute generation}_ {vCores}。 如需詳細資訊，請參閱[定價層](./concepts-pricing-tiers.md)。

>[!IMPORTANT] 
>- 伺服器上的預設 MySQL 版本為 5.7。 我們目前也提供 5.6 和 8.0 版本。
>- 若要檢視 **az mysql server create** 命令的所有引數，請參閱此 [參考文件](/cli/azure/mysql/server#az-mysql-server-create)。
>- 伺服器上預設會強制執行 SSL。 如需 SSL 的詳細資訊，請參閱[設定 SSL 連線能力](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則 
根據預設，建立的新伺服器受防火牆規則保護，且不可公開存取。 您可以使用 [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule) 命令，在伺服器上設定防火牆規則。 這可讓您在本機連線到伺服器。

下列範例會建立名為 `AllowMyIP` 的防火牆規則，以允許來自特定 IP 位址 192.168.0.1 的連線。 取代您將連線的 IP 位址。 如有需要，您可以使用某個範圍的 IP 位址。 如果不知道如何尋找您的 IP，請移至 [https://whatismyipaddress.com/](https://whatismyipaddress.com/) 以取得您的 IP 位址。

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure Database for MySQL 的連線透過連接埠 3306 進行通訊。 如果您嘗試從公司網路內進行連線，可能不允許透過連接埠 3306 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 3306，否則您無法連線到您的伺服器。

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

結果會採用 JSON 格式。 請記下 **fullyQualifiedDomainName** 和 **administratorLogin** 。
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
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

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>使用 MySQL 命令列用戶端連線到適用於 MySQL 的 Azure 資料庫伺服器
您可以使用熱門的用戶端工具 **[mysql .exe](https://dev.mysql.com/downloads/)** 命令列工具搭配 [Azure Cloud Shell](../cloud-shell/overview.md)，連線到您的伺服器。 或者，您也可以在本機環境中使用 mysql 命令列。
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>清除資源
如果您不需要這些資源來進行其他快速入門/教學課程，可以執行下列命令加以刪除︰ 

```azurecli-interactive
az group delete --name myresourcegroup
```

若您只想要刪除一個新建立的伺服器，則可執行 [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete) 命令。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[使用 MySQL 在 Windows 上建立 PHP 應用程式](../app-service/tutorial-php-mysql-app.md)
