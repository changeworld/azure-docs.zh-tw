---
title: 創建、管理伺服器和單個資料庫
description: 了解如何建立及管理 SQL Database 伺服器和單一資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268544"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>在 Azure SQL Database 中建立及管理 SQL Database 伺服器和單一資料庫

您可以使用 Azure 入口網站、PowerShell、Azure CLI、REST API 和 Transact-SQL，來建立及管理 SQL Database 伺服器與單一資料庫。

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Azure 門戶：管理 SQL 資料庫伺服器和單個資料庫

您可以事先或在建立伺服器本身的同時，建立 Azure SQL 資料庫的資源群組。 有多種方法可以使用新的 SQL Server 表單，可以透過建立新的 SQL Server，或是在建立新資料庫時一起使用。

### <a name="create-a-blank-sql-database-server"></a>建立空白的 SQL Database 伺服器

若要使用 [Azure 入口網站](https://portal.azure.com)建立 SQL Database 伺服器，請瀏覽至空白的 SQL Server (邏輯伺服器) 表單。  

### <a name="create-a-blank-or-sample-sql-single-database"></a>建立空白或範例 SQL 單一資料庫

若要使用 [Azure 入口網站](https://portal.azure.com)建立 Azure SQL 單一資料庫，請瀏覽至空白的 SQL Database 表單，並提供要求的資訊。 您可以事先或在建立單一資料庫本身的同時，建立 Azure SQL 資料庫的資源群組和 SQL Database 伺服器。 您可以建立空白資料庫，或建立根據 Adventure Works LT 的範例資料庫。

  ![建立資料庫-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> 如需選取資料庫定價層的資訊，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。

若要建立「受控執行個體」，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>管理現有的 SQL Database 伺服器

若要管理現有的 SQL Database 伺服器，請使用多種方法 (例如從特定的 SQL 資料庫頁面、[SQL Server]**** 頁面，或 [所有資源]**** 頁面)，瀏覽至伺服器。

若要管理現有的資料庫，請瀏覽至 [SQL 資料庫]**** 頁面，按一下您想要管理的資料庫。 下列螢幕擷取畫面顯示如何從資料庫的 [概觀]**** 頁面，開始設定資料庫的伺服器層級防火牆。

   ![伺服器防火牆規則](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> 若要設定資料庫的效能屬性，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。
> [!TIP]
> 如需 Azure 入口網站快速入門，請參閱[在 Azure 入口網站中建立 Azure SQL 資料庫](sql-database-single-database-get-started.md)。

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell：管理 SQL 資料庫伺服器和單個資料庫

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。

若要使用 Azure PowerShell 建立及管理 Azure SQL Database 伺服器、單一和集區資料庫以及 SQL Database 伺服器防火牆，請使用下列 PowerShell Cmdlet。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

> [!TIP]
> 如需 PowerShell 範例指令碼，請參閱[使用 PowerShell 建立 Azure SQL 單一資料庫並設定 SQL Database 伺服器防火牆規則](scripts/sql-database-create-and-configure-database-powershell.md)和[使用 PowerShell 監視和調整 SQL 單一資料庫](scripts/sql-database-monitor-and-scale-database-powershell.md)。

| Cmdlet | 描述 |
| --- | --- |
|[新-AzSql 資料庫](/powershell/module/az.sql/new-azsqldatabase)|建立資料庫 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|取得一或多個資料庫|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|設定資料庫的屬性，或將現有資料庫移到彈性集區中|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|移除資料庫|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|建立資源群組|
|[新-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|建立伺服器|
|[獲取-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|傳回伺服器的相關資訊|
|[設置-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|修改伺服器的屬性|
|[刪除-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|移除伺服器|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|建立伺服器層級防火牆規則 |
|[獲取-AzSqlServer防火牆規則](/powershell/module/az.sql/get-azsqlserverfirewallrule)|取得伺服器的防火牆規則|
|[設置-AzSqlServer防火牆規則](/powershell/module/az.sql/set-azsqlserverfirewallrule)|修改伺服器中的防火牆規則|
|[刪除-AzSqlServer防火牆規則](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|刪除伺服器的防火牆規則|
| 新-AzSqlServer虛擬網路規則 | 根據同時是虛擬網路服務端點的子網路，建立[*虛擬網路規則*](sql-database-vnet-service-endpoint-rule-overview.md)。 |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI：管理 SQL 資料庫伺服器和單個資料庫

若要使用 [Azure CLI](/cli/azure) 建立和管理 Azure SQL 伺服器、資料庫和防火牆，請使用下列 [Azure CLI SQL Database](/cli/azure/sql/db) 命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。 如需建立和管理彈性集區，請參閱[彈性集區](sql-database-elastic-pool.md)。

> [!TIP]
> 如需 Azure CLI 快速入門，請參閱[使用 Azure CLI 建立 Azure SQL 單一資料庫](sql-database-cli-samples.md)。 如需 Azure CLI 範例指令碼，請參閱[使用 CLI 建立 Azure SQL 單一資料庫並設定 SQL Database 防火牆規則](scripts/sql-database-create-and-configure-database-cli.md)和[使用 CLI 監視和調整 Azure SQL 單一資料庫](scripts/sql-database-monitor-and-scale-database-cli.md)。
>

| Cmdlet | 描述 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |建立資料庫|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|列出伺服器中的所有資料庫和資料倉儲，或彈性集區中的所有資料庫|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|列出可用的服務目標與儲存體限制|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|傳回資料庫使用方式|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|取得資料庫或資料倉儲|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|更新資料庫|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|移除資料庫|
|[az group create](/cli/azure/group#az-group-create)|建立資源群組|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|建立伺服器|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|列出伺服器|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|傳回伺服器使用方式|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|取得伺服器|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|更新伺服器|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|刪除伺服器|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|建立伺服器防火牆規則|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|列出伺服器上的防火牆規則|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|顯示防火牆規則的詳細資料|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|更新防火牆規則|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|刪除防火牆規則|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>交易-SQL：管理 SQL 資料庫伺服器和單個資料庫

若要使用 Transact-SQL 建立和管理 Azure SQL 伺服器、資料庫和防火牆，請使用下列 T-SQL 命令。 您可以使用 Azure 入口網站、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)，或任何可連線到 Azure SQL Database 伺服器並傳遞 TRANSACT-SQL 命令的其他程式來發出這些命令。 如需管理彈性集區，請參閱[彈性集區](sql-database-elastic-pool.md)。

> [!TIP]
> 如需在 Microsoft Windows 上使用 SQL Server Management Studio 的快速入門，請參閱 [Azure SQL Database：使用 SQL Server Management Studio 連線及查詢資料](sql-database-connect-query-ssms.md)。 如需在 macOS、Linux 或 Windows 上使用 Visual Studio Code 的快速入門，請參閱 [Azure SQL Database：使用 Visual Studio Code 連線及查詢資料](sql-database-connect-query-vscode.md)。
> [!IMPORTANT]
> 您無法使用 Transact-SQL 建立或刪除伺服器。

| Command | 描述 |
| --- | --- |
|[創建資料庫](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|建立新的單一資料庫。 您必須連接到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |修改 Azure SQL 資料庫。 |
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|刪除資料庫。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|傳回 Azure SQL 資料庫或 Azure SQL 資料倉儲的版本 (服務層級)、服務目標 (定價層) 和彈性集區名稱 (如果有的話)。 若已登入 Azure SQL Database 伺服器中的 master 資料庫，則傳回所有資料庫的相關資訊。 對於 Azure SQL 資料倉儲，您必須連線到 master 資料庫。|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 傳回 Azure SQL Database 資料庫的 CPU、IO 和記憶體耗用量。 每 15 秒有一個資料列存在，即使資料庫中沒有任何活動亦然。|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|傳回 Azure SQL Database 的 CPU 使用量和儲存體資料。 於五分鐘間隔內收集及彙總資料。|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|包含 SQL Database 資料庫連線事件的統計資料，提供資料庫連接成功和失敗的概觀。 |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|傳回成功的 Azure SQL Database 資料庫連接、連接失敗和死結。 可以使用這些資訊，對 SQL Database 的資料庫活動進行追蹤或疑難排解。|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|建立或更新 SQL Database 伺服器的伺服器層級防火牆設定。 這個預存程序只可在 master 資料庫中對伺服器層級主體登入提供。 具有 Azure 層級權限的使用者建立第一個伺服器層級防火牆規則之後，才能使用 Transact-SQL 建立伺服器層級防火牆規則|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|傳回與您的 Microsoft Azure SQL Database 相關聯之伺服器層級防火牆設定的相關資訊。|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|移除 SQL Database 伺服器的伺服器層級防火牆設定。 這個預存程序只可在 master 資料庫中對伺服器層級主體登入提供。|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|建立或更新您的 Azure SQL Database 或 SQL 資料倉儲的資料庫層級防火牆規則。 您可以為 master 資料庫，以及 SQL Database 上的使用者資料庫，設定資料庫防火牆規則。 使用自主資料庫使用者時，資料庫防火牆規則很有用。 |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|傳回與您的 Microsoft Azure SQL Database 相關聯之資料庫層級防火牆設定的相關資訊。 |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|移除您的 Azure SQL Database 或 SQL 資料倉儲的資料庫層級防火牆設定。 |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>REST API：管理 SQL 資料庫伺服器和單個資料庫

若要建立和管理 Azure SQL 伺服器、資料庫和防火牆，請使用 REST API 要求。

| Command | 描述 |
| --- | --- |
|[伺服器 - 創建或更新](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|建立或更新新的伺服器。|
|[伺服器 - 刪除](https://docs.microsoft.com/rest/api/sql/servers/delete)|刪除 SQL 伺服器。|
|[伺服器 - 取得](https://docs.microsoft.com/rest/api/sql/servers/get)|取得伺服器。|
|[伺服器 - 清單](https://docs.microsoft.com/rest/api/sql/servers/list)|傳回訂用帳戶中的伺服器清單。|
|[伺服器 - 按資源組列出](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|傳回資源群組中的伺服器清單。|
|[伺服器 - 更新](https://docs.microsoft.com/rest/api/sql/servers/update)|更新現有伺服器。|
|[資料庫 - 創建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|建立新的資料庫或更新現有資料庫。|
|[資料庫 - 刪除](https://docs.microsoft.com/rest/api/sql/databases/delete)|刪除資料庫。|
|[資料庫 - 取得](https://docs.microsoft.com/rest/api/sql/databases/get)|取得資料庫。|
|[資料庫 - 按彈性池列出](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|傳回將彈性集區中的資料庫列出的清單。|
|[資料庫 - 按伺服器列出](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|傳回伺服器中的資料庫清單。|
|[資料庫 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)|更新現有的資料庫。|
|[防火牆規則 - 創建或更新](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|建立或更新防火牆規則。|
|[防火牆規則 - 刪除](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|刪除防火牆規則。|
|[防火牆規則 - 獲取](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|取得防火牆規則。|
|[防火牆規則 - 按伺服器列出](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|傳回防火牆規則的清單。|

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 有關受支援功能的資訊，請參閱[功能](sql-database-features.md)。
