---
title: 管理讀取複本-Azure CLI、REST API 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解如何從 Azure CLI 和 REST API 管理適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的讀取複本
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774795"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>從 Azure CLI 建立及管理讀取複本，REST API

在本文中，您將瞭解如何使用 Azure CLI 和 REST API 來建立和管理適用於 PostgreSQL 的 Azure 資料庫中的讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

## <a name="azure-cli"></a>Azure CLI
您可以使用 Azure CLI 建立及管理讀取複本。

### <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 使用[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)作為主要伺服器。


### <a name="prepare-the-master-server"></a>準備主要伺服器
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。

`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 當此靜態參數變更時，需要重新開機伺服器，變更才會生效。

1. 將 `azure.replication_support` 設定為 [複本]。

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> 如果您在嘗試設定 replication_support azure 時收到「指定 Azure CLI 的值無效」錯誤，可能是您的伺服器預設已有複本集。 Bug 導致無法在複本為內部預設值的較新伺服器上正確反映此設定。 <br><br>
> 您可以略過準備主要步驟，並移至建立複本。 <br><br>
> 如果您想要確認您的伺服器是在此類別中，請造訪 Azure 入口網站中的伺服器複寫頁面。 [停用複寫] 將會呈現灰色，而且工具列中的 [新增複本] 將會變成作用中。

2. 重新開機伺服器以套用變更。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>建立讀取複本

[Az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create)命令需要下列參數：

| 設定 | 範例值 | 說明  |
| --- | --- | --- |
| resource-group | myresourcegroup |  將在其中建立複本伺服器的資源群組。  |
| NAME | mydemoserver-複本 | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫之現有主伺服器的名稱或資源識別碼。 |

在下列 CLI 範例中，會在與主伺服器相同的區域中建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要建立跨區域讀取複本，請使用 `--location` 參數。 下列 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[讀取複本概念一文](concepts-read-replicas.md)。 

如果您尚未在一般用途或記憶體優化的主伺服器上將 `azure.replication_support` 參數設定為 [**複本**]，並重新啟動伺服器，則會收到錯誤。 建立複本之前，請先完成這兩個步驟。

使用與主伺服器相同的計算和儲存設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
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
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。

`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 當此靜態參數變更時，需要重新開機伺服器，變更才會生效。

1. 將 `azure.replication_support` 設定為 [複本]。

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
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[讀取複本概念一文](concepts-read-replicas.md)。 

如果您尚未在一般用途或記憶體優化的主伺服器上將 `azure.replication_support` 參數設定為 [**複本**]，並重新啟動伺服器，則會收到錯誤。 建立複本之前，請先完成這兩個步驟。

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
