---
title: 自動成長儲存體-Azure PowerShell-適用於 PostgreSQL 的 Azure 資料庫
description: 本文說明如何在適用於 PostgreSQL 的 Azure 資料庫中使用 PowerShell 來啟用自動成長儲存體。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a186c6e176cde20474cdf4772b8724ad3f356a3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87493613"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>使用 PowerShell 在適用於 PostgreSQL 的 Azure 資料庫伺服器中自動成長儲存體

本文說明如何設定適用於 PostgreSQL 的 Azure 資料庫 server 儲存體在不影響工作負載的情況下成長。

儲存體自動成長可防止您 [的伺服器達到儲存體限制](/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit) ，且變成隻讀。 針對具有 100 GB 或更少布建儲存體的伺服器，當可用空間低於10% 時，大小會增加 5 GB。 如果伺服器具有超過 100 GB 的已布建儲存體，則當可用空間低於 10 GB 時，大小會增加5%。 儲存體限制上限適用于 [適用於 PostgreSQL 的 Azure 資料庫定價層](/azure/postgresql/concepts-pricing-tiers#storage)的 [儲存體] 區段中所指定。

> [!IMPORTANT]
> 請記住，儲存體只能相應增加，不能相應減少。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明指南，您需要：

- [Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)安裝在本機或[Azure Cloud Shell](https://shell.azure.com/)于瀏覽器中
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 雖然 Az.PostgreSql PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.PostgreSql -AllowPrerelease`。
> 在 Az.PostgreSql PowerShell 模組正式推出後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

如果您選擇在本機使用 PowerShell，請使用 [disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) Cmdlet 連接到您的 Azure 帳戶。

## <a name="enable-postgresql-server-storage-auto-grow"></a>啟用于 postgresql 伺服器儲存體自動成長

使用下列命令，在現有伺服器上啟用伺服器自動成長儲存體：

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

使用下列命令來建立新的伺服器時，啟用伺服器自動成長儲存體：

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [如何使用 PowerShell 在適用於 PostgreSQL 的 Azure 資料庫中建立及管理讀取複本](howto-read-replicas-powershell.md)。
