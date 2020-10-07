---
title: 快速入門：還原備份 (SSMS)
titleSuffix: Azure SQL Managed Instance
description: 在本快速入門中，了解如何使用 SQL Server Management Studio (SSMS) 將資料庫備份還原至 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 18f717ca05e93c9a8f06ac8868e9a6e5ff80eadb
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355528"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>快速入門：使用 SSMS 將資料庫還原至 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在本快速入門中，您將使用 SQL Server Management Studio (SSMS)，將資料庫 (Wide World Importers - Standard 備份檔案) 從 Azure Blob 儲存體還原至 [Azure SQL 受控執行個體](sql-managed-instance-paas-overview.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> 如需使用 Azure 資料庫移轉服務進行移轉的詳細資訊，請參閱[使用資料庫移轉服務的 SQL 受控執行個體移轉](../../dms/tutorial-sql-server-to-managed-instance.md)。
> 如需各種移轉方法的詳細資訊，請參閱[將 SQL Server 移轉至 Azure SQL 受控執行個體](migrate-to-instance-from-sql-server.md)。

## <a name="prerequisites"></a>必要條件

此快速入門：

- 使用[建立受控執行個體](instance-create-quickstart.md)快速入門的資源。
- 需要安裝最新版本的 [SSMS](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)。
- 需要使用 SSMS 以連線到 SQL 受控執行個體。 請參閱這些快速入門以了解如何連線：
  - 在 SQL 受控執行個體上[啟用公用端點](public-endpoint-configure.md) - 這是本教學課程建議的方法。
  - [從 Azure VM 連線到 SQL 受控執行個體](connect-vm-instance-configure.md)。
  - [設定從內部部署連線至 SQL 受控執行個體的點對站連線](point-to-site-p2s-configure.md)。

> [!NOTE]
> 如需有關使用 Azure Blob 儲存體和[共用存取簽章 (SAS) 金鑰](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)來備份和還原 SQL Server 資料庫的詳細資訊，請參閱 [SQL Server 備份到 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)。

## <a name="restore-from-a-backup-file"></a>從備份檔案還原

在 SQL Server Management Studio 中，遵循這些步驟以將 Wide World Importers 資料庫還原至 SQL 受控執行個體。 資料庫備份檔案儲存在預先設定的 Azure Blob 儲存體帳戶中。

1. 開啟 SSMS 並連線到您的受控執行個體。
2. 在 [物件總管] 中，以滑鼠右鍵按一下您的受控執行個體，並選取 [新增查詢] 以開啟新的查詢視窗。
3. 執行下列 SQL 指令碼，其會使用預先設定的儲存體帳戶和 SAS 金鑰，在您的受控執行個體中[建立認證](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql)。

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![建立認證](./media/restore-sample-database-quickstart/credential.png)

4. 若要檢查您的認證，請執行下列指令碼，它會使用[容器](https://azure.microsoft.com/services/container-instances/) URL 來取得備份檔案清單。

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![檔案清單](./media/restore-sample-database-quickstart/file-list.png)

5. 執行下列程式碼以還原 Wide World Importers 資料庫。

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![此螢幕擷取畫面顯示在物件總管中執行的指令碼與成功訊息。](./media/restore-sample-database-quickstart/restore.png)

6. 執行下列程式碼來追蹤還原的狀態。

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. 當還原完成時，請在 [物件總管] 中檢視資料庫。 您可以使用 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 檢視來確認資料庫還原已完成。

> [!NOTE]
> 資料庫還原作業是非同步的，而且可以重試。 如果連線中斷或逾時已過期，您可能會在 SQL Server Management Studio 中收到錯誤。 Azure SQL Database 將會繼續嘗試在背景還原資料庫，而且您可以使用 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 檢視追蹤還原進度。
> 在還原程序的某些階段中，您將會在系統檢視中看到唯一識別碼，而不是實際的資料庫名稱。 在[這裡](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement)了解 `RESTORE` 陳述式行為差異。

## <a name="next-steps"></a>後續步驟

- 如果在步驟 5 中，以訊息識別碼 22003 終止資料庫還原，請建立包含備份總和檢查碼的新備份檔案，然後再次執行還原。 請參閱[在備份或還原期間啟用或停用備份總和檢查碼](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)。
- 若要對備份至 URL 進行疑難排解，請參閱 [SQL Server 備份至 URL 的最佳做法和疑難排解](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)。
- 如需有關應用程式連線選項的概觀，請參閱[將您的應用程式連線至 SQL 受控執行個體](connect-application-instance.md)。
- 若要使用慣用工具或語言進行查詢，請參閱[快速入門：Azure SQL Database 連線和查詢](../database/connect-query-content-reference-guide.md)。
