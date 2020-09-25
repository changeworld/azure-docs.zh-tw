---
title: 管理伺服器-Azure CLI 適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 瞭解如何從 Azure CLI 管理適用於 MySQL 的 Azure 資料庫彈性的伺服器。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7701fe91d0e3f78f9596687bf945ba4b11c2d199
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331687"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>使用 Azure CLI 管理適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

本文說明如何管理 Azure 中部署的彈性伺服器 (預覽版) 。 管理工作包括計算和儲存體調整、管理員密碼重設，以及查看伺服器詳細資料。

## <a name="prerequisites"></a>Prerequisites
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

您可以使用下列命令，輕鬆地擴大計算層級、虛擬核心和儲存體。 您可以看到所有伺服器作業，您可以執行 [az mysql 有彈性的伺服器更新](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

上述引數的詳細資料如下：

**設定** | **範例值** | **說明**
---|---|---
NAME | mydemoserver | 輸入 Azure Database for MySQL 伺服器的唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 此名稱必須包含 3 到 63 個字元。
resource-group | myresourcegroup | 提供 Azure 資源群組的名稱。
sku-name|Standard_D4ds_v4|輸入計算層和大小的名稱。 遵循 Standard_ {VM size} 縮寫的慣例。 如需詳細資訊，請參閱[定價層](../concepts-pricing-tiers.md)。
storage-size | 6144 | 伺服器的儲存體容量 (單位為 MB)。 最小值為5120，而增加1024增量。

> [!Important]
> - 儲存體可以相應增加 (不過，您無法相應縮小儲存體) 


## <a name="manage-mysql-databases-on-a-server"></a>管理伺服器上的 MySQL 資料庫。
您可以使用任何這些命令來建立、刪除、列出和查看伺服器上資料庫的資料庫屬性

| Cmdlet | 使用量| 描述 |
| --- | ---| --- |
|[az mysql 彈性-伺服器資料庫建立](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |建立資料庫|
|[az mysql 彈性-伺服器資料庫刪除](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|從伺服器刪除您的資料庫。 此命令不會刪除您的伺服器。 |
|[az mysql 彈性-伺服器資料庫清單](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|列出伺服器上的所有資料庫|
|[az mysql 彈性-伺服器資料庫顯示](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|顯示資料庫的更多詳細資料|

## <a name="update-admin-password"></a>更新系統管理員密碼
您可以使用此命令變更系統管理員角色的密碼
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  請確定密碼的長度至少為8個字元，最多128個字元。
> 密碼必須包含下列三個類別的字元：英文大寫字母、英文小寫字母、數位和非英數位元。

## <a name="delete-a-server"></a>刪除伺服器
如果您只想要刪除 MySQL 具彈性的伺服器，您可以執行 [az MySQL 有彈性的伺服器刪除](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) 命令。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>後續步驟
- [瞭解如何啟動或停止伺服器](how-to-stop-start-server-portal.md)
- [瞭解如何管理虛擬網路](how-to-manage-virtual-network-cli.md)
- [連線問題疑難排解](how-to-troubleshoot-common-connection-issues.md)
- [建立及管理防火牆](how-to-manage-firewall-cli.md)
