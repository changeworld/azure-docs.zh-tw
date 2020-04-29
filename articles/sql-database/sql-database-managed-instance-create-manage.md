---
title: 受控實例的管理 API 參考
description: 深入了解建立及管理 Azure SQL Database 受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268856"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Azure SQL Database 受控執行個體的管理 API 參考

您可以使用 Azure 入口網站、PowerShell、Azure CLI、REST API 和 Transact-SQL，來建立及管理 Azure SQL Database 受控執行個體。 在本文中，您可以概略了解建立和設定受控執行個體的函式和 API。

## <a name="azure-portal-create-a-managed-instance"></a>Azure 入口網站：建立受控實例

如需示範如何建立 Azure SQL Database 受控執行個體的快速入門，請參閱[快速入門：建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell：建立及管理受控實例

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

若要使用 Azure PowerShell 建立和管理受控執行個體，請使用下列 PowerShell 指令程式。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

> [!TIP]
> 如需 PowerShell 範例腳本，請參閱[快速啟動腳本：使用 PowerShell 程式庫建立 AZURE SQL 受控執行個體](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)。

| Cmdlet | 說明 |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|建立 Azure SQL Database 受控執行個體 |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|傳回 Azure SQL Database 受控執行個體的資訊|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|設定 Azure SQL Database 受控執行個體的屬性|
|[移除-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|移除 Azure SQL Database 受控執行個體|
|[新增-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|建立 Azure SQL Database 受控執行個體資料庫|
|[AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|傳回 Azure SQL Database 受控執行個體的資料庫資訊|
|[移除-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|移除 Azure SQL Database 受控執行個體資料庫|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|還原 Azure SQL Database 受控執行個體資料庫|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI：建立和管理受控實例

若要使用 [Azure CLI](/cli/azure) 建立和管理受控執行個體，請使用下列 [Azure CLI SQL 受控執行個體](/cli/azure/sql/mi)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。

> [!TIP]
> 如需 Azure CLI 快速入門，請參閱[使用 Azure CLI 處理 SQL 受控執行個體](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)。

| Cmdlet | 說明 |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |建立受控執行個體|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|列出可用的受控執行個體|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|取得受控執行個體的詳細資料|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|更新受控執行個體|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|移除受控執行個體|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |建立受控資料庫|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|列出可用的受控資料庫|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|還原受控資料庫|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|移除受控資料庫|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-sql：建立和管理實例資料庫

若要在建立受控執行個體後建立與管理執行個體資料庫，請使用下列 T-SQL 命令。 您可以使用 Azure 入口網站、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 發出這些命令。 [Visual Studio Code](https://code.visualstudio.com/docs)，或可以連接到 Azure SQL Database 伺服器並傳遞 TRANSACT-SQL 命令的其他任何程式。

> [!TIP]
> 如需示範如何使用 Microsoft Windows 上的 SQL Server Management Studio 來設定及連線到受控執行個體的快速入門，請參閱[快速入門：設定 AZURE VM 以連線至 Azure SQL Database 受控執行個體](sql-database-managed-instance-configure-vm.md)和[快速入門：設定從內部部署到 Azure SQL Database 受控執行個體的點對站](sql-database-managed-instance-configure-p2s.md)連線。
> [!IMPORTANT]
> 您無法使用 Transact-SQL 建立或刪除受控執行個體。

| Command | 說明 |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|建立新的受控執行個體資料庫。 您必須連接到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |修改 Azure SQL Database 受控執行個體資料庫。|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API：建立和管理受控實例

若要建立和管理受控執行個體，請使用下列 REST API 要求。

| Command | 說明 |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|建立或更新受控執行個體。|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|刪除受控執行個體。|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|取得受控執行個體。|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|傳回訂用帳戶之中受控執行個體的清單。|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|傳回資源群組之中受控執行個體的清單。|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|更新受控執行個體。|

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 如需支援功能的相關資訊，請參閱[功能](sql-database-features.md)。
