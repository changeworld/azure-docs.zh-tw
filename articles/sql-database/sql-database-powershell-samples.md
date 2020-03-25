---
title: Azure PowerShell 指令碼範例
description: Azure PowerShell 指令碼範例可協助您建立和管理 Azure SQL Database 伺服器、彈性集區、資料庫和防火牆。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: dd16753a9b057e441884b0a6a019701766aaa321
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73821407"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure SQL Database 的 Azure PowerShell 範例

Azure SQL Database 可讓您使用 Azure PowerShell 來設定資料庫、執行個體和集區。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝和使用 PowerShell，此教學課程需要 AZ PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="single-database-and-elastic-pools"></a>[單一資料庫與彈性集區](#tab/single-database)

下表包含適用於 Azure SQL Database 之範例 Azure PowerShell 指令碼的連結。

| |  |
|---|---|
|**建立並設定單一資料庫和彈性集區**||
| [建立單一資料庫並設定資料庫伺服器防火牆規則](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會建立單一 Azure SQL 資料庫，並設定伺服器層級防火牆規則。 |
| [建立彈性集區並移動集區資料庫](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會建立 Azure SQL Database 彈性集區、移動集區資料庫，並變更計算大小。|
|**設定異地複寫和容錯移轉**||
| [使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會為單一 Azure SQL 資料庫 設定作用中異地複寫，並將其容錯移轉到次要複本。 |
| [使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會為 SQL 彈性集區中的 Azure SQL 資料庫設定作用中異地複寫，並將其容錯移轉到次要複本。 |
|**設定容錯移轉群組**||
| [設定單一資料庫的容錯移轉群組](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會建立資料庫和容錯移轉群組，將資料庫新增至容錯移轉群組，並測試容錯移轉至次要伺服器。 | 
| [設定彈性集區的容錯移轉群組](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會建立資料庫、將其新增至彈性集區、將彈性集區新增至容錯移轉群組，然後測試是否能容錯移轉至次要伺服器。 | 
|**調整單一資料庫和彈性集區**||
| [調整單一資料庫](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會監視 Azure SQL 資料庫的效能計量，並將其調整為較高的計算大小，然後對其中一個效能計量建立警示規則。 |
| [調整彈性集區](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會監視 Azure SQL Database 彈性集區的效能計量，並將其調整為較高的計算大小，然後對其中一個效能計量建立警示規則。 |
| **稽核與威脅偵測** |
| [設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會設定 Azure SQL 資料庫的稽核與威脅偵測原則。 |
| **還原、複製和匯入資料庫**||
| [還原資料庫](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會從異地備援備份還原 Azure SQL 資料庫，並將已刪除的 Azure SQL 資料庫還原為最新的備份。 |
| [將資料庫複製到新伺服器](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會在新的 Azure SQL Server 中建立現有 Azure SQL 資料庫的複本。 |
| [從 bacpac 檔案匯入資料庫](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會從 bacpac 檔案將資料庫匯入 Azure SQL Server。 |
| **同步處理資料庫之間的資料**||
| [同步處理 SQL Database 之間的資料](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會設定「資料同步」在多個 Azure SQL 資料庫之間進行同步處理。 |
| [內部部署 SQL Database 與 SQL Server 之間的同步資料](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會設定「資料同步」在內部部署的 Azure SQL 資料庫和 SQL Server 之間進行同步處理。 |
| [更新 SQL 資料同步的同步結構描述](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 這個 PowerShell 指令碼會從資料同步的同步結構描述新增或移除項目。 |
|||

深入了解[單一資料庫 Azure PowerShell API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)。

## <a name="managed-instance"></a>[受控執行個體](#tab/managed-instance)

下表包含適用於 Azure SQL Database - 受控執行個體之範例 Azure PowerShell 指令碼的連結。

| |  |
|---|---|
|**建立及設定受控執行個體**||
| [建立和管理受控執行個體](scripts/sql-database-create-configure-managed-instance-powershell.md) | 此 PowerShell 指令碼說明如何使用 Azure PowerShell 建立和管理受控執行個體 |
| [使用 Azure Resource Manager 範本建立及管理受控執行個體](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼說明如何使用 Azure PowerShell 和 Azure Resource Manager 範本建立及管理受控執行個體。|
| [將資料庫還原到另一個地理區域中的 受控執行個體](scripts/sql-managed-instance-restore-geo-backup.md) | 此 PowerShell 指令碼會製作一個資料庫的備份，並將它還原到另一個區域。 這稱為異地還原災害復原案例。 |
| **設定透明資料加密 (TDE)**||
| [從 Azure Key Vault 使用自己的金鑰管理受控執行個體中的透明資料加密](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會使用來自 Azure Key Vault 的金鑰，在Azure SQL 受控執行個體的「攜帶您自己的金鑰」案例中設定透明資料加密 (TDE)。|
|**設定容錯移轉群組**||
| [設定受控執行個體的容錯移轉群組](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會建立兩個受控執行個體，並將其新增至容錯移轉群組，然後測試是否能從主要受控執行個體容錯移轉到次要受控執行個體。 | 
|||

深入了解[受控執行個體 Azure PowerShell API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)。

---

## <a name="additional-resources"></a>其他資源

此頁面列出的範例會使用 [Azure SQL Database Cmdlet](/powershell/module/az.sql/) 來建立和管理 Azure SQL 資源。 用來執行查詢及執行多項資料庫工作的其他 Cmdlet，位於 [sqlserver](/powershell/module/sqlserver/) 模組中。 如需詳細資訊，請參閱 [SQL Server PowerShell](/sql/powershell/sql-server-powershell/)。
