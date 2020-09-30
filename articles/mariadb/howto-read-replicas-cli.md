---
title: 管理讀取複本-Azure CLI、REST API 適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何使用 Azure CLI 和 REST API，在適用於 MariaDB 的 Azure 資料庫中設定和管理讀取複本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f6b53efdf49538476821ddeaed9bbf4278af0728
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542405"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>如何使用 Azure CLI 和 REST API，在適用於 MariaDB 的 Azure 資料庫中建立及管理讀取複本

在本文中，您將瞭解如何使用 Azure CLI 和 REST API，在適用於 MariaDB 的 Azure 資料庫服務中建立及管理讀取複本。

## <a name="azure-cli"></a>Azure CLI
您可以使用 Azure CLI 來建立及管理讀取複本。

### <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 將作為來源伺服器使用的 [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md) 。 

> [!IMPORTANT]
> 讀取複本功能僅適用于一般用途或記憶體優化定價層中的適用於 MariaDB 的 Azure 資料庫伺服器。 確定來源伺服器是在其中一個定價層。

### <a name="create-a-read-replica"></a>建立讀取複本

> [!IMPORTANT]
> 當您為沒有現有複本的來源建立複本時，來源會先重新開機以準備複寫。 請考慮這一點，並在離峰期間執行這些作業。

使用下列命令可以建立讀取複本伺服器︰

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica create` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| NAME | mydemoreplicaserver | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫之現有來源伺服器的名稱或識別碼。 |

若要建立跨區域讀取複本，請使用 `--location` 參數。 

以下的 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。 

> [!NOTE]
> 系統會以與主要伺服器相同的伺服器設定建立讀取複本。 複本伺服器設定在建立後可以變更。 建議將複本伺服器的設定保留為等於或大於來源的值，以確保複本能夠跟上主伺服器。

### <a name="list-replicas-for-a-source-server"></a>列出來源伺服器的複本

若要查看指定來源伺服器的所有複本，請執行下列命令： 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica list` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| server-name | mydemoserver | 來源伺服器的名稱或識別碼。 |

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦來源與複本之間的複寫停止，就無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

使用下列命令，可以停止複寫至讀取複本伺服器︰

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mariadb server replica stop` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器所在的資源群組。  |
| NAME | mydemoreplicaserver | 要停止複寫的複本伺服器名稱。 |

### <a name="delete-a-replica-server"></a>刪除複本伺服器

您可以執行 **[az 適用于 mariadb server delete](/cli/azure/mariadb/server)** 命令來刪除讀取複本伺服器。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>刪除來源伺服器

> [!IMPORTANT]
> 刪除來源伺服器會停止對所有複本伺服器複寫，並刪除來源伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要刪除來源伺服器，您可以執行 **[az 適用于 mariadb server delete](/cli/azure/mariadb/server)** 命令。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
您可以使用 [Azure REST API](/rest/api/azure/)來建立及管理讀取複本。

### <a name="create-a-read-replica"></a>建立讀取複本
您可以使用 [建立 API](/rest/api/mariadb/servers/create)來建立讀取複本：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。 

如果您尚未將參數設定 `azure.replication_support` 為一般用途或記憶體優化來源伺服器上的 **複本** ，並重新啟動伺服器，則會收到錯誤訊息。 建立複本之前，請先完成這兩個步驟。

使用與主伺服器相同的計算和儲存體設定來建立複本。 建立複本之後，您可以從來源伺服器個別變更數個設定：計算世代、虛擬核心、儲存體及備份保留期限。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。


> [!IMPORTANT]
> 將來源伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

### <a name="list-replicas"></a>列出複本
您可以使用 [複本清單 API](/rest/api/mariadb/replicas/listbyserver)來查看來源伺服器的複本清單：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用 [UPDATE API](/rest/api/mariadb/servers/update)來停止來源伺服器和讀取複本之間的複寫。

停止複寫至來源伺服器和讀取複本之後，就無法復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>刪除來源或複本伺服器
若要刪除來源或複本伺服器，請使用 [刪除 API](/rest/api/mariadb/servers/delete)：

當您刪除來源伺服器時，會停止複寫至所有讀取複本。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)
