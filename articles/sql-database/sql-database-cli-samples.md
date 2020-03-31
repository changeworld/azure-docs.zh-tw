---
title: Azure CLI 指令碼範例
description: Azure CLI 指令碼範例可以建立和管理 Azure SQL Database 伺服器、彈性集區、資料庫和防火牆。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061708"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure SQL Database 的 Azure CLI 範例

您可以使用 <a href="/cli/azure">Azure CLI</a> 來設定 Azure SQL Database。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

# <a name="single-database--elastic-pools"></a>[單一資料庫與彈性集區](#tab/single-database)

| | |
|---|---|
|**建立單一資料庫和彈性集區**||
| [建立單一資料庫並設定防火牆規則](scripts/sql-database-create-and-configure-database-cli.md) | 建立 Azure SQL 資料庫並設定伺服器層級防火牆規則。 |
| [建立彈性集區並移動集區資料庫](scripts/sql-database-move-database-between-pools-cli.md) | 建立 SQL 彈性集區、移動集區 Azure SQL 資料庫，然後變更計算大小。 |
|**調整單一資料庫和彈性集區**||
| [調整單一資料庫](scripts/sql-database-monitor-and-scale-database-cli.md) | 在查詢 Azure SQL 資料庫的大小資訊後，將其擴縮為不同的計算大小。 |
| [調整彈性集區](scripts/sql-database-scale-pool-cli.md) | 將 SQL 彈性集區擴縮為不同的計算大小。 |
|**設定異地複寫和容錯移轉**||
| [將單一資料庫新增至容錯移轉群組](scripts/sql-database-add-single-db-to-failover-group-cli.md)| 建立資料庫和容錯移轉群組、將資料庫新增至容錯移轉群組，然後測試容錯移轉至次要伺服器。 |
| [設定彈性集區的容錯移轉群組](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | 建立資料庫、將其新增至彈性集區、將彈性集區新增至容錯移轉群組，然後測試是否能容錯移轉至次要伺服器。 |
| [使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| 為 Azure SQL 資料庫設定作用中異地複寫，並將其容錯移轉到次要複本。 |
| [使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| 為 SQL 彈性集區中的 Azure SQL 資料庫設定作用中異地複寫，然後將其容錯移轉到次要複本。 |
| **稽核與威脅偵測** |
| [設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-cli.md)| 設定 Azure SQL 資料庫的稽核與威脅偵測原則。 |
| **備份、還原、複製和匯入資料庫**||
| [備份資料庫](scripts/sql-database-backup-database-cli.md)| 將 Azure SQL 資料庫備份到 Azure 儲存體備份。 |
| [還原資料庫](scripts/sql-database-restore-database-cli.md)| 從異地備援備份還原 Azure SQL 資料庫，並將已刪除的 Azure SQL Database 還原為最新的備份。 |
| [將資料庫複製到新伺服器](scripts/sql-database-copy-database-to-new-server-cli.md) | 在新的 Azure SQL Server 中建立現有 Azure SQL 資料庫的複本。 |
| [從 bacpac 檔案匯入資料庫](scripts/sql-database-import-from-bacpac-cli.md)| 從 .bacpac  檔案將資料庫匯入 Azure SQL Server。 |
|||

深入了解[單一資料庫 Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)。

# <a name="managed-instance"></a>[受控執行個體](#tab/managed-instance)

下表包含適用於 Azure SQL Database 之 Azure CLI 指令碼範例的連結 - 受控執行個體。

| | |
|---|---|
| **建立受控執行個體**||
| [建立受控執行個體](scripts/sql-database-create-configure-managed-instance-cli.md)| 建立受控執行個體。 |
| **設定透明資料加密 (TDE)**||
| [使用 Azure Key Vault 來管理受控執行個體中的透明資料加密](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| 在各種重要案例中使用 Azure Key Vault 來設定 Azure SQL 受控執行個體中的透明資料加密 (TDE)。 |
|**設定容錯移轉群組**||
| [設定受控執行個體的容錯移轉群組](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | 建立兩個受控執行個體、將其新增至容錯移轉群組，然後測試是否能從主要受控執行個體容錯移轉到次要受控執行個體。 |
|||

如需其他的受控執行個體範例，請參閱[建立](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/)、[更新](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/)、[移動資料庫](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)、[使用](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)指令碼。

深入了解[受控執行個體 Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances)。

---
