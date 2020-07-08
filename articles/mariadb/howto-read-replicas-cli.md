---
title: 管理讀取複本-Azure CLI、REST API 適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何使用 Azure CLI 和 REST API 來設定和管理適用於 MariaDB 的 Azure 資料庫中的讀取複本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: ddcfea684a22c9ad06197086b3e74700df755da1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707986"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>如何使用 Azure CLI 和 REST API 來建立和管理適用於 MariaDB 的 Azure 資料庫中的讀取複本

在本文中，您將瞭解如何使用 Azure CLI 和 REST API 來建立和管理適用於 MariaDB 的 Azure 資料庫服務中的讀取複本。

## <a name="azure-cli"></a>Azure CLI
您可以使用 Azure CLI 建立及管理讀取複本。

### <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 將做為主伺服器使用的[適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)。 

> [!IMPORTANT]
> 「讀取複本」功能僅適用于一般用途或記憶體優化定價層中的適用於 MariaDB 的 Azure 資料庫伺服器。 請確定主要伺服器處於這些定價層中。

### <a name="create-a-read-replica"></a>建立讀取複本

> [!IMPORTANT]
> 當您為沒有任何現有複本的主要伺服器建立複本時，主要伺服器首先將會重新啟動，以準備本身進行複寫。 請考慮這一點，並在離峰期間執行這些作業。

使用下列命令可以建立讀取複本伺服器︰

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica create` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| NAME | mydemoreplicaserver | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫的現有主要伺服器的名稱或識別碼。 |

若要建立跨區域讀取複本，請使用 `--location` 參數。 

下列 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。 

> [!NOTE]
> 系統會以與主要伺服器相同的伺服器設定建立讀取複本。 複本伺服器設定在建立後可以變更。 建議複本伺服器設定的值應保持等於或大於主要伺服器，以確保複本伺服器能保持與主要伺服器一致。

### <a name="list-replicas-for-a-master-server"></a>列出主要伺服器的複本

若要檢視指定主要伺服器的所有複本，請執行下列命令： 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica list` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| server-name | mydemoserver | 主要伺服器的名稱或識別碼。 |

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦停止主要與複本伺服器之間進行複寫，便無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

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

若要刪除讀取複本伺服器，可以執行**[az 適用于 mariadb server delete](/cli/azure/mariadb/server)** 命令。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>刪除主要複本

> [!IMPORTANT]
> 刪除主要伺服器會停止對所有複本伺服器複寫，並刪除主要伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要刪除主伺服器，您可以執行**[az 適用于 mariadb server delete](/cli/azure/mariadb/server)** 命令。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
您可以使用[Azure REST API](/rest/api/azure/)來建立及管理讀取複本。

### <a name="create-a-read-replica"></a>建立讀取複本
您可以使用[建立 API](/rest/api/mariadb/servers/create)來建立讀取複本：

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

如果您尚未在 `azure.replication_support` 一般用途或記憶體優化的主伺服器上，將參數設定為 [**複本**]，並重新啟動伺服器，就會收到錯誤。 建立複本之前，請先完成這兩個步驟。

使用與主伺服器相同的計算和儲存設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。


> [!IMPORTANT]
> 將主伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

### <a name="list-replicas"></a>列出複本
您可以使用[複本清單 API](/rest/api/mariadb/replicas/listbyserver)來查看主伺服器的複本清單：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用[更新 API](/rest/api/mariadb/servers/update)來停止主伺服器與讀取複本之間的複寫。

停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

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

### <a name="delete-a-master-or-replica-server"></a>刪除主要或複本伺服器
若要刪除主要或複本伺服器，您可以使用[刪除 API](/rest/api/mariadb/servers/delete)：

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)
