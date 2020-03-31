---
title: 將單個或池資料庫匯出到 BACPAC 檔
description: 使用 Azure 入口網站將 Azure SQL 資料庫匯出到 BACPAC 檔案
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061636"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>將 Azure SQL 資料庫匯出到 BACPAC 檔案

當您需要匯出資料庫以封存或移至另一個平台時，可以將資料庫結構描述和資料匯出至 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。 BACPAC 檔案是一種副檔名為 BACPAC 的 ZIP 檔案，其包含來自 SQL Server Database 的中繼資料和資料。 BACPAC 檔案可以儲存在 Azure Blob 儲存體，或在內部部署位置的本機儲存體中，之後再匯入至 Azure SQL Database 或 SQL Server 內部部署安裝。

## <a name="considerations-when-exporting-an-azure-sql-database"></a>匯出 Azure SQL 資料庫時的考量

- 要使匯出在事務上保持一致，必須確保在匯出期間未發生寫入活動，或者從 Azure SQL 資料庫[的事務一致性副本](sql-database-copy.md)匯出。
- 如果您要匯出至 blob 儲存體，BACPAC 檔案的大小上限為 200 GB。 若要封存較大的 BACPAC 檔案，請將匯出到本機儲存體。
- 不支援使用本文所討論的方法將 BACPAC 檔案匯出到 Azure 進階儲存體。
- 當前不支援防火牆後面的存儲。
- 如果執行從 Azure SQL Database 匯出的作業超過 20 個小時，它可能會被取消。 若要增加匯出期間的效能，您可以︰

  - 暫時提高計算大小。
  - 在匯出期間停止所有讀取及寫入活動。
  - 在所有大型資料表上搭配使用 [叢集索引](https://msdn.microsoft.com/library/ms190457.aspx) 和非 null 值。 若沒有叢集索引，如果要花超過 6-12 小時，匯出可能會失敗。 這是因為匯出服務需要完成資料表掃描，以便嘗試匯出整份資料表。 有一個可判斷資料表是否已針對匯出進行最佳化的好方法，就是執行 **DBCC SHOW_STATISTICS**，並確定 *RANGE_HI_KEY* 不是 null 且其值具有良好的分佈。 如需詳細資料，請參閱 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 並非用於備份和還原作業。 Azure SQL Database 會自動為每個使用者資料庫建立備份。 如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)和 [SQL Database 備份](sql-database-automated-backups.md)。

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>使用 Azure 入口網站匯出到 BACPAC 檔案

當前不支援使用 Azure PowerShell 從[託管實例](sql-database-managed-instance.md)匯出資料庫的 BACPAC。 請使用 SQL 伺服器管理工作室或 SQL 包。

> [!NOTE]
> 處理通過 Azure 門戶或 PowerShell 提交的導入/匯出請求的電腦需要存儲 BACPAC 檔以及資料層應用程式框架 （DacFX） 生成的暫存檔案。 在大小相同的資料庫之間所需的磁碟空間差異很大，可能需要高達資料庫大小的 3 倍的磁碟空間。 運行導入/匯出請求的電腦僅具有 450GB 的本地磁碟空間。 因此，某些請求可能會失敗與錯誤`There is not enough space on the disk`。 在這種情況下，解決方法是在具有足夠本地磁碟空間的電腦上運行 sqlpackage.exe。 我們鼓勵使用[SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility)導入/匯出大於 150GB 的資料庫以避免此問題。

1. 若要使用 [Azure 入口網站](https://portal.azure.com)匯出資料庫，請開啟資料庫頁面，然後按一下工具列上的 [匯出]****。

   ![資料庫匯出](./media/sql-database-export/database-export1.png)

2. 指定 BACPAC 檔案名稱、選取要匯出之現有的 Azure 儲存體帳戶和容器，然後提供適當的認證以存取來源資料庫。 即使您是 Azure 管理員，這裡也需要 SQL **Server 管理員登錄**，因為作為 Azure 管理員並不等於具有 SQL Server 管理員許可權。

    ![資料庫匯出](./media/sql-database-export/database-export2.png)

3. 按一下 [確定]****。

4. 若要監視匯出作業的進度，請開啟包含匯出資料庫的 SQL Database 伺服器頁面。 在 [設定]**** 下，然後按一下 [匯入/匯出記錄]****。

   ![匯出記錄](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>使用 SQLPackage 公用程式匯出到 BACPAC 檔案

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯出 SQL 資料庫，請參閱[匯出參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)。 SQLPackage 公用程式隨附於最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您也可以直接從 Microsoft 下載中心下載最新版的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

針對大部分生產環境中的延展性和效能，我們建議您使用 SQLPackage 公用程式。 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

此範例會說明如何透過 Active Directory 通用驗證使用 SqlPackage.exe 匯出資料庫：

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 匯出到 BACPAC 檔案

最新版的 SQL Server Management Studio 提供了精靈協助您將 Azure SQL 資料庫匯出至 BACPAC 檔案。 請參閱[匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)。

## <a name="export-to-a-bacpac-file-using-powershell"></a>使用 PowerShell 匯出到 BACPAC 檔案

> [!NOTE]
> [受控執行個體](sql-database-managed-instance.md)目前不支援使用 Azure PowerShell 將資料庫匯出至 BACPAC 檔案。 若要將受控執行個體匯出到 BACPAC 檔案，請使用 SQL Server Management Studio 或 SQLPackage。

使用[New-AzSql 資料庫匯出](/powershell/module/az.sql/new-azsqldatabaseexport)Cmdlet 向 Azure SQL 資料庫服務提交匯出資料庫請求。 視資料庫大小而定，匯出作業可能需要一些時間才能完成。

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

要檢查匯出請求的狀態，請使用[Get-AzSql 資料庫導入匯出狀態](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus)Cmdlet。 如果在要求後立即執行此 Cmdlet，通常會傳回 **Status : InProgress**。 當您看見 **Status: Succeeded** 時，便代表匯出已完成。

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>後續步驟

- 若要了解單一資料庫和集區資料庫的長期備份保留，以替換匯出的資料庫以達到封存目的，請參閱[長期備份保留](sql-database-long-term-retention.md)。 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代長期備份保留。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。
- 若要了解如何將 BACPAC 匯入 SQL Server 資料庫，請參閱 [將 BACPAC 匯入 SQL Server 資料庫](https://msdn.microsoft.com/library/hh710052.aspx)。
- 若要了解如何將 BACPAC 從 SQL Server 資料庫匯出，請參閱[匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- 若要了解如何使用資料移轉服務來移轉資料庫，請參閱[使用 DMS 在離線狀態下將 SQL Server 移轉至 Azure SQL Database](../dms/tutorial-sql-server-to-azure-sql.md)。
- 如果您要從 SQL Server 匯出以準備移轉至 Azure SQL Database，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
