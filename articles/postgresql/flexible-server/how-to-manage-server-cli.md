---
title: 管理伺服器-Azure CLI-適用於 PostgreSQL 的 Azure 資料庫-彈性的伺服器
description: 瞭解如何從 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8f078c2c7b22a27c1fd87030361fc56d4495177c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936771"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>使用 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

本文說明如何管理您在 Azure 中部署的彈性伺服器。 管理工作包括計算和儲存體調整、管理員密碼重設，以及查看伺服器詳細資料。

## <a name="prerequisites"></a>必要條件
如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 本文需要您以本機方式執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須使用 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 命令登入您的帳戶。 請注意 **id** 屬性，這是指您的 Azure 帳戶的**訂用帳戶識別碼**。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令來選取您帳戶底下的特定訂用帳戶。 記下 **az login** 輸出中的 **id** 值，作為命令中**訂用帳戶**引數的值。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 若要取得您的所有訂用帳戶，請使用 [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> 如果您尚未建立有彈性的伺服器，請建立一個，以便開始使用此操作說明指南。

## <a name="scale-compute-and-storage"></a>調整計算和儲存體

您可以使用下列命令，輕鬆地擴大計算層級、虛擬核心和儲存體。 您可以查看所有伺服器操作，您可以執行 [az postgres 有彈性的伺服器伺服器總覽](/cli/azure/PostgreSQL/server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

上述引數的詳細資料如下：

**設定** | **範例值** | **說明**
---|---|---
NAME | mydemoserver | 輸入伺服器的唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 此名稱必須包含 3 到 63 個字元。
resource-group | myresourcegroup | 提供 Azure 資源群組的名稱。
sku-name|Standard_D4ds_v3|輸入計算層和大小的名稱。 遵循 Standard_ {VM size} 縮寫的慣例。 如需詳細資訊，請參閱[定價層](../concepts-pricing-tiers.md)。
storage-size | 6144 | 伺服器的儲存體容量 (單位為 MB)。 最小值為5120，而增加1024增量。

> [!IMPORTANT]
> 儲存體無法縮小。 

## <a name="manage-postgresql-databases-on-a-server"></a>管理伺服器上的于 postgresql 資料庫

您可以使用一些應用程式來連線到您的 Azure Database for PostgreSQL 伺服器。 如果您的用戶端電腦已安裝 PostgreSQL，您可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本機執行個體來連線到 Azure PostgreSQL 伺服器。 現在我們將使用 psql 命令列公用程式來連線到 Azure PostgreSQL 伺服器。

1. 執行下列 psql 命令以連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   例如，下列命令會使用存取認證，連線到 PostgreSQL 伺服器 **mydemoserver.postgres.database.azure.com** 上名為 **postgres** 的預設資料庫。 系統提示輸入密碼時，請輸入您選擇的 `<server_admin_password>`。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   連線之後，psql 公用程式會顯示 postgres 提示字元，供您鍵入 sql 命令。 在初始連線輸出中，可能會因為您所使用的 psql 版本與適用於 PostgreSQL 的 Azure 資料庫伺服器版本不同，而顯示警告。

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
   > "psql:FATAL: no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL：需要 SSL 連線。 指定 SSL 選項，然後再試一次。
   >
   > 請確認上述防火牆規則步驟中允許您用戶端的 IP。

2. 在提示字元中輸入下列命令，以建立名為 "postgresdb" 的空白資料庫：

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. 在提示字元中執行下列命令，以將連接切換至新建立的資料庫 **postgresdb**：

    ```bash
    \c postgresdb
    ```

4. 輸入 `\q`，然後選取 Enter 鍵結束 psql。

您已透過 psql 連線至適用於 PostgreSQL 的 Azure 資料庫伺服器，並已建立空的使用者資料庫。

## <a name="reset-admin-password"></a>重設系統管理員密碼
您可以使用此命令變更系統管理員角色的密碼
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 請確定密碼的長度至少為8個字元，最多128個字元。
> 密碼必須包含下列三個類別的字元：英文大寫字母、英文小寫字母、數位和非英數位元。

## <a name="delete-a-server"></a>刪除伺服器

如果您只想要刪除于 postgresql 彈性伺服器，您可以執行 [az postgres 彈性伺服器刪除](/cli/azure/PostgreSQL/server#az-PostgreSQL-flexible-server-delete) 命令。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>下一步

- [瞭解備份和還原概念](concepts-backup-restore.md)
- [調整和監視伺服器](concepts-monitoring.md)
