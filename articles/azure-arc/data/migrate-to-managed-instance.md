---
title: 從 SQL Server 將資料庫移轉至啟用 Azure Arc 的 SQL 受控執行個體
description: 從 SQL Server 將資料庫移轉至啟用 Azure Arc 的 SQL 受控執行個體
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934522"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>遷移： SQL Server Azure Arc 啟用的 SQL 受控執行個體

此案例會逐步引導您透過兩種不同的備份和還原方法，從 SQL Server 實例將資料庫移轉至 Azure Arc 中的 Azure SQL 受控實例。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>使用 Azure blob 儲存體 

使用 Azure blob 儲存體來遷移至已啟用 Azure Arc 的 SQL 受控執行個體。

這個方法會使用 Azure Blob 儲存體作為暫時儲存位置，讓您可以備份到，然後從中還原。

### <a name="prerequisites"></a>必要條件

- [安裝 Azure Data Studio](install-client-tools.md)
- [安裝 Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
- Azure 訂用帳戶

### <a name="step-1-provision-azure-blob-storage"></a>步驟1：布建 Azure blob 儲存體

1. 遵循[建立 Azure Blob 儲存體帳戶](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)中所述的步驟
1. 啟動 Azure 儲存體總管
1. 登[入 Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure)以存取在上一個步驟中建立的 blob 儲存體
1. 以滑鼠右鍵按一下 blob 儲存體帳戶，然後選取 [ **建立 Blob 容器** ] 來建立將儲存備份檔案的新容器。

### <a name="step-2-get-storage-blob-credentials"></a>步驟2：取得儲存體 blob 認證

1. 在 Azure 儲存體總管中，以滑鼠右鍵按一下剛建立的 blob 容器，然後選取 [**取得共用存取**簽章]

1. 選取 [**讀取**]、[**寫入**] 和 [**清單**]

1. 選取 [建立] 

   記下此畫面中的 URI 和查詢字串。 稍後的步驟將會需要這些。 按一下 [ **複製** ] 按鈕，以儲存至 [記事本/OneNote] 等等。

1. 關閉 [ **共用存取** 簽章] 視窗。

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>步驟3：將資料庫檔案備份到 Azure Blob 儲存體

在此步驟中，我們將連接到來源 SQL Server，並建立想要遷移至 SQL 受控執行個體 Azure Arc 之資料庫的備份檔案。

1. 啟動 Azure Data Studio
1. 連接到您想要遷移至 SQL 受控執行個體之資料庫的 SQL Server 實例 Azure Arc
1. 以滑鼠右鍵按一下資料庫，然後選取 [追加**查詢**]
1. 以下列格式準備您的查詢， `<...>` 以使用先前步驟中的共用存取簽章資訊來取代所指定的預留位置。  取代這些值之後，請執行查詢。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. 同樣地，請依照下列方式準備 **BACKUP DATABASE** 命令，以建立 blob 容器的備份檔案。  取代這些值之後，請執行查詢。

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. 開啟 Azure 儲存體總管，並驗證在先前步驟中建立的備份檔案會顯示在 Blob 容器中

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>步驟4：將資料庫從 Azure blob 儲存體還原至 SQL 受控執行個體-Azure Arc

1. 從 Azure Data Studio，登入並連接到 SQL 受控執行個體 Azure Arc。
1. 展開 [ **系統資料庫**]，以滑鼠右鍵按一下 **master** 資料庫，然後選取 [追加 **查詢**]。
1. 在 [查詢編輯器] 視窗中，準備並執行與上一個步驟相同的查詢來建立認證。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. 準備並執行下列命令，以確認備份檔案可讀取且不變。

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. 依照下列方式準備並執行 **RESTORE DATABASE** 命令，以將備份檔案還原至 SQL 受控執行個體上的資料庫 Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

若要深入瞭解備份至 URL：

[備份至 URL 檔](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[使用 SQL Server Management Studio (SSMS) 備份至 URL ](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>方法2：使用 kubectl 將備份檔案複製到 Azure SQL 受控執行個體 Azure Arc pod

此方法會示範如何建立您透過任何方法建立的備份檔案，然後將它複製到 Azure SQL 受控實例 pod 中的本機儲存體，以便在 Windows 或 Linux 上的一般檔案系統上進行還原。 在此案例中，您將會使用命令，將檔案 `kubectl cp` 從一個位置複製到 pod 的檔案系統。

### <a name="prerequisites"></a>必要條件

- 安裝並設定 kubectl 以指向您的 Kubernetes 叢集，其中部署 Azure Arc 資料服務
- 已安裝 Azure Data Studio 或 SQL Server 管理伺服器之類的工具，並連接到您要在其中建立備份檔案的 SQL Server，或已在本機檔案系統上建立現有的 .bak 檔案。

### <a name="step-1-backup-the-database-if-you-havent-already"></a>步驟1：如果您還沒有備份資料庫，

將 SQL Server 資料庫備份至本機檔案路徑，例如任何一般 SQL Server 備份至磁片：

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>步驟2：將備份檔案複製到 pod 的檔案系統

尋找部署 sql 實例的 pod 名稱。 通常看起來應該像這樣 `pod/<sqlinstancename>-0`

藉由執行下列各項來取得所有 pod 的清單：

 ```console
kubectl get pods -n <namespace of data controller>
```

範例：

將備份檔案從本機儲存體複製到叢集中的 sql pod。

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>步驟3：還原資料庫

準備並執行 RESTORE 命令以將備份檔案還原至 Azure SQL 受控實例-Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

範例：

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>下一步

[深入瞭解 Azure Arc 啟用的 SQL 受控執行個體的特性和功能](managed-instance-features.md)

[從建立資料控制器開始](create-data-controller.md)

[已經建立資料控制器了嗎？建立啟用 Azure Arc 的 SQL 受控執行個體](create-sql-managed-instance.md)