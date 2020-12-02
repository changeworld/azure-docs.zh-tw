---
title: 管理伺服器-Azure CLI-適用於 PostgreSQL 的 Azure 資料庫-彈性的伺服器
description: 瞭解如何從 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493673"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>使用 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器處於預覽階段。

本文說明如何管理您在 Azure 中部署的彈性伺服器。 管理工作包括計算和儲存體調整、管理員密碼重設，以及查看伺服器詳細資料。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 

您需要在本機執行 Azure CLI 版本2.0 或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

使用 [az login 命令登](/cli/azure/reference-index#az-login) 入您的帳戶。 

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令來選取您的訂用帳戶。 記下 **az 登** 入輸出中的 **識別碼** 值，以便在下列命令中用來做為 **訂閱** 引數的值。 如果您有多個訂用帳戶，請選擇要將資源計費的訂用帳戶。 若要識別所有訂用帳戶，請使用 [az account list](/cli/azure/account#az-account-list) 命令。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> 如果您尚未建立有彈性的伺服器，您將需要這麼做，才能遵循本操作指南。

## <a name="scale-compute-and-storage"></a>調整計算和儲存體

您可以使用下列命令，輕鬆地擴大計算層級、虛擬核心和儲存體。 如需您可以執行的所有伺服器作業清單，請參閱 [az postgres 有彈性的伺服器](/cli/azure/postgres/flexible-server) 總覽。

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

以下是上述程式碼中引數的詳細資料：

**設定** | **範例值** | **說明**
---|---|---
NAME | mydemoserver | 輸入伺服器的唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 其必須包含 3 到 63 個字元。
resource-group | myresourcegroup | 提供 Azure 資源群組的名稱。
sku-name|Standard_D4ds_v3|輸入計算層和大小的名稱。 此值會遵循慣例中 *Standard_ {VM size}* 的慣例。 如需詳細資訊，請參閱[定價層](../concepts-pricing-tiers.md)。
storage-size | 6144 | 輸入伺服器的儲存容量（以 mb 為單位）。 最小值為5120，以1024遞增遞增。

> [!IMPORTANT]
> 您無法縮減儲存體。 

## <a name="manage-postgresql-databases-on-a-server"></a>管理伺服器上的于 postgresql 資料庫

您可以使用一些應用程式來連線到您的 Azure Database for PostgreSQL 伺服器。 如果您的用戶端電腦已安裝于 postgresql，您可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html)的本機實例。 現在，讓我們使用 psql 命令列工具連線到適用於 PostgreSQL 的 Azure 資料庫伺服器。

1. 執行下列 **psql** 命令：

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   例如，下列命令會在您的于 postgresql 伺服器 **mydemoserver.postgres.database.azure.com** 上，透過您的存取認證連接到名為 **postgres** 的預設資料庫。 當系統提示您時，請輸入 `<server_admin_password>` 您選擇的。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   連接之後，psql 工具會顯示 **postgres** 提示字元，您可以在其中輸入 SQL 命令。 如果您使用的 psql 版本與適用於 PostgreSQL 的 Azure 資料庫伺服器上的版本不同，則會在初始連線輸出中出現警告。

   psql 輸出範例：

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > 如果未將防火牆設定為允許用戶端的 IP 位址，就會發生下列錯誤：
   >
   > "psql:FATAL: no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL：需要 SSL 連線。 請指定 SSL 選項，然後再試一次。」
   >
   > 確認防火牆規則中允許您用戶端的 IP 位址。

2. 在提示字元中輸入下列命令，以建立名為 **postgresdb** 的空白資料庫：

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. 在提示字元中執行下列命令，以將連接切換至新建立的資料庫 **postgresdb**：

    ```bash
    \c postgresdb
    ```

4. 輸入  `\q` 並選取 Enter 鍵以結束 psql。

在本節中，您會透過 psql 連接到適用於 PostgreSQL 的 Azure 資料庫伺服器，並建立空白的使用者資料庫。

## <a name="reset-the-admin-password"></a>重設管理員密碼

您可以使用下列命令來變更系統管理員角色的密碼：

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 請選擇最少8個字元，最多128個字元的密碼。 密碼必須包含下列三個類別的字元： 
> - 英文大寫字母
> - 英文小寫字母
> - 數字
> - 非英數位元

## <a name="delete-a-server"></a>刪除伺服器

若要刪除適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器，請執行 [az postgres 彈性伺服器刪除](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) 命令。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>後續步驟

- [瞭解備份和還原概念](concepts-backup-restore.md)
- [調整和監視伺服器](concepts-monitoring.md)