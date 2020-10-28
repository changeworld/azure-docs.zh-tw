---
title: 適用于 Azure SQL 受控執行個體的管理 API 參考
description: 瞭解如何建立及設定 Azure SQL 受控執行個體的受控實例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: cf100861705bf1654b7206445c884b5fe315b06d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792628"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Azure SQL 受控執行個體的受控 API 參考
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

您可以使用 Azure 入口網站、PowerShell、Azure CLI、REST API 和 Transact-sql，來建立及設定 Azure SQL 受控執行個體的受控實例。 在本文中，您可以找到可用於建立及設定受控實例的函式和 API 的總覽。

## <a name="azure-portal-create-a-managed-instance"></a>Azure 入口網站：建立受控實例

如需顯示如何建立受控實例的快速入門，請參閱 [快速入門：建立受控實例](instance-create-quickstart.md)。

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell：建立及設定受控實例

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az.Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRM 模組中命令的引數本質上相同。

若要使用 Azure PowerShell 建立和管理受控執行個體，請使用下列 PowerShell 指令程式。 如果您需要安裝或升級 PowerShell，請參閱 [安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

> [!TIP]
> 如需 PowerShell 範例腳本，請參閱 [快速入門腳本：使用 PowerShell 程式庫建立受控實例](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell)。

| Cmdlet | 描述 |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|建立受控執行個體。 |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|傳回受控實例的相關資訊。|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|設定受控實例的屬性。|
|[移除-New-azsqlinstance](/powershell/module/az.sql/remove-azsqlinstance)|移除受控實例。|
|[AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|取得在受控實例或特定作業上執行的管理作業清單。|
|[停止-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|取消在受控實例上執行的特定管理操作。|
|[新 AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|建立 SQL 受控執行個體資料庫。|
|[AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|傳回 SQL 受控執行個體資料庫的相關資訊。|
|[移除-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|移除 SQL 受控執行個體資料庫。|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|還原 SQL 受控執行個體資料庫。|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI：建立及設定受控實例

若要使用 [Azure CLI](/cli/azure)建立及設定受控實例，請使用下列 [Azure CLI SQL 受控執行個體命令](/cli/azure/sql/mi)。 使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 在您的瀏覽器中執行 CLI，或將它 [安裝](/cli/azure/install-azure-cli) 在 MacOS、Linux 或 Windows 上。

> [!TIP]
> 如需 Azure CLI 快速入門，請參閱[使用 Azure CLI 處理 SQL 受控執行個體](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)。

| Cmdlet | 描述 |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az-sql-mi-create) |建立受控執行個體。|
|[az sql mi list](/cli/azure/sql/mi#az-sql-mi-list)|列出可用的受控實例。|
|[az sql mi show](/cli/azure/sql/mi#az-sql-mi-show)|取得受控實例的詳細資料。|
|[az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)|更新受控實例。|
|[az sql mi delete](/cli/azure/sql/mi#az-sql-mi-delete)|移除受控實例。|
|[az sql mi op 清單](/cli/azure/sql/mi/op#az_sql_mi_op_list)|取得在受控實例上執行的管理作業清單。|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|取得在受控實例上執行的特定管理作業。|
|[az sql mi op cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|取消在受控實例上執行的特定管理操作。|
|[az sql midb create](/cli/azure/sql/midb#az-sql-midb-create) |建立受控資料庫。|
|[az sql midb list](/cli/azure/sql/midb#az-sql-midb-list)|列出可用的受控資料庫。|
|[az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)|還原受控資料庫。|
|[az sql midb delete](/cli/azure/sql/midb#az-sql-midb-delete)|移除受管理的資料庫。|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-sql：建立和設定實例資料庫

若要在建立受控實例之後建立並設定實例資料庫，請使用下列 T-sql 命令。 您可以使用 Azure 入口網站、 [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、 [Azure Data Studio](/sql/azure-data-studio/what-is)、 [Visual Studio Code](https://code.visualstudio.com/docs)，或任何其他可連接至伺服器並傳遞 transact-sql 命令的程式來發出這些命令。

> [!TIP]
> 如需示範如何使用 Microsoft Windows 上的 SQL Server Management Studio 來設定及連線到受控實例的快速入門，請參閱 [快速入門：設定 AZURE VM 以連接到 AZURE sql 受控執行個體](connect-vm-instance-configure.md) 和 [快速入門：設定從內部部署至 azure sql 受控執行個體的點對站](point-to-site-p2s-configure.md)連線。

> [!IMPORTANT]
> 您無法使用 Transact-sql 建立或刪除受控實例。

| Command | 描述 |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|在 SQL 受控執行個體中建立新的實例資料庫。 您必須連接到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |修改 SQL 受控執行個體中的實例資料庫。|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API：建立及設定受控實例

若要建立及設定受控實例，請使用這些 REST API 要求。

| Command | 描述 |
| --- | --- |
|[Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)|建立或更新受控實例。|
|[Managed Instances - Delete](/rest/api/sql/managedinstances/delete)|刪除受控實例。|
|[Managed Instances - Get](/rest/api/sql/managedinstances/get)|取得受控實例。|
|[Managed Instances - List](/rest/api/sql/managedinstances/list)|傳回訂用帳戶中的受控實例清單。|
|[Managed Instances - List By Resource Group](/rest/api/sql/managedinstances/listbyresourcegroup)|傳回資源群組中的受控實例清單。|
|[Managed Instances - Update](/rest/api/sql/managedinstances/update)|更新受控實例。|
|[受控執行個體作業-依受控執行個體列出](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|取得在受控實例上執行的管理作業清單。|
|[受控執行個體作業-取得](/rest/api/sql/managedinstanceoperations/get)|取得在受控實例上執行的特定管理作業。|
|[受控執行個體作業-取消](/rest/api/sql/managedinstanceoperations/cancel)|取消在受控實例上執行的特定管理操作。|

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](../database/migrate-to-database-from-sql-server.md)。
- 如需支援功能的詳細資訊，請參閱 [功能](../database/features-comparison.md)。