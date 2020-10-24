---
title: 管理伺服器-Azure CLI-適用於 PostgreSQL 的 Azure 資料庫
description: 瞭解如何從 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫伺服器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: d99634388b9c4db99c996cfccb9bb5f12682f217
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490113"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>使用 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器

本文說明如何管理您在 Azure 中部署的單一伺服器。 管理工作包括計算和儲存體調整、管理員密碼重設，以及查看伺服器詳細資料。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 本文需要您以本機方式執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須使用 [az login](/cli/azure/reference-index#az-login) 命令登入您的帳戶。 請注意 **id** 屬性，這是指您的 Azure 帳戶的**訂用帳戶識別碼**。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account) 命令來選取您帳戶底下的特定訂用帳戶。 記下 **az login** 輸出中的 **id** 值，作為命令中**訂用帳戶**引數的值。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 若要取得您的所有訂用帳戶，請使用 [az account list](/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

如果您尚未建立伺服器，請參閱本 [快速入門](quickstart-create-server-database-azure-cli.md) 以建立伺服器。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>調整計算和儲存體

您可以使用下列命令，輕鬆地擴大定價層、計算和儲存。 您可以查看您可以執行[az postgres server](/cli/azure/mysql/server)的所有伺服器操作總覽

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

上述引數的詳細資料如下：

**設定** | **範例值** | **說明**
---|---|---
NAME | mydemoserver | 輸入適用於 PostgreSQL 的 Azure 資料庫伺服器的唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 此名稱必須包含 3 到 63 個字元。
resource-group | myresourcegroup | 提供 Azure 資源群組的名稱。
sku-name|GP_Gen5_2|輸入定價層和計算組庇的名稱。 遵循簡短形式的慣例 {pricing tier} _{compute generation}_ {vCores}。 如需詳細資訊，請參閱[定價層](./concepts-pricing-tiers.md)。
storage-size | 6144 | 伺服器的儲存體容量 (單位為 MB)。 最小值為5120，而增加1024增量。

> [!Important]
> - 儲存體可以相應增加 (不過，您無法相應縮小儲存體) 
> - 不支援從基本升級至一般用途或記憶體優化定價層。 您可以[使用 bash 腳本](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)或[使用於 postgresql 工作臺](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)，以手動方式擴大


## <a name="manage-postgresql-databases-on-a-server"></a>管理伺服器上的于 postgresql 資料庫。
您可以使用任何這些命令來建立、刪除、列出和查看伺服器上資料庫的資料庫屬性

| Cmdlet | 使用量| 描述 |
| --- | ---| --- |
|[az postgres db create](/cli/azure/sql/db#az-mysql-db-create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |建立資料庫|
|[az postgres db delete](/cli/azure/sql/db#az-mysql-db-delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|從伺服器刪除您的資料庫。 此命令不會刪除您的伺服器。 |
|[az postgres db 清單](/cli/azure/sql/db#az-mysql-db-list)|```az postgres db list -g myresourcegroup -s mydemoserver```|列出伺服器上的所有資料庫|
|[az postgres db show](/cli/azure/sql/db#az-mysql-db-show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|顯示資料庫的更多詳細資料|

## <a name="update-admin-password"></a>更新系統管理員密碼
您可以使用此命令變更系統管理員角色的密碼
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  請確定密碼的長度至少為8個字元，最多128個字元。
> 密碼必須包含下列三個類別的字元：英文大寫字母、英文小寫字母、數位和非英數位元。

## <a name="delete-a-server"></a>刪除伺服器
如果您只想要刪除于 postgresql 單一伺服器，您可以執行 [az postgres server delete](/cli/azure/mysql/server#az-mysql-server-delete) 命令。

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>後續步驟
- [重新啟動伺服器](howto-restart-server-cli.md)
- [還原處于不良狀態的伺服器](howto-restore-server-cli.md)
- [監視和微調伺服器](concepts-monitoring.md)