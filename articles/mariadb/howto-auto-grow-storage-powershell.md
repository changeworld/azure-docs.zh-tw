---
title: 自動成長儲存體-Azure PowerShell-適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何在適用於 MariaDB 的 Azure 資料庫中使用 PowerShell 來啟用自動成長儲存體。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d03a67fc8a8172573598662ad9770b28493e9a2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497098"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>使用 PowerShell 在適用於 MariaDB 的 Azure 資料庫伺服器中自動增加儲存體

本文說明如何在不影響工作負載的情況下，將適用於 MariaDB 的 Azure 資料庫伺服器存放裝置設定為可成長。

存放裝置自動成長可防止您[的伺服器達到儲存空間限制](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)，而變成隻讀。 針對具有 100 GB 或更少布建儲存體的伺服器，當可用空間低於10% 時，大小會增加 5 GB。 針對具有超過 100 GB 已布建儲存體的伺服器，當可用空間低於 10 GB 時，大小會增加5%。 最大儲存體限制適用于[適用於 MariaDB 的 Azure 資料庫定價層](/azure/mariadb/concepts-pricing-tiers#storage)的 [儲存體] 區段中所指定。

> [!IMPORTANT]
> 請記住，儲存體只能相應增加，而不能相應縮小。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明指南，您需要：

- [Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)已安裝在本機或[Azure Cloud Shell](https://shell.azure.com/)在瀏覽器中
- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 雖然 Az.MariaDb PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.MariaDb -AllowPrerelease`。
> 在 Az.MariaDb PowerShell 模組正式推出後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

如果您選擇在本機使用 PowerShell，請使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) Cmdlet 連接到您的 Azure 帳戶。

## <a name="enable-mariadb-server-storage-auto-grow"></a>啟用適用于 mariadb 伺服器存放裝置自動成長

使用下列命令，在現有的伺服器上啟用伺服器自動成長儲存體：

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

使用下列命令建立新的伺服器時，啟用伺服器自動成長儲存體：

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何使用 PowerShell 建立和管理適用於 MariaDB 的 Azure 資料庫中的讀取複本](howto-read-replicas-powershell.md)。
