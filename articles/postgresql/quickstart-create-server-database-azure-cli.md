---
title: 快速入門：建立伺服器 - Azure CLI - 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 在本快速入門指南中，您將使用 Azure CLI 來建立適用於 PostgreSQL 的 Azure 資料庫伺服器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 182226a0bb97c9239f1c0d0dc6e7d4d2b8bdbb1c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798868"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立適用於 PostgreSQL 的 Azure 資料庫伺服器

本快速入門說明如何使用 [Azure Cloud Shell](https://shell.azure.com) 中的 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 命令，在五分鐘內建立單一的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!TIP]
> 請考慮使用較簡單的 [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI 命令 (目前在預覽狀態)。 試用[快速入門](./quickstart-create-server-up-azure-cli.md)。

## <a name="prerequisites"></a>先決條件
本文要求您在本機執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令登入您的帳戶。 請注意 **id** 屬性，這是指您的 Azure 帳戶的**訂用帳戶識別碼**。 

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令來選取您帳戶底下的特定訂用帳戶 ID。 記下 **az login** 輸出中的 **id** 值，作為命令中**訂用帳戶**引數的值。 

```azurecli
az account set --subscription <subscription id>
```

如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 若要取得您的所有訂用帳戶，請使用 [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)。

## <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 命令建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)，然後在此資源群組內建立您的 PostgreSQL 伺服器。 請提供唯一的名稱。 下列範例會在 `westus` 位置建立名為 `myresourcegroup` 的資源群組。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

使用 [az postgres server create](/cli/azure/postgres/server) 命令來建立[適用於 PostgreSQL 的 Azure 資料庫伺服器](overview.md)。 一部伺服器可以包含多個資料庫。

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
以下是上述引數的詳細資料： 

**設定** | **範例值** | **說明**
---|---|---
NAME | mydemoserver | 可識別 Azure Database for PostgreSQL 伺服器的唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 其必須包含 3 到 63 個字元。
resource-group | myresourcegroup | Azure 資源群組的名稱。
location | westus | 伺服器的 Azure 位置。
admin-user | myadmin | 適用於系統管理員登入的使用者名稱。 此名稱不得為 **azure_superuser**、**admin**、**administrator**、**root**、**guest** 或 **public**。
admin-password | *安全密碼* | 系統管理員使用者的密碼。 其必須包含 8 到 128 個字元，且來自下列類別的其中三個類別：英文大寫字母、英文小寫字母、數字及非英數字元。
sku-name|GP_Gen5_2| 定價層和計算設定的名稱。 遵循簡短形式的慣例 {pricing tier} _{compute generation}_ {vCores}。 如需詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫定價](https://azure.microsoft.com/pricing/details/postgresql/server/)。

>[!IMPORTANT] 
>- 伺服器上的預設 PostgreSQL 版本為 9.6。 若要查看所有支援的版本，請參閱[支援的 PostgreSQL 主要版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)。
>- 若要檢視 **az postgres server create** 命令的所有引數，請參閱[此參考文件](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)。
>- 伺服器上預設會強制執行 SSL。 如需 SSL 的詳細資訊，請參閱[設定 SSL 連線能力](./concepts-ssl-connection-security.md)。

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則 
根據預設，您建立的伺服器不可公開存取，且受防火牆規則保護。 您可以使用 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) 命令，在伺服器上設定防火牆規則，讓您的本機環境能夠連線到伺服器並存取。 

下列範例會建立名為 `AllowMyIP` 的防火牆規則，以允許來自特定 IP 位址 192.168.0.1 的連線。 請取代為與您的連線來源相對應的 IP 位址或 IP 位址範圍。 如果您不知道 IP 位址，請移至 [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) 加以取得。


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> 為了避免連線問題，請確定您的網路防火牆允許連接埠 5432。 適用於 PostgreSQL 的 Azure 資料庫伺服器會使用該連接埠。 

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，請提供主機資訊和存取認證。

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

結果會採用 JSON 格式。 請記下 **administratorLogin** 和 **fullyQualifiedDomainName** 值。

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>使用 psql 連線到適用於 PostgreSQL 資料庫的 Azure 資料庫伺服器
[psql](https://www.postgresql.org/docs/current/static/app-psql.html) 用戶端是用於連線到 PostgreSQL 伺服器的熱門選擇。 您可以搭配使用 psql 與 [Azure Cloud Shell](../cloud-shell/overview.md) 來連線到您的伺服器。 您也可以在本機環境中使用 psql (如果有的話)。 系統會使用新的 PostgreSQL 伺服器自動建立空的資料庫 **postgres**。 您可以使用該資料庫來與 psql 連線，如下列程式碼所示。 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> 如果您想要使用 URL 路徑來連線到 Postgres，在進行 URL 編碼時，請將使用者名稱中的 @ 符號編碼為 `%40`。 例如，psql 的連接字串會是：
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>清除資源
如果您不需要這些資源來進行其他快速入門或教學課程，可以執行下列命令加以刪除。 

```azurecli-interactive
az group delete --name myresourcegroup
```

如果您只想要刪除一個新建立的伺服器，則可以執行 [az postgres server delete](/cli/azure/postgres/server) 命令。

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md)
> 
> [使用 PostgreSQL 部署 Django Web 應用程式](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [與 Node.JS 應用程式連線](./connect-nodejs.md)

