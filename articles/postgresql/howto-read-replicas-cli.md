---
title: 管理讀取副本 - Azure CLI，REST API - 用於 PostgreSQL 的 Azure 資料庫 - 單個伺服器
description: 瞭解如何在 Azure 資料庫中管理讀取副本，用於 PostgreSQL - Azure CLI 和 REST API 中的單個伺服器
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774795"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>從 Azure CLI REST API 創建和管理讀取副本

在本文中，您將瞭解如何使用 Azure CLI 和 REST API 在 Azure 資料庫中創建和管理讀取副本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

## <a name="azure-cli"></a>Azure CLI
可以使用 Azure CLI 創建和管理讀取副本。

### <a name="prerequisites"></a>Prerequisites

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 使用[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)作為主要伺服器。


### <a name="prepare-the-master-server"></a>準備主要伺服器
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。

`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 更改此靜態參數時，需要重新開機伺服器才能使更改生效。

1. 設置為`azure.replication_support`REPLICA。

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> 如果在嘗試從 Azure CLI 設置 azure.replication_support 時收到錯誤"給定的無效值"，則預設情況下伺服器可能已設置了 REPLICA。 一個 Bug 阻止此設置正確反映在較新的伺服器上，其中 REPLICA 是內部預設值。 <br><br>
> 您可以跳過準備主步驟，然後轉到棄置站台。 <br><br>
> 如果要確認伺服器屬於此類別，請訪問 Azure 門戶中的伺服器複製頁。 "禁用複製"將灰顯，工具列中"添加副本"將處於活動狀態。

2. 重新開機伺服器以應用更改。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>建立讀取複本

[az postgres 伺服器複本創建](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create)命令需要以下參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group | myresourcegroup |  將棄置站台伺服器的資源組。  |
| NAME | 我的演示伺服器複本 | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複製的現有主伺服器的名稱或資源識別碼。 |

在下面的 CLI 示例中，副本是在與主副本相同的區域中創建的。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

要創建跨區域讀取副本，請使用 參數`--location`。 下面的 CLI 示例在美國西部棄置站台。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 要詳細瞭解可以在哪些區域棄置站台，請訪問[讀取副本概念一文](concepts-read-replicas.md)。 

如果尚未將`azure.replication_support`參數設置為通用或記憶體優化主伺服器上**的 REPLICA**並重新啟動伺服器，則收到錯誤。 在棄置站台之前完成這兩個步驟。

副本使用與主副本相同的計算和存儲設置創建。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主伺服器設置更新為新值之前，將副本設置更新為相等或更大的值。 此操作可説明副本跟上對主副本所做的任何更改。

### <a name="list-replicas"></a>列出副本
您可以使用[az postgres 伺服器複本清單](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)命令查看主伺服器的副本清單。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
通過使用[az postgres 伺服器複本停止](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)命令，可以停止主伺服器和讀取副本之間的複製。

停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>刪除主伺服器或副本伺服器
要刪除主伺服器或副本伺服器，請使用[az postgres 伺服器刪除](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)命令。

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
可以使用[Azure REST API](/rest/api/azure/)創建和管理讀取副本。

### <a name="prepare-the-master-server"></a>準備主要伺服器
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。

`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 更改此靜態參數時，需要重新開機伺服器才能使更改生效。

1. 設置為`azure.replication_support`REPLICA。

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

2. [重新開機伺服器](/rest/api/postgresql/servers/restart)以應用更改。

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>建立讀取複本
可以使用[創建 API](/rest/api/postgresql/servers/create)創建讀取副本：

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
> 要詳細瞭解可以在哪些區域棄置站台，請訪問[讀取副本概念一文](concepts-read-replicas.md)。 

如果尚未將`azure.replication_support`參數設置為通用或記憶體優化主伺服器上**的 REPLICA**並重新啟動伺服器，則收到錯誤。 在棄置站台之前完成這兩個步驟。

副本使用與主副本相同的計算和存儲設置創建。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。


> [!IMPORTANT]
> 在將主伺服器設置更新為新值之前，將副本設置更新為相等或更大的值。 此操作可説明副本跟上對主副本所做的任何更改。

### <a name="list-replicas"></a>列出副本
您可以使用[副本清單 API](/rest/api/postgresql/replicas/listbyserver)查看主伺服器的副本清單：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用[更新 API](/rest/api/postgresql/servers/update)停止主伺服器和讀取副本之間的複製。

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

### <a name="delete-a-master-or-replica-server"></a>刪除主伺服器或副本伺服器
要刪除主伺服器或副本伺服器，請使用刪除[API](/rest/api/postgresql/servers/delete)：

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>後續步驟
* 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。
* 了解如何[在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。
