---
title: 什麼是 Azure SQL Database 和 Azure Synapse Analytics 中的伺服器？
titleSuffix: ''
description: 瞭解 Azure SQL Database 和 Azure Synapse 分析所使用的邏輯 SQL server，以及如何管理它們。
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
ms.openlocfilehash: dbcc82d3ec4b50cf51210f8a4319bbf374185a88
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498084"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Azure SQL Database 和 Azure Synapse 中的邏輯 SQL server 是什麼？
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

在 Azure SQL Database 和 Azure Synapse 分析中，伺服器是一種邏輯結構，可作為資料庫集合的中央管理點。 在伺服器層級，您[可以管理登](logins-create-manage.md)入、[防火牆規則](firewall-configure.md)、[審核規則](../../azure-sql/database/auditing-overview.md)、[威脅偵測原則](threat-detection-configure.md)，以及[自動容錯移轉群組](auto-failover-group-overview.md)。 伺服器可以位於與其資源群組不同的區域中。 伺服器必須先存在，您才能在 Azure Synapse 分析的 Azure SQL Database 或資料倉儲資料庫中建立資料庫。 單一伺服器所管理的所有資料庫都會建立在與伺服器相同的區域內。

此伺服器與您在內部部署環境中可能熟悉的 SQL Server 實例不同。 具體而言，與管理資料庫或資料倉儲資料庫的伺服器無關，並不保證其相關位置。 此外，Azure SQL Database 和 Azure Synapse 都不會公開任何實例層級的存取或功能。 相較之下，受控實例中的實例資料庫全都是以您熟悉內部部署或虛擬機器世界中 SQL Server 的相同方式，在實體上共置。

當您建立伺服器時，您會提供伺服器登入帳戶和密碼，其具有該伺服器上 master 資料庫的系統管理許可權，以及在該伺服器上建立的所有資料庫。 這個初始帳戶是 SQL 登入帳戶。 Azure SQL Database 和 Synapse 分析支援 SQL 驗證和 Azure Active Directory 驗證以進行驗證。 如需登入和驗證的相關資訊，請參閱[管理 Azure SQL Database 的資料庫和登入](logins-create-manage.md)。 不支援 Windows 驗證。

SQL Database 和 Azure Synapse 中的伺服器：

- 建立在 Azure 訂用帳戶內，但可以使用其所包含的資源移至另一個訂用帳戶
- 是資料庫的父資源、彈性集區和資料倉儲
- 提供資料庫、彈性集區和資料倉儲資料庫的命名空間
- 是具有強式存留期語義的邏輯容器-刪除伺服器，並刪除其資料庫、彈性集區和 SQK 集區
- 參與[Azure 角色型存取控制（AZURE RBAC）](/azure/role-based-access-control/overview) -伺服器內的資料庫、彈性集區和資料倉儲資料庫會繼承伺服器的存取權限
- 是資料庫、彈性集區和資料倉儲資料庫的身分識別的高階元素，用於 Azure 資源管理的目的（請參閱資料庫和集區的 URL 配置）
- 區域中的共置資源
- 提供用來存取資料庫的連接端點 (`<serverName>`.database.windows.net)
- 藉由連接到 master 資料庫提供存取有關透過 DMV 內含資源的中繼資料
- 提供適用於其資料庫的管理原則範圍 - 登入、防火牆、稽核、威脅偵測等等
- 受父訂用帳戶內的配額限制 (每個訂用帳戶預設六部伺服器 - [在此參閱訂用帳戶限制](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- 提供其包含之資源的資料庫配額和 DTU 或虛擬核心配額範圍 (例如 45,000 DTU)
- 內含資源上啟用功能的版本控制範圍
- 伺服器層級主體登入可以管理伺服器上的所有資料庫
- 可以包含類似于內部部署環境中 SQL Server 實例中的登入，這些使用者會被授與伺服器上一個或多個資料庫的存取權，而且可以授與有限的系統管理許可權。 如需詳細資訊，請參閱[登入](logins-create-manage.md)。
- 在伺服器上建立之所有資料庫的預設定序是 `SQL_LATIN1_GENERAL_CP1_CI_AS` ，其中 `LATIN1_GENERAL` 是英文（美國）、 `CP1` 字碼頁1252、 `CI` 不區分大小寫，且區分 `AS` 重音。

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>使用 Azure 入口網站管理伺服器、資料庫和防火牆

您可以在一段時間之前或建立伺服器本身時，建立伺服器的資源群組。 有多種方法可以使用新的 SQL Server 表單，可以透過建立新的 SQL Server，或是在建立新資料庫時一起使用。

### <a name="create-a-blank-server"></a>建立空白伺服器

若要使用[Azure 入口網站](https://portal.azure.com)來建立伺服器（不含資料庫、彈性集區或資料倉儲資料庫），請流覽至空白的 SQL server （邏輯 sql server）表單。

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>在 Azure SQL Database 中建立空白或範例資料庫

若要使用[Azure 入口網站](https://portal.azure.com)在 SQL Database 中建立資料庫，請流覽至空白的 SQL Database 表單，並提供所要求的資訊。 您可以提早建立資源群組和伺服器，或在建立資料庫本身時建立。 您可以建立空白資料庫，或建立根據 Adventure Works LT 的範例資料庫。

  ![建立資料庫-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> 如需選取資料庫定價層的資訊，請參閱[以 DTU 為基礎的購買模型](service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](service-tiers-vcore.md)。

若要建立受控實例，請參閱[建立受控實例](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>管理現有的伺服器

若要管理現有的伺服器，請使用多種方法（例如從特定資料庫頁面、[ **SQL server** ] 頁面或 [**所有資源**] 頁面），流覽至伺服器。

若要管理現有的資料庫，請瀏覽至 [SQL 資料庫]**** 頁面，按一下您想要管理的資料庫。 下列螢幕擷取畫面顯示如何從資料庫的 [概觀]**** 頁面，開始設定資料庫的伺服器層級防火牆。

   ![伺服器防火牆規則](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> 若要設定資料庫的效能屬性，請參閱[以 DTU 為基礎的購買模型](service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](service-tiers-vcore.md)。
> [!TIP]
> 如需 Azure 入口網站快速入門，請參閱[Azure 入口網站中的 SQL Database 建立資料庫](single-database-create-quickstart.md)。

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>使用 PowerShell 管理伺服器、資料庫和防火牆

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 模組仍受到支援，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

若要使用 Azure PowerShell 來建立和管理伺服器、資料庫和防火牆，請使用下列 PowerShell Cmdlet。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如需建立和管理彈性集區，請參閱[彈性集區](elastic-pool-overview.md)。

| Cmdlet | 描述 |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|建立資料庫 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|取得一或多個資料庫|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|設定資料庫的屬性，或將現有資料庫移到彈性集區中|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|移除資料庫|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|建立資源群組|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|建立伺服器|
|[AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|傳回伺服器的相關資訊|
|[設定-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|修改伺服器的屬性|
|[移除-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|移除伺服器|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|建立伺服器層級防火牆規則 |
|[New-azsqlserverfirewallrule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|取得伺服器的防火牆規則|
|[設定-New-azsqlserverfirewallrule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|修改伺服器中的防火牆規則|
|[移除-New-azsqlserverfirewallrule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|刪除伺服器的防火牆規則|
| 新增-AzSqlServerVirtualNetworkRule | 根據同時是虛擬網路服務端點的子網路，建立[*虛擬網路規則*](vnet-service-endpoint-rule-overview.md)。 |

> [!TIP]
> 如需 PowerShell 快速入門，請參閱[使用 PowerShell 在 Azure SQL Database 中建立資料庫](single-database-create-quickstart.md)。 如需 PowerShell 範例腳本，請參閱[使用 powershell 在 Azure SQL Database 中建立資料庫和設定防火牆規則](scripts/create-and-configure-database-powershell.md)和[使用 powershell 在 Azure SQL Database 中監視和調整資料庫](scripts/monitor-and-scale-database-powershell.md)。
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>使用 Azure CLI 管理伺服器、資料庫和防火牆

若要使用[Azure CLI](/cli/azure)來建立和管理伺服器、資料庫和防火牆，請使用下列[Azure CLI SQL Database](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。 如需建立和管理彈性集區，請參閱[彈性集區](elastic-pool-overview.md)。

| Cmdlet | 描述 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |建立資料庫|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|列出由伺服器管理的所有資料庫，或彈性集區中的所有資料庫|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|列出可用的服務目標與儲存體限制|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|傳回資料庫使用方式|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|取得資料庫
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

> [!TIP]
> 如需 Azure CLI 快速入門，請參閱[使用 Azure CLI 在 Azure SQL Database 中建立資料庫](az-cli-script-samples-content-guide.md)。 如 Azure CLI 範例腳本，請參閱[使用 cli 在 Azure SQL Database 中建立資料庫和設定防火牆規則](scripts/create-and-configure-database-cli.md)和[使用 cli 來監視和調整 Azure SQL Database 中的資料庫](scripts/monitor-and-scale-database-cli.md)。
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>使用 Transact-sql 管理伺服器、資料庫和防火牆

若要使用 Transact-sql 來建立和管理伺服器、資料庫和防火牆，請使用下列 T-sql 命令。 您可以使用 Azure 入口網站、 [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、 [Visual Studio Code](https://code.visualstudio.com/docs)，或可以連接到伺服器並傳遞 transact-sql 命令的任何其他程式來發出這些命令。 如需管理彈性集區，請參閱[彈性集區](elastic-pool-overview.md)。

> [!IMPORTANT]
> 您無法使用 Transact-SQL 建立或刪除伺服器。

| Command | 說明 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) | 在 Azure SQL Database 中建立新的資料庫。 您必須連接到 master 資料庫才能建立新的資料庫。|
|[建立資料庫（Azure Synapse）](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest) | 在 Azure Synapse 中建立新的資料倉儲資料庫。 您必須連接到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |修改資料庫或彈性集區。 |
|[ALTER DATABASE (Azure SQL 資料倉儲)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=sql-server-ver15)|修改 Azure Synapse 中的資料倉儲資料庫。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|刪除資料庫。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|傳回資料庫的版本（服務層級）、服務目標（定價層）和彈性集區名稱（如果有的話）。 如果登入伺服器的 master 資料庫，會傳回所有資料庫的相關資訊。 針對 Azure Synapse，您必須連接到 master 資料庫。|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 傳回 Azure SQL Database 中資料庫的 CPU、IO 和記憶體耗用量。 每 15 秒有一個資料列存在，即使資料庫中沒有任何活動亦然。|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|傳回 Azure SQL Database 中資料庫的 CPU 使用量和儲存體資料。 於五分鐘間隔內收集及彙總資料。|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|包含 Azure SQL Database 的資料庫連接事件統計資料，提供資料庫連接成功和失敗的總覽。 |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|傳回成功的 Azure SQL Database 資料庫連接、連接失敗，以及 Azure SQL Database 的鎖死。 您可以使用此資訊來追蹤或疑難排解您的資料庫活動。|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|建立或補救伺服器的伺服器層級防火牆設定。 這個預存程序只可在 master 資料庫中對伺服器層級主體登入提供。 具有 Azure 層級權限的使用者建立第一個伺服器層級防火牆規則之後，才能使用 Transact-SQL 建立伺服器層級防火牆規則|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|傳回與伺服器關聯之伺服器層級防火牆設定的相關資訊。|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|從伺服器移除伺服器層級防火牆設定。 這個預存程序只可在 master 資料庫中對伺服器層級主體登入提供。|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|在 Azure SQL Database 中，建立或更新資料庫的資料庫層級防火牆規則。 您可以針對 master 資料庫和 SQL Database 中的使用者資料庫設定資料庫防火牆規則。 使用自主資料庫使用者時，資料庫防火牆規則很有用。 Azure Synapse 不支援資料庫防火牆規則。|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|傳回 Azure SQL Database 中資料庫層級防火牆設定的相關資訊。 |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|在 Azure SQL Database 中，移除您的資料庫的資料庫層級防火牆設定。 |

> [!TIP]
> 如需在 Microsoft Windows 上使用 SQL Server Management Studio 的快速入門，請參閱 [Azure SQL Database：使用 SQL Server Management Studio 連線及查詢資料](connect-query-ssms.md)。 如需在 macOS、Linux 或 Windows 上使用 Visual Studio Code 的快速入門，請參閱 [Azure SQL Database：使用 Visual Studio Code 連線及查詢資料](connect-query-vscode.md)。

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>使用 REST API 管理伺服器、資料庫和防火牆

若要建立和管理伺服器、資料庫和防火牆，請使用這些 REST API 要求。

| Command | 說明 |
| --- | --- |
|[伺服器-建立或更新](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|建立或更新新的伺服器。|
|[伺服器 - 刪除](https://docs.microsoft.com/rest/api/sql/servers/delete)|刪除伺服器。|
|[伺服器 - 取得](https://docs.microsoft.com/rest/api/sql/servers/get)|取得伺服器。|
|[伺服器 - 清單](https://docs.microsoft.com/rest/api/sql/servers/list)|傳回伺服器的清單。|
|[伺服器-依資源群組列出](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|傳回資源群組中的伺服器清單。|
|[伺服器 - 更新](https://docs.microsoft.com/rest/api/sql/servers/update)|更新現有伺服器。|
|[資料庫-建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|建立新的資料庫或更新現有資料庫。|
|[資料庫 - 刪除](https://docs.microsoft.com/rest/api/sql/databases/delete)|刪除資料庫。|
|[資料庫 - 取得](https://docs.microsoft.com/rest/api/sql/databases/get)|取得資料庫。|
|[資料庫-依彈性集區列出](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|傳回將彈性集區中的資料庫列出的清單。|
|[資料庫-依伺服器列出](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|傳回伺服器中的資料庫清單。|
|[資料庫 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)|更新現有的資料庫。|
|[防火牆規則-建立或更新](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|建立或更新防火牆規則。|
|[防火牆規則-刪除](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|刪除防火牆規則。|
|[防火牆規則-取得](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|取得防火牆規則。|
|[防火牆規則-依伺服器列出](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|傳回防火牆規則的清單。|

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何將 SQL Server 資料庫移轉至 Azure SQL Database，請參閱[遷移至 Azure SQL Database](migrate-to-database-from-sql-server.md)。
- 如需支援功能的相關資訊，請參閱[功能](features-comparison.md)。
