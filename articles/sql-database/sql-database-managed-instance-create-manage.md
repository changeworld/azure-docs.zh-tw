---
title: 託管實例的管理 API 引用
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268856"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Azure SQL Database 受控執行個體的管理 API 參考

您可以使用 Azure 入口網站、PowerShell、Azure CLI、REST API 和 Transact-SQL，來建立及管理 Azure SQL Database 受控執行個體。 在本文中，您可以概略了解建立和設定受控執行個體的函式和 API。

## <a name="azure-portal-create-a-managed-instance"></a>Azure 門戶：創建託管實例

有關如何創建 Azure SQL 資料庫託管實例的快速入門，請參閱[快速入門：創建 Azure SQL 資料庫託管實例](sql-database-managed-instance-get-started.md)。

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell：創建和管理託管實例

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。

若要使用 Azure PowerShell 建立和管理受控執行個體，請使用下列 PowerShell 指令程式。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

> [!TIP]
> 有關 PowerShell 示例腳本，請參閱[快速入門腳本：使用 PowerShell 庫創建 Azure SQL 託管實例](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)。

| Cmdlet | 描述 |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|建立 Azure SQL Database 受控執行個體 |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|傳回 Azure SQL Database 受控執行個體的資訊|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|設定 Azure SQL Database 受控執行個體的屬性|
|[刪除-AzSql 實例](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|移除 Azure SQL Database 受控執行個體|
|[新實例資料庫](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|建立 Azure SQL Database 受控執行個體資料庫|
|[獲取-AzSql實例資料庫](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|傳回 Azure SQL Database 受控執行個體的資料庫資訊|
|[刪除-AzSql實例資料庫](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|移除 Azure SQL Database 受控執行個體資料庫|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|還原 Azure SQL Database 受控執行個體資料庫|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI：創建和管理託管實例

若要使用 [Azure CLI](/cli/azure) 建立和管理受控執行個體，請使用下列 [Azure CLI SQL 受控執行個體](/cli/azure/sql/mi)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。

> [!TIP]
> 如需 Azure CLI 快速入門，請參閱[使用 Azure CLI 處理 SQL 受控執行個體](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)。

| Cmdlet | 描述 |
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

## <a name="transact-sql-create-and-manage-instance-databases"></a>交易-SQL：創建和管理實例資料庫

若要在建立受控執行個體後建立與管理執行個體資料庫，請使用下列 T-SQL 命令。 您可以使用 Azure 入口網站、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 發出這些命令。 [Visual Studio Code](https://code.visualstudio.com/docs)，或可以連接到 Azure SQL Database 伺服器並傳遞 TRANSACT-SQL 命令的其他任何程式。

> [!TIP]
> 有關必須使用 Microsoft Windows 上的 SQL 伺服器管理工作室配置和連接到託管實例的快速入門，請參閱[快速入門：配置 Azure VM 以連接到 Azure SQL 資料庫託管實例](sql-database-managed-instance-configure-vm.md)和[快速入門：從本地配置到 Azure SQL 資料庫託管實例的點到網站連接](sql-database-managed-instance-configure-p2s.md)。
> [!IMPORTANT]
> 您無法使用 Transact-SQL 建立或刪除受控執行個體。

| Command | 描述 |
| --- | --- |
|[創建資料庫](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|建立新的受控執行個體資料庫。 您必須連接到 master 資料庫才能建立新的資料庫。|
| [更改資料庫](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |修改 Azure SQL Database 受控執行個體資料庫。|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API：創建和管理託管實例

若要建立和管理受控執行個體，請使用下列 REST API 要求。

| Command | 描述 |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|建立或更新受控執行個體。|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|刪除受控執行個體。|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|取得受控執行個體。|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|傳回訂用帳戶之中受控執行個體的清單。|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|傳回資源群組之中受控執行個體的清單。|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|更新受控執行個體。|

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 有關受支援功能的資訊，請參閱[功能](sql-database-features.md)。
