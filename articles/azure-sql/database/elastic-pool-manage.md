---
title: 管理彈性集區
description: 使用 Azure 入口網站、PowerShell、Azure CLI、Transact-sql (T-sql) 和 Rest API 來建立和管理 Azure SQL Database 彈性集區。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 5916a687c4eff4c6c8890b14a8c204cbabc145ea
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792186"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>在 Azure SQL Database 中管理彈性集區
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

使用彈性集區，您可以判斷彈性集區處理其資料庫工作負載所需的資源數量，以及適用於每個集區資料庫的資源數量。

## <a name="azure-portal"></a>Azure 入口網站

所有集區設定都可以在一個位置找到：[設定集區]  刀鋒視窗。 若要取得此資訊，請在 Azure 入口網站中尋找彈性集區，然後從分頁頂端或從左側的 [資源] 功能表中，按一下 [ **設定集** 區]。

您可以從這裡進行下列任何變更並將變更全部儲存在一個批次中：

1. 變更集區的服務層級
2. 相應放大或縮小效能 (DTU 或虛擬核心) 和儲存體
3. 在集區中新增或移除資料庫
4. 為集區中的資料庫設定最小 (保證) 和最大效能限制
5. 檢閱成本摘要，以檢視因為您的新選擇所造成的任何帳單變更

![彈性集區組態刀鋒視窗](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az.Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

若要使用 Azure PowerShell 建立和管理 SQL Database 彈性集區和集區資料庫，請使用下列 PowerShell Cmdlet。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 若要建立及管理彈性集區的伺服器，請參閱 [建立和管理伺服器](logical-servers.md)。 若要建立及管理防火牆規則，請參閱[使用 PowerShell 建立及管理防火牆規則](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)。

> [!TIP]
> 如需 PowerShell 範例指令碼，請參閱[使用 PowerShell 建立彈性集區並在集區之間移動資料庫以及將其移出集區](scripts/move-database-between-elastic-pools-powershell.md)和[使用 PowerShell 在 Azure SQL Database 中監視和調整 SQL 彈性集區](scripts/monitor-and-scale-pool-powershell.md)。
>

| Cmdlet | 描述 |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|建立彈性集區。|
|[AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|取得彈性集區及其屬性值。|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|修改彈性集區的屬性。例如，使用 **StorageMB** 屬性可修改彈性集區的最大儲存體。|
|[移除-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|刪除彈性集區。|
|[AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|取得彈性集區上的作業狀態|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|在現有的集區建立新的資料庫，或建立新的資料庫做為單一資料庫。 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|取得一或多個資料庫。|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|設定資料庫的屬性，或將現有資料庫移入彈性集區、移出彈性集區，或在彈性集區之間移動。|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|移除資料庫。|

> [!TIP]
> 使用入口網站或一次只建立單一資料庫的 PowerShell Cmdlet 在彈性集區中建立許多資料庫可能需要花費一些時間。 若要自動建立成彈性集區，請參閱 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。

## <a name="azure-cli"></a>Azure CLI

若要使用 [Azure CLI](/cli/azure) 建立和管理 SQL Database 彈性集區，請使用下列 [Azure CLI SQL Database](/cli/azure/sql/db) 命令。 使用 [Cloud Shell](../../cloud-shell/overview.md) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。

> [!TIP]
> 如 Azure CLI 範例腳本，請參閱 [使用 CLI 在 sql 彈性集區中的 SQL Database 移動資料庫](scripts/move-database-between-elastic-pools-cli.md) ，並 [使用 Azure CLI 來調整 AZURE SQL DATABASE 中的 SQL 彈性集](scripts/scale-pool-cli.md)區。
>

| Cmdlet | 描述 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|建立彈性集區。|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|傳回將伺服器中的彈性集區列出的清單。|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|傳回將彈性集區中的資料庫列出的清單。|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|也包含可用的集區 DTU 設定、儲存體限制，以及個別資料庫設定。 為了減少繁複度，額外的儲存空間限制和個別資料庫設定預設為隱藏。|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|更新彈性集區。|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|刪除彈性集區。|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

若要在現有彈性集區中建立並移動資料庫，或傳回 SQL Database 彈性集區與 Transact-SQL 的資訊，請使用下列 T-SQL 命令。 您可以使用 Azure 入口網站、 [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、 [Visual Studio Code](https://code.visualstudio.com/docs)，或任何其他可連接至伺服器並傳遞 transact-sql 命令的程式來發出這些命令。 若要使用 T-SQL 建立及管理防火牆規則，請參閱[使用 Transact-SQL 管理防火牆規則](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)。

> [!IMPORTANT]
> 您無法使用 Transact-SQL 建立、更新或刪除 Azure SQL Database 彈性集區。 您可以新增或移除彈性集區中的資料庫，也可以使用 DMV 傳回現有彈性集區的資訊。
>

| Command | 描述 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|在現有的集區建立新的資料庫，或建立新的資料庫做為單一資料庫。 您必須連接到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |將資料庫移入彈性集區、將資料庫移出彈性集區，或在彈性集區之間移動資料庫。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|刪除資料庫。|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|傳回伺服器上所有彈性集區的資源使用量統計資料。 每個彈性集區，每 15 秒報告時間範圍會傳回一列 (每分鐘四列)。 包括集區中所有資料庫的 CPU、IO、記錄、儲存體使用情況和並行的要求/工作階段使用量。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|針對 SQL Database 或 Azure Synapse Analytics (先前的 SQL 資料倉儲) 中的資料庫，傳回 (服務層級) 、服務目標 (定價層) 和彈性集區名稱（如果有的話）。 如果登入伺服器中的 master 資料庫，則會傳回所有資料庫的資訊。 針對 Azure Synapse Analytics，您必須連接到 master 資料庫。|

## <a name="rest-api"></a>REST API

若要建立和管理 SQL Database 彈性集區和集區資料庫，請使用這些 REST API 要求。

| Command | 描述 |
| --- | --- |
|[彈性集區-建立或更新](/rest/api/sql/elasticpools/createorupdate)|建立新的彈性集區或更新現有的彈性集區。|
|[彈性集區-刪除](/rest/api/sql/elasticpools/delete)|刪除彈性集區。|
|[彈性集區-取得](/rest/api/sql/elasticpools/get)|取得彈性集區。|
|[彈性集區-依伺服器列出](/rest/api/sql/elasticpools/listbyserver)|傳回將伺服器中的彈性集區列出的清單。|
|[彈性集區-更新](/rest/api/sql/elasticpools/listbyserver)|更新現有的彈性集區。|
|[彈性集區活動](/rest/api/sql/elasticpoolactivities)|傳回彈性集區活動。|
|[彈性集區資料庫活動](/rest/api/sql/elasticpooldatabaseactivities)|傳回資料庫內彈性集區上的活動。|
|[資料庫-建立或更新](/rest/api/sql/databases/createorupdate)|建立新的資料庫或更新現有資料庫。|
|[資料庫 - 取得](/rest/api/sql/databases/get)|取得資料庫。|
|[資料庫-依彈性集區列出](/rest/api/sql/databases/listbyelasticpool)|傳回將彈性集區中的資料庫列出的清單。|
|[資料庫-依伺服器列出](/rest/api/sql/databases/listbyserver)|傳回伺服器中的資料庫清單。|
|[資料庫 - 更新](/rest/api/sql/databases/update)|更新現有的資料庫。|

## <a name="next-steps"></a>後續步驟

* 若要深入了解使用彈性集區的 SaaS 應用程式的設計模式，請參閱 [採用 Azure SQL Database 的多租用戶 SaaS 應用程式的設計模式](saas-tenancy-app-design-patterns.md)。
* 如需使用彈性集區的 SaaS 教學課程，請參閱 [Wingtip SaaS 應用程式簡介](saas-dbpertenant-wingtip-app-overview.md)。