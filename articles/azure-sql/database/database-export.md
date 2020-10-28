---
title: '將 Azure SQL Database 匯出至 BACPAC 檔案 (Azure 入口網站) '
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 使用 Azure 入口網站將資料庫匯出至 BACPAC 檔案。
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.topic: how-to
ms.openlocfilehash: 7dc6cd580687544226b61a29ca9ccf2d1b8dff42
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671542"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>匯出至 BACPAC 檔案-Azure SQL Database 和 Azure SQL 受控執行個體

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

當您需要匯出資料庫以封存或移至另一個平台時，可以將資料庫結構描述和資料匯出至 [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4) 檔案。 BACPAC 檔案是副檔名為 BACPAC 的 ZIP 檔案，其中包含資料庫的中繼資料和資料。 BACPAC 檔案可以儲存在 Azure Blob 儲存體中，或儲存在內部部署位置的本機儲存體中，之後再匯入回 Azure SQL Database、Azure SQL 受控執行個體或 SQL Server 實例。

## <a name="considerations"></a>考量

- 若要讓匯出處於交易一致狀態，您必須確定在匯出期間未發生任何寫入活動，或是您要從資料庫的交易 [一致性複本](database-copy.md) 匯出。
- 如果您要匯出至 blob 儲存體，BACPAC 檔案的大小上限為 200 GB。 若要封存較大的 BACPAC 檔案，請將匯出到本機儲存體。
- 不支援使用本文所討論的方法將 BACPAC 檔案匯出到 Azure 進階儲存體。
- 目前不支援在防火牆後方的儲存體。
- 如果匯出作業超過 20 個小時，它可能會被取消。 若要增加匯出期間的效能，您可以︰

  - 暫時提高計算大小。
  - 在匯出期間停止所有讀取及寫入活動。
  - 在所有大型資料表上搭配使用 [叢集索引](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) 和非 null 值。 若沒有叢集索引，如果要花超過 6-12 小時，匯出可能會失敗。 這是因為匯出服務需要完成資料表掃描，以便嘗試匯出整份資料表。 有一個可判斷資料表是否已針對匯出進行最佳化的好方法，就是執行 **DBCC SHOW_STATISTICS** ，並確定 *RANGE_HI_KEY* 不是 null 且其值具有良好的分佈。 如需詳細資料，請參閱 [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql)。

> [!NOTE]
> BACPAC 並非用於備份和還原作業。 Azure 會自動為每個使用者資料庫建立備份。 如需詳細資訊，請參閱[商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)和 [SQL Database 備份](automated-backups-overview.md)。

## <a name="the-azure-portal"></a>Azure 入口網站

目前不支援使用 Azure 入口網站從 [AZURE SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 匯出資料庫的 BACPAC。 請改用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 處理透過 Azure 入口網站或 PowerShell 提交的匯入/匯出要求的機器，必須儲存 BACPAC 檔案以及資料層應用程式架構 (DacFX) 所產生的暫存檔案。 大小相同的資料庫所需的磁碟空間有很大的差異，且最多可能需要資料庫大小 3 倍的磁碟空間。 執行匯入/匯出要求的電腦只有450GB 的本機磁碟空間。 因此，某些要求可能會失敗，並出現錯誤 `There is not enough space on the disk`。 在此情況下，因應措施是在具有足夠本機磁碟空間的機器上執行 sqlpackage.exe。 我們鼓勵使用 [SqlPackage](#sqlpackage-utility) 來匯入/匯出大於150GB 的資料庫，以避免此問題。

1. 若要使用  。

   ![醒目顯示 [匯出] 按鈕的螢幕擷取畫面。](./media/database-export/database-export1.png)

2. 指定 BACPAC 檔案名稱、選取要匯出之現有的 Azure 儲存體帳戶和容器，然後提供適當的認證以存取來源資料庫。 如果您是 Azure 系統管理員，則需要 SQL **Server 系統管理員登** 入，因為 azure 系統管理員不等於具有 Azure SQL Database 或 azure SQL 受控執行個體的系統管理員許可權。

    ![資料庫匯出](./media/database-export/database-export2.png)

3. 按一下 [確定]。

4. 若要監視匯出作業的進度，請開啟包含要匯出之資料庫的伺服器頁面。 在 [設定]  下，然後按一下 [匯入/匯出記錄]  。

   ![匯出記錄](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SQLPackage 公用程式

若要使用 [SqlPackage](/sql/tools/sqlpackage) 命令列公用程式在 SQL Database 中匯出資料庫，請參閱 [匯出參數和屬性](/sql/tools/sqlpackage#export-parameters-and-properties)。 SQLPackage 公用程式隨附於最新版的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools for Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)，或者您也可以直接從 Microsoft 下載中心下載最新版的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

針對大部分生產環境中的延展性和效能，我們建議您使用 SQLPackage 公用程式。 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)。

此範例會說明如何透過 Active Directory 通用驗證使用 SqlPackage.exe 匯出資料庫：

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

SQL Server Management Studio 的最新版本會提供一個嚮導，將 Azure SQL Database 或 SQL 受控執行個體資料庫中的資料庫匯出至 BACPAC 檔案。 請參閱[匯出資料層應用程式](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)。

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [AZURE SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 目前不支援使用 Azure PowerShell 將資料庫匯出至 BACPAC 檔案。 若要將受控執行個體匯出到 BACPAC 檔案，請使用 SQL Server Management Studio 或 SQLPackage。

使用 [AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) 指令程式，將匯出資料庫要求提交給 Azure SQL Database 服務。 視資料庫大小而定，匯出作業可能需要一些時間才能完成。

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

若要檢查匯出要求的狀態，請使用 [AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Cmdlet。 如果在要求後立即執行此 Cmdlet，通常會傳回 **Status : InProgress** 。 當您看見 **Status: Succeeded** 時，便代表匯出已完成。

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

- 若要瞭解單一資料庫和集區資料庫的長期備份保留，以做為封存用途匯出資料庫的替代方案，請參閱 [長期備份保留](long-term-retention-overview.md)。 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代長期備份保留。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)。
- 若要了解如何將 BACPAC 匯入 SQL Server 資料庫，請參閱 [將 BACPAC 匯入 SQL Server 資料庫](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)。
- 若要了解如何將 BACPAC 從 SQL Server 資料庫匯出，請參閱[匯出資料層應用程式](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- 若要瞭解如何使用資料移轉服務來遷移資料庫，請參閱 [使用 DMS 從 SQL Server 遷移至 Azure SQL Database 離線](../../dms/tutorial-sql-server-to-azure-sql.md)。
- 如果您要從 SQL Server 匯出以準備移轉至 Azure SQL Database，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](migrate-to-database-from-sql-server.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](../../storage/blobs/security-recommendations.md)。