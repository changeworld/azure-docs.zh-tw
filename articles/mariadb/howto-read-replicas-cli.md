---
title: 管理讀取副本 - Azure CLI，REST API - MariaDB 的 Azure 資料庫
description: 本文介紹如何使用 Azure CLI 和 REST API 在 MariaDB 的 Azure 資料庫中設置和管理讀取副本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abf80e98881b73bed53c5a939a79bc8b3a9de2db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530575"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>如何使用 Azure CLI 和 REST API 在 MariaDB 的 Azure 資料庫中創建和管理讀取副本

在本文中，您將學習如何使用 Azure CLI 和 REST API 在 MariaDB 服務的 Azure 資料庫中創建和管理讀取副本。

## <a name="azure-cli"></a>Azure CLI
可以使用 Azure CLI 創建和管理讀取副本。

### <a name="prerequisites"></a>Prerequisites

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 將用作主伺服器[的 MariaDB 伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)的 Azure 資料庫。 

> [!IMPORTANT]
> 讀取副本功能僅適用于"通用"或"記憶體優化定價層"中 MariaDB 伺服器的 Azure 資料庫。 請確定主要伺服器處於這些定價層中。

### <a name="create-a-read-replica"></a>建立讀取複本

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

要創建跨區域讀取副本，請使用 參數`--location`。 

> [!NOTE]
> 跨區域複製處於預覽狀態。

下面的 CLI 示例在美國西部棄置站台。

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 要詳細瞭解可以在哪些區域棄置站台，請訪問[讀取副本概念一文](concepts-read-replicas.md)。 

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

通過運行**[az mariadb 伺服器刪除](/cli/azure/mariadb/server)** 命令，可以刪除讀取副本伺服器。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>刪除主要複本

> [!IMPORTANT]
> 刪除主要伺服器會停止對所有複本伺服器複寫，並刪除主要伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

要刪除主伺服器，可以運行 az **[mariadb 伺服器刪除](/cli/azure/mariadb/server)** 命令。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
可以使用[Azure REST API](/rest/api/azure/)創建和管理讀取副本。

### <a name="create-a-read-replica"></a>建立讀取複本
可以使用[創建 API](/rest/api/mariadb/servers/create)創建讀取副本：

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
> 要詳細瞭解可以在哪些區域棄置站台，請訪問[讀取副本概念一文](concepts-read-replicas.md)。 

如果尚未將`azure.replication_support`參數設置為通用或記憶體優化主伺服器上**的 REPLICA**並重新啟動伺服器，則收到錯誤。 在棄置站台之前完成這兩個步驟。

副本使用與主副本相同的計算和存儲設置創建。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。


> [!IMPORTANT]
> 在將主伺服器設置更新為新值之前，將副本設置更新為相等或更大的值。 此操作可説明副本跟上對主副本所做的任何更改。

### <a name="list-replicas"></a>列出副本
您可以使用[副本清單 API](/rest/api/mariadb/replicas/listbyserver)查看主伺服器的副本清單：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用[更新 API](/rest/api/mariadb/servers/update)停止主伺服器和讀取副本之間的複製。

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

### <a name="delete-a-master-or-replica-server"></a>刪除主伺服器或副本伺服器
要刪除主伺服器或副本伺服器，請使用刪除[API](/rest/api/mariadb/servers/delete)：

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)
