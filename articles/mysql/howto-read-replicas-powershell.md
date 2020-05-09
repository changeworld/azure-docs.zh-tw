---
title: 管理讀取複本-Azure PowerShell 適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用 PowerShell 來設定和管理適用於 MySQL 的 Azure 資料庫中的讀取複本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 9ac85299311c1fd233988c6472d6325934dd42dd
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614534"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>如何使用 PowerShell 建立和管理適用於 MySQL 的 Azure 資料庫中的讀取複本

在本文中，您將瞭解如何使用 PowerShell 來建立和管理適用於 MySQL 的 Azure 資料庫服務中的讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

## <a name="azure-powershell"></a>Azure PowerShell

您可以使用 PowerShell 來建立及管理讀取複本。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明指南，您需要：

- [Az PowerShell 模組](/powershell/azure/install-az-ps)已安裝在本機或[Azure Cloud Shell](https://shell.azure.com/)在瀏覽器中
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 當 Az MySql PowerShell 模組處於預覽狀態時，您必須使用下列命令，將它與 Az PowerShell 模組分開安裝： `Install-Module -Name Az.MySql -AllowPrerelease`。
> 在 Az MySql PowerShell 模組正式推出之後，它會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

如果您選擇在本機使用 PowerShell，請使用[disconnect-azaccount](/powershell/module/az.accounts/Connect-AzAccount) Cmdlet 連接到您的 Azure 帳戶。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 讀取複本功能僅供「適用於 MySQL 的 Azure 資料庫」伺服器用於一般用途或記憶體最佳化定價層。 請確定主要伺服器處於這些定價層中。

### <a name="create-a-read-replica"></a>建立讀取複本

使用下列命令可以建立讀取複本伺服器︰

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlServerReplica` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resourceGroupName |  myresourcegroup |  建立複本伺服器所在的資源群組。  |
| 名稱 | mydemoreplicaserver | 所建立的新複本伺服器名稱。 |

若要建立跨區域讀取複本，請使用**Location**參數。 下列範例會在**美國西部**區域建立複本。

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

若要深入瞭解您可以在哪些區域中建立複本，請造訪[讀取複本概念一文](concepts-read-replicas.md)。

根據預設，除非指定**Sku**參數，否則會使用與主伺服器相同的伺服器設定來建立讀取複本。

> [!NOTE]
> 建議複本伺服器設定的值應保持等於或大於主要伺服器，以確保複本伺服器能保持與主要伺服器一致。

### <a name="list-replicas-for-a-master-server"></a>列出主要伺服器的複本

若要檢視指定主要伺服器的所有複本，請執行下列命令：

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resourceGroupName |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| ServerName | mydemoserver | 主要伺服器的名稱或識別碼。 |

### <a name="delete-a-replica-server"></a>刪除複本伺服器

您可以藉由執行`Remove-AzMySqlServer` Cmdlet 來刪除讀取複本伺服器。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>刪除主要複本

> [!IMPORTANT]
> 刪除主要伺服器會停止對所有複本伺服器複寫，並刪除主要伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要刪除主伺服器，您可以執行`Remove-AzMySqlServer` Cmdlet。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 PowerShell 重新開機適用於 MySQL 的 Azure 資料庫 server](howto-restart-server-powershell.md)
