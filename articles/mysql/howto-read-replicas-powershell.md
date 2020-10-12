---
title: 管理讀取複本-Azure PowerShell-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用 PowerShell 在適用於 MySQL 的 Azure 資料庫中設定和管理讀取複本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/24/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e9c8ce7519c6e2c84ef47fc78897c4b67b89e56a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540994"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>如何使用 PowerShell 在適用於 MySQL 的 Azure 資料庫中建立及管理讀取複本

在本文中，您將瞭解如何使用 PowerShell 在適用於 MySQL 的 Azure 資料庫服務中建立及管理讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

## <a name="azure-powershell"></a>Azure PowerShell

您可以使用 PowerShell 來建立及管理讀取複本。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

- [Az PowerShell 模組](/powershell/azure/install-az-ps)安裝在本機或[Azure Cloud Shell](https://shell.azure.com/)于瀏覽器中
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 雖然 Az.MySql PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.MySql -AllowPrerelease`。
> 在 Az.MySql PowerShell 模組正式推出後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

如果您選擇在本機使用 PowerShell，請使用 [disconnect-azaccount](/powershell/module/az.accounts/Connect-AzAccount) Cmdlet 連接到您的 Azure 帳戶。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 讀取複本功能僅供「適用於 MySQL 的 Azure 資料庫」伺服器用於一般用途或記憶體最佳化定價層。 確定來源伺服器是在其中一個定價層。

### <a name="create-a-read-replica"></a>建立讀取複本

> [!IMPORTANT]
> 當您為沒有現有複本的來源建立複本時，來源會先重新開機以準備複寫。 請考慮這一點，並在離峰期間執行這些作業。

使用下列命令可以建立讀取複本伺服器︰

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlReplica` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resourceGroupName |  myresourcegroup |  建立複本伺服器的資源群組。  |
| 名稱 | mydemoreplicaserver | 所建立的新複本伺服器名稱。 |

若要建立跨區域讀取複本，請使用 **Location** 參數。 下列範例會在 **美國西部** 區域建立複本。

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

若要深入瞭解您可以在哪些區域中建立複本，請造訪[參閱複本概念文章](concepts-read-replicas.md)。

依預設，除非指定了 **Sku** 參數，否則會使用與來源相同的伺服器設定來建立讀取複本。

> [!NOTE]
> 建議將複本伺服器的設定保留為等於或大於來源的值，以確保複本能夠跟上主伺服器。

### <a name="list-replicas-for-a-source-server"></a>列出來源伺服器的複本

若要查看指定來源伺服器的所有複本，請執行下列命令：

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resourceGroupName |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| ServerName | mydemoserver | 來源伺服器的名稱或識別碼。 |

### <a name="delete-a-replica-server"></a>刪除複本伺服器

您可以藉由執行 Cmdlet 來刪除讀取複本伺服器 `Remove-AzMySqlServer` 。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>刪除來源伺服器

> [!IMPORTANT]
> 刪除來源伺服器會停止對所有複本伺服器複寫，並刪除來源伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要刪除來源伺服器，您可以執行此 `Remove-AzMySqlServer` Cmdlet。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 PowerShell 重新開機適用於 MySQL 的 Azure 資料庫伺服器](howto-restart-server-powershell.md)
