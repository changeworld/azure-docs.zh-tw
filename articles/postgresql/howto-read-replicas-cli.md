---
title: 管理讀取複本-Azure CLI、REST API 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何從 Azure CLI 和 REST API 管理適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的讀取複本
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: de74258cadcdf81da211561a84ff06927830e690
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274402"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>從 Azure CLI 建立及管理讀取複本，REST API

在本文中，您將瞭解如何使用 Azure CLI 和 REST API 來建立和管理適用於 PostgreSQL 的 Azure 資料庫中的讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

## <a name="azure-replication-support"></a>Azure 複寫支援
[讀取複本](concepts-read-replicas.md)和[邏輯解碼](concepts-logical.md)兩者都相依于 Postgres 寫 (WAL) 以取得資訊。 這兩個功能需要來自 Postgres 的不同記錄層級。 邏輯解碼需要比讀取複本更高的記錄層級。

若要設定正確的記錄層級，請使用 Azure 複寫支援參數。 Azure 複寫支援有三個設定選項：

* **Off** -將最少的資訊放在 WAL 中。 大部分適用於 PostgreSQL 的 Azure 資料庫伺服器上都無法使用此設定。  
* **複本**-比**關閉**更詳細的資訊。 這是[讀取複本](concepts-read-replicas.md)正常執行所需的最基本記錄層級。 這是大多數伺服器上的預設設定。
* **邏輯**比**複本**更詳細。 這是要使用之邏輯解碼的最低記錄層級。 讀取複本也適用于此設定。

此參數變更之後，必須重新開機伺服器。 就內部而言，此參數會設定 Postgres 參數 `wal_level` 、 `max_replication_slots` 和 `max_wal_senders` 。

## <a name="azure-cli"></a>Azure CLI
您可以使用 Azure CLI 建立及管理讀取複本。

### <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 使用[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)作為主要伺服器。


### <a name="prepare-the-master-server"></a>準備主要伺服器

1. 檢查主伺服器的 `azure.replication_support` 值。 必須至少是複本，讀取複本才能正常執行。

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. 如果不 `azure.replication_support` 是至少一個複本，請設定它。 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. 重新開機伺服器以套用變更。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>建立讀取複本

[Az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create)命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group | myresourcegroup |  將在其中建立複本伺服器的資源群組。  |
| name | mydemoserver-複本 | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫之現有主伺服器的名稱或資源識別碼。 如果您想要複本和主機的資源群組不同，請使用 [資源識別碼]。 |

在下列 CLI 範例中，會在與主伺服器相同的區域中建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要建立跨區域讀取複本，請使用 `--location` 參數。 下列 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。 

如果您尚未在 `azure.replication_support` 一般用途或記憶體優化的主伺服器上，將參數設定為 [**複本**]，並重新啟動伺服器，就會收到錯誤。 建立複本之前，請先完成這兩個步驟。

> [!IMPORTANT]
> 請參閱[讀取複本總覽的考慮一節](concepts-read-replicas.md#considerations)。
>
> 將主伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

### <a name="list-replicas"></a>列出複本
您可以使用[az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)命令來查看主伺服器的複本清單。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用[az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)命令，停止主伺服器和讀取複本之間的複寫。

停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>刪除主要或複本伺服器
若要刪除主要或複本伺服器，請使用[az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)命令。

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
您可以使用[Azure REST API](/rest/api/azure/)來建立及管理讀取複本。

### <a name="prepare-the-master-server"></a>準備主要伺服器

1. 檢查主伺服器的 `azure.replication_support` 值。 必須至少是複本，讀取複本才能正常執行。

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. 如果不 `azure.replication_support` 是至少一個複本，請設定它。

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

2. [重新開機伺服器](/rest/api/postgresql/servers/restart)以套用變更。

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>建立讀取複本
您可以使用[建立 API](/rest/api/postgresql/servers/create)來建立讀取複本：

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

如果您尚未在 `azure.replication_support` 一般用途或記憶體優化的主伺服器上，將參數設定為 [**複本**]，並重新啟動伺服器，就會收到錯誤。 建立複本之前，請先完成這兩個步驟。

使用與主伺服器相同的計算和儲存設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。


> [!IMPORTANT]
> 將主伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

### <a name="list-replicas"></a>列出複本
您可以使用[複本清單 API](/rest/api/postgresql/replicas/listbyserver)來查看主伺服器的複本清單：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用[更新 API](/rest/api/postgresql/servers/update)來停止主伺服器與讀取複本之間的複寫。

停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

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

### <a name="delete-a-master-or-replica-server"></a>刪除主要或複本伺服器
若要刪除主要或複本伺服器，您可以使用[刪除 API](/rest/api/postgresql/servers/delete)：

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>後續步驟
* 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。
* 了解如何[在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。
