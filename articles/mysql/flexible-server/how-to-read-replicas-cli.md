---
title: 使用 Azure CLI 管理適用於 MySQL 的 Azure 資料庫彈性伺服器中的讀取複本。
description: 瞭解如何使用 Azure CLI 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中設定和管理讀取複本。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: acbb0d5b643919d7fa1bf3966532ebd83129fc2a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795176"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>如何使用 Azure CLI 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中建立及管理讀取複本

> [!IMPORTANT]
> 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中讀取複本處於預覽階段。

在本文中，您將瞭解如何使用 Azure CLI，在適用於 MySQL 的 Azure 資料庫彈性的伺服器中建立及管理讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

> [!Note]
> 在啟用高可用性的伺服器上不支援複本。 

## <a name="azure-cli"></a>Azure CLI
您可以使用 Azure CLI 來建立及管理讀取複本。

### <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [適用於 MySQL 的 Azure 資料庫的彈性伺服器](quickstart-create-server-cli.md)，將作為來源伺服器使用。

### <a name="create-a-read-replica"></a>建立讀取複本

> [!IMPORTANT]
> 當您為沒有現有複本的來源建立複本時，來源會先重新開機以準備複寫。 請考慮這一點，並在離峰期間執行這些作業。

使用下列命令可以建立讀取複本伺服器︰

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> 使用與來源相同的伺服器設定來建立讀取複本。 複本伺服器設定在建立後可以變更。 複本伺服器一律會建立在相同的資源群組、與來源伺服器相同的位置和相同的訂用帳戶中。 如果您想要將複本伺服器建立到不同的資源群組或不同的訂閱，您可以在建立後[移動複本伺服器](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)。 建議將複本伺服器的設定保留為等於或大於來源的值，以確保複本能夠跟上來源。


### <a name="list-replicas-for-a-source-server"></a>列出來源伺服器的複本

若要查看指定來源伺服器的所有複本，請執行下列命令： 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦來源與複本之間的複寫停止，就無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

使用下列命令，可以停止複寫至讀取複本伺服器︰

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>刪除複本伺服器

執行 **[az mysql server delete](/cli/azure/mysql/server)** 命令，即可刪除讀取複本伺服器。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>刪除來源伺服器

> [!IMPORTANT]
> 刪除來源伺服器會停止對所有複本伺服器複寫，並刪除來源伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要刪除來源伺服器，您可以執行 **[az mysql 彈性-伺服器刪除](/cli/azure/mysql/flexible-server)** 命令。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)
