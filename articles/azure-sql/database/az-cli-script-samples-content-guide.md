---
title: Azure CLI 指令碼範例
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 用來建立及管理 Azure SQL Database 和 Azure SQL 受控執行個體的 Azure CLI 指令碼範例
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 0f28d8f82796f54002312514b1a34e3dd8b666a3
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675262"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>用於 Azure SQL Database 和 SQL 受控執行個體的 Azure CLI 範例 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

您可以使用 <a href="/cli/azure">Azure CLI</a> 來設定 Azure SQL Database 和 SQL 受控執行個體。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

下表包含用來管理 Azure SQL Database 中單一和集區資料庫的 Azure CLI 指令碼範例連結。 

|區域|描述|
|---|---|
|**在 Azure SQL Database 中建立資料庫**||
| [建立單一資料庫並設定防火牆規則](scripts/create-and-configure-database-cli.md) | 建立 SQL Database 並設定伺服器層級防火牆規則。 |
| [建立彈性集區並移動集區資料庫](scripts/move-database-between-elastic-pools-cli.md) | 建立彈性集區、移動集區資料庫，然後變更計算大小。 |
|**在 Azure SQL Database 中調整資料庫**||
| [調整單一資料庫](scripts/monitor-and-scale-database-cli.md) | 在查詢 SQL Database 中資料庫的大小資訊之後，將其擴縮為不同的計算大小。 |
| [調整彈性集區](scripts/scale-pool-cli.md) | 將 SQL 彈性集區擴縮為不同的計算大小。 |
|**設定異地複寫和容錯移轉**||
| [將單一資料庫新增至容錯移轉群組](scripts/add-database-to-failover-group-cli.md)| 建立資料庫和容錯移轉群組、將資料庫新增至容錯移轉群組，然後測試容錯移轉至次要伺服器。 |
| [設定彈性集區的容錯移轉群組](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | 建立資料庫、將其新增至彈性集區、將彈性集區新增至容錯移轉群組，然後測試是否能容錯移轉至次要伺服器。 |
| [使用作用中異地複寫設定單一資料庫並進行容錯移轉](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| 為單一 Azure SQL Database 內的資料庫設定作用中異地複寫，並將其容錯移轉到次要複本。 |
| [使用作用中異地複寫設定集區資料庫並進行容錯移轉](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| 為彈性集區中的資料庫設定作用中異地複寫，然後將其容錯移轉到次要複本。 |
| **稽核與威脅偵測** |
| [設定稽核與威脅偵測](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| 設定 Azure SQL Database 的稽核與威脅偵測原則。 |
| **備份、還原、複製和匯入資料庫**||
| [備份資料庫](../../sql-database/scripts/sql-database-backup-database-cli.md)| 將 SQL Database 中的資料庫備份到 Azure 儲存體備份。 |
| [還原資料庫](../../sql-database/scripts/sql-database-restore-database-cli.md)| 從異地備援備份還原 SQL Database 中的資料庫，並將已刪除的資料庫還原為最新的備份。 |
| [將資料庫複製到新伺服器](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | 在新的伺服器中建立現有 SQL Database 中資料庫的複本。 |
| [從 BACPAC 檔案匯入資料庫](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| 從 BACPAC 檔案將資料庫匯入 SQL Database。 |
|||

深入了解[單一資料庫 Azure CLI API](single-database-manage.md#the-azure-cli)。

# <a name="azure-sql-managed-instance"></a>[Azure SQL 受控執行個體](#tab/managed-instance)

下表包含適用於 Azure SQL 受控執行個體的 Azure CLI 指令碼範例連結。

|區域|描述|
|---|---|
| **建立 SQL 受控執行個體**||
| [建立 SQL 受控執行個體](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| 建立 SQL 受控執行個體。 |
| **設定透明資料加密 (TDE)**||
| [使用 Azure Key Vault 來管理 SQL 受控執行個體中的透明資料加密](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| 在各種重要案例中使用 Azure Key Vault 來設定 SQL 受控執行個體中的透明資料加密 (TDE)。 |
|**設定容錯移轉群組**||
| [設定 SQL 受控執行個體的容錯移轉群組](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | 建立兩個 SQL 受控執行個體的執行個體，並將其新增至容錯移轉群組，然後測試是否能從主要 SQL 受控執行個體容錯移轉至次要 SQL 受控執行個體。 |
|||

如需其他 SQL 受控執行個體範例，請參閱[建立](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli)、[更新](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli)、[移動資料庫](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)和[使用](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)指令碼。

深入了解 [SQL 受控執行個體 Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances)。

---