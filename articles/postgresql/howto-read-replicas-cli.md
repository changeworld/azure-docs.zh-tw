---
title: 管理讀取複本-Azure CLI、REST API 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何從 Azure CLI 和 REST API 管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器中的讀取複本
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2fa8794066739302d2f32acb13c936c524dc89a8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422343"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>從 Azure CLI 建立及管理讀取複本，REST API

在本文中，您將瞭解如何使用 Azure CLI 和 REST API，在適用於 PostgreSQL 的 Azure 資料庫中建立及管理讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

## <a name="azure-replication-support"></a>Azure 複寫支援
[讀取複本](concepts-read-replicas.md) 和 [邏輯解碼](concepts-logical.md) 都取決於 Postgres 的預先寫入記錄 (WAL) 取得資訊。 這兩個功能需要來自 Postgres 的不同記錄層級。 邏輯解碼需要比讀取複本更高層級的記錄。

若要設定正確的記錄層級，請使用 Azure 複寫支援參數。 Azure 複寫支援有三個設定選項：

* **Off** -將最少的資訊放在 WAL 中。 這項設定無法在大部分的適用於 PostgreSQL 的 Azure 資料庫伺服器上使用。  
* **複本** -更 **詳細的資訊。** 這是 [讀取複本](concepts-read-replicas.md) 正常運作所需的最小記錄層級。 這項設定在大部分伺服器上是預設值。
* **邏輯** 更詳細的資訊比 **複本** 更詳細。 這是要讓邏輯解碼正常運作的最小記錄層級。 讀取複本也可在此設定中運作。


> [!NOTE]
> 針對持續大量寫入大量寫入的主要工作負載部署讀取複本時，複寫延遲可能會持續成長，而且可能永遠無法趕上主要工作負載。 這也可能會增加主資料庫的儲存體使用量，因為 WAL 檔案在複本收到時，不會被刪除。

## <a name="azure-cli"></a>Azure CLI
您可以使用 Azure CLI 來建立及管理讀取複本。

### <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)作為主伺服器。


### <a name="prepare-the-primary-server"></a>準備主伺服器

1. 檢查主伺服器的 `azure.replication_support` 值。 至少要有複本才能讓讀取複本正常運作。

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. 如果不 `azure.replication_support` 是至少複本，請設定它。 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. 重新開機伺服器以套用變更。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>建立讀取複本

[Az postgres server replica create](/cli/azure/postgres/server/replica#az-postgres-server-replica-create)命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group | myresourcegroup |  將在其中建立複本伺服器的資源群組。  |
| NAME | mydemoserver-複本 | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫之現有主伺服器的名稱或資源識別碼。 如果您希望複本和 master 的資源群組不同，請使用資源識別碼。 |

在以下的 CLI 範例中，會在與主伺服器相同的區域中建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要建立跨區域讀取複本，請使用 `--location` 參數。 以下的 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。 

如果您 `azure.replication_support` 未在一般用途或記憶體優化的主伺服器上將參數設定為 **複本** ，並重新啟動伺服器，則會收到錯誤訊息。 建立複本之前，請先完成這兩個步驟。

> [!IMPORTANT]
> 請參閱 [讀取複本總覽的考慮一節](concepts-read-replicas.md#considerations)。
>
> 將主伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

### <a name="list-replicas"></a>列出複本
您可以使用 [az postgres server replica list](/cli/azure/postgres/server/replica#az-postgres-server-replica-list) 命令來查看主伺服器的複本清單。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用 [az postgres server replica stop](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop) 命令，停止主伺服器和讀取複本之間的複寫。

停止複寫到主伺服器和讀取複本之後，就無法復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>刪除主要或複本伺服器
若要刪除主要或複本伺服器，請使用 [az postgres server delete](/cli/azure/postgres/server#az-postgres-server-delete) 命令。

當您刪除主伺服器時，將會停止複寫至所有讀取複本。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
您可以使用 [Azure REST API](/rest/api/azure/)來建立及管理讀取複本。

### <a name="prepare-the-primary-server"></a>準備主伺服器

1. 檢查主伺服器的 `azure.replication_support` 值。 至少要有複本才能讓讀取複本正常運作。

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. 如果不 `azure.replication_support` 是至少複本，請設定它。

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [重新開機伺服器](/rest/api/postgresql/servers/restart) 以套用變更。

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>建立讀取複本
您可以使用 [建立 API](/rest/api/postgresql/servers/create)來建立讀取複本：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。 

如果您 `azure.replication_support` 未在一般用途或記憶體優化的主伺服器上將參數設定為 **複本** ，並重新啟動伺服器，則會收到錯誤訊息。 建立複本之前，請先完成這兩個步驟。

使用與主伺服器相同的計算和儲存體設定來建立複本。 建立複本之後，您可以從主伺服器個別變更數個設定：計算世代、虛擬核心、儲存體及備份保留期限。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。


> [!IMPORTANT]
> 將主伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

### <a name="list-replicas"></a>列出複本
您可以使用 [複本清單 API](/rest/api/postgresql/replicas/listbyserver)來查看主伺服器的複本清單：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用 [UPDATE API](/rest/api/postgresql/servers/update)來停止主伺服器和讀取複本之間的複寫。

停止複寫到主伺服器和讀取複本之後，就無法復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>刪除主要或複本伺服器
若要刪除主要或複本伺服器，請使用 [刪除 API](/rest/api/postgresql/servers/delete)：

當您刪除主伺服器時，將會停止複寫至所有讀取複本。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>後續步驟
* 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。
* 了解如何[在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。