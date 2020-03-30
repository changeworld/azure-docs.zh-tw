---
title: 導入 BACPAC 檔以創建資料庫
description: 匯入 BACPAC 檔案以建立新的 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256233"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>快速入門：將 BACPAC 檔導入 Azure SQL 資料庫中的資料庫

您可以使用 [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 檔案將 SQL Server 資料庫匯入 Azure SQL Database 中的資料庫。 您可以從儲存在 Azure Blob 儲存體 (僅標準儲存體) 中，或內部部署中的本機儲存體中的 `BACPAC` 檔案匯入資料。 若要藉由提供更多且更快速的資源來最大化匯入速度，請在匯入程序期間，將您的資料庫調整為較高的服務層級與計算大小。 然後，您可以在匯入成功後相應減少。

> [!NOTE]
> 資料庫匯入後的相容性層級會以來源資料庫的相容性層級為基礎。

> [!IMPORTANT]
> 匯入資料庫後，您可以選擇於目前的相容性層級 (針對 AdventureWorks2008R2 資料庫為層級 100) 或更高層級運作資料庫。 如需於特定相容性層級操作資料庫的含意與選項詳細資訊，請參閱 [ALTER DATABASE 相容性層級 (ALTER DATABASE Compatibility Level)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。 如需相容性層級其他相關資料庫等級設定的資訊，另請參閱 [ALTER DATABASE 範圍組態 (ALTER DATABASE SCOPED CONFIGURATION)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。

## <a name="using-azure-portal"></a>使用 Azure 入口網站

觀看此視頻，瞭解如何從 Azure 門戶中的 BACPAC 檔導入，或繼續閱讀以下內容：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure 入口網站「僅」](https://portal.azure.com) ** 支援在 Azure SQL Database 中建立單一資料庫，且「僅能」** 從儲存在 Azure Blob 儲存體中的 BACPAC 檔案建立。

當前不支援使用 Azure PowerShell 從 BACPAC 檔將資料庫移轉到[託管實例](sql-database-managed-instance.md)。 請使用 SQL 伺服器管理工作室或 SQL 包。

> [!NOTE]
> 處理通過 Azure 門戶或 PowerShell 提交的導入/匯出請求的電腦需要存儲 BACPAC 檔以及資料層應用程式框架 （DacFX） 生成的暫存檔案。 在大小相同的資料庫之間所需的磁碟空間差異很大，可能需要高達資料庫大小的 3 倍的磁碟空間。 運行導入/匯出請求的電腦僅具有 450GB 的本地磁碟空間。 因此，某些請求可能會失敗與錯誤`There is not enough space on the disk`。 在這種情況下，解決方法是在具有足夠本地磁碟空間的電腦上運行 sqlpackage.exe。 我們鼓勵使用 SqlPackage 導入/匯出大於 150GB 的資料庫以避免此問題。

1. 若要使用 Azure 入口網站從 BACPAC 檔案匯入至單一資料庫，請開啟適當的資料庫伺服器頁面，然後在工具列上選取 [匯入資料庫]****。  

   ![資料庫匯入1](./media/sql-database-import/sql-server-import-database.png)

1. 選取 BACPAC 檔案的儲存體帳戶及容器，然後選取要匯入的 BACPAC 檔案。

1. 指定新資料庫的大小 (通常與原始資料庫相同)，並提供目的地 SQL Server 認證。 如需新 Azure SQL 資料庫可能之值的清單，請參閱[建立資料庫](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。

   ![資料庫匯入2](./media/sql-database-import/sql-server-import-database-settings.png)

1. 按一下 [確定]****。

1. 若要監視匯入進度，請開啟資料庫的伺服器頁面，然後在 [設定]**** 下選取 [匯入/匯出記錄]****。 匯入成功時，會處於 [已完成]**** 狀態。

   ![資料庫匯入狀態](./media/sql-database-import/sql-server-import-database-history.png)

1. 若要確認資料庫伺服器上的資料庫為線上狀態，請選取 [SQL 資料庫]****，並確認新的資料庫為 [線上]**** 狀態。

## <a name="using-sqlpackage"></a>使用 Sql 包

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯入 SQL Server 資料庫，請參閱[匯入參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 具有最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和[適用於 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)。 您也可以從 Microsoft 下載中心下載最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

為了規模和效能，我們建議在大部分生產環境中使用 SqlPackage，而不使用 Azure 入口網站。 如需 SQL Server 客戶諮詢小組部落格中有關使用 `BACPAC` 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) \(英文\)。

下列 SqlPackage 命令會將 **AdventureWorks2008R2** 資料庫從本機儲存體匯入至名為 **mynewserver20170403** 的 Azure SQL Database 伺服器。 此命令會建立名為 **myMigratedDatabase**、且具有**進階**服務層級和 **P6** 服務目標的新資料庫。 請針對您的環境適當變更這些值。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要連線到從公司防火牆後方管理單一資料庫的 SQL Database 伺服器，該防火牆必須開啟連接埠 1433。 若要連線到受控執行個體，您必須有[點對站連線](sql-database-managed-instance-configure-p2s.md)或快速路由連線。

此範例說明如何使用 SqlPackage 與 Active Directory 通用驗證來匯入資料庫。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>使用 PowerShell

> [!NOTE]
> [託管實例](sql-database-managed-instance.md)當前不支援使用 Azure PowerShell 從 BACPAC 檔將資料庫移轉到實例資料庫中。 若要匯入至受控執行個體，請使用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 處理通過門戶或 Powershell 提交的導入/匯出請求的電腦需要存儲 bacpac 檔以及資料層應用程式框架 （DacFX） 生成的暫存檔案。 在大小相同的 DB 中所需的磁碟空間差異很大，並且可能需要高達資料庫大小的 3 倍。 運行導入/匯出請求的電腦僅具有 450GB 的本地磁碟空間。 因此，某些請求可能會失敗，出現"磁片上空間不足"錯誤。 在這種情況下，解決方法是在具有足夠本地磁碟空間的電腦上運行 sqlpackage.exe。 導入/匯出大於 150GB 的資料庫時，請使用 SqlPackage 避免此問題。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure 資源管理器 （RM） 模組仍受 Azure SQL 資料庫支援，但所有後續開發都針對 Az.Sql 模組。 AzureRM 模組將繼續接收錯誤修復，至少直到 2020 年 12 月。  Az 模組和 AzureRm 模組中命令的參數基本相同。 有關其相容性的更多資訊，請參閱[介紹新的 Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。

使用[New-AzSql 資料庫導入](/powershell/module/az.sql/new-azsqldatabaseimport)Cmdlet 向 Azure SQL 資料庫服務提交導入資料庫請求。 匯入可能需要一些時間才能完成，視資料庫大小而定。

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

您可以使用[獲取-AzSql 資料庫導入狀態](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus)Cmdlet 檢查導入的進度。 在請求後立即運行 Cmdlet 通常會返回`Status: InProgress`。 當您看到`Status: Succeeded`時，導入已完成。

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用[az-sql-db 導入](/cli/azure/sql/db#az-sql-db-import)命令向 Azure SQL 資料庫服務提交導入資料庫請求。 匯入可能需要一些時間才能完成，視資料庫大小而定。

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> 如需其他指令碼範例，請參閱[從 BACPAC 檔案匯入資料庫](scripts/sql-database-import-from-bacpac-powershell.md)。

## <a name="limitations"></a>限制

不支援匯入至彈性集區中的資料庫。 您可以將資料匯入到單一資料庫，然後將資料庫移到彈性集區。

## <a name="import-using-wizards"></a>使用精靈匯入

您也可以使用這些精靈。

- [SQL Server Management Studio 中的匯入資料層應用程式精靈](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL 伺服器導入和匯出嚮導](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>後續步驟

- 要瞭解如何連接到和查詢導入的 SQL 資料庫，請參閱[快速入門：Azure SQL 資料庫：使用 SQL 伺服器管理工作室連接和查詢資料](sql-database-connect-query-ssms.md)。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)。
- 如需有關整個 SQL Server 資料庫移轉程序的討論，包括效能建議，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
