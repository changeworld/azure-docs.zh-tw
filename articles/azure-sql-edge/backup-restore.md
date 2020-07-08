---
title: 備份和還原資料庫-Azure SQL Edge （預覽）
description: 瞭解 Azure SQL Edge （預覽）中的備份和還原功能。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 92a37babbcc0bbba3845267ca2eb0f95b9fceafa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667857"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge-preview"></a>在 Azure SQL Edge 中備份和還原資料庫（預覽） 

Azure SQL Edge 是以 Linux 上最新版本的 Microsoft SQL Server 資料庫引擎為基礎。 它提供的備份和還原資料庫功能類似于在容器中執行的 Linux 上的 SQL Server 和 SQL Server。 備份和還原元件提供了重要的保護措施，以保護 Azure SQL Edge 資料庫中所儲存的資料。 

若要將重大資料遺失的風險降至最低，您應該定期備份您的資料庫，以定期保留對資料的修改。 規劃完善的備份和還原策略有助於保護資料庫，以防止各種失敗所導致的資料遺失。 藉由還原一組備份然後復原您的資料庫，來測試您的策略，以準備好有效回應嚴重損壞。

若要深入瞭解備份為何重要，請參閱[備份和還原 SQL Server 資料庫](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)。

Azure SQL Edge 可讓您備份到本機儲存體和 Azure blob，並從中還原。 如需詳細資訊，請參閱[使用 Azure Blob 儲存體 SQL Server 備份和還原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/)和[SQL SERVER 備份至 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

## <a name="back-up-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中備份資料庫

Azure SQL Edge 支援與 SQL Server 相同的備份類型。 如需完整清單，請參閱[備份總覽](/sql/relational-databases/backup-restore/backup-overview-sql-server/)。

> [!IMPORTANT] 
> 根據預設，在 Azure SQL Edge 中建立的資料庫會使用簡單復原模式。 因此，您無法在這些資料庫上執行記錄備份。 如果您需要這樣做，則需要系統管理員將資料庫復原模式變更為完整復原模式。 如需 SQL Server 支援的完整復原模式清單，請參閱[復原模式總覽](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)。

### <a name="back-up-to-local-disk"></a>備份至本機磁片

在下列範例中，您會使用 `BACKUP DATABASE` transact-sql 命令，在容器中建立資料庫備份。 基於此範例的目的，您會建立名為*backup*的新資料夾來儲存備份檔案。

1. 建立用於備份的資料夾。 在您的 Azure SQL Edge 容器執行所在的主機上執行此命令。 在下列命令中，將 **<AzureSQLEdge_Container_Name>** 取代為部署中的 Azure SQL Edge 容器名稱。

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. 使用 SQL Server Management Studio （SSMS），或使用 Azure Data Studio 連接到 Azure SQL Edge 實例。 執行 `BACKUP DATABASE` 命令以備份您的使用者資料庫。 在下列範例中，您正在進行*IronOreSilicaPrediction*資料庫的備份。

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. 執行命令之後，如果資料庫的備份成功，您會在 SSMS 或 Azure Data Studio 的 [結果] 區段中看到類似下列的訊息。

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>備份至 URL

Azure SQL Edge 支援備份至分頁 Blob 和區塊 Blob。 如需詳細資訊，請參閱[備份至區塊 blob 和分頁 blob](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob)。 在下列範例中，資料庫*IronOreSilicaPrediction*正在備份到區塊 blob。 

1. 若要將備份設定為封鎖 blob，請先產生共用存取簽章（SAS）權杖，讓您可以用來在 Azure SQL Edge 上建立 SQL Server 認證。 此腳本會建立與預存存取原則相關聯的 SAS。 如需詳細資訊，請參閱[共用存取簽章，第1部分：瞭解 SAS 模型](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。 這個指令碼也會撰寫在 SQL Server 上建立認證所需的 T-SQL 命令。 下列腳本假設您已有 Azure 訂用帳戶和儲存體帳戶，以及用於備份的儲存體容器。

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    成功執行腳本之後，請將 `CREATE CREDENTIAL` 命令複製到查詢工具。 接著，連接到 SQL Server 的實例，然後執行命令以使用 SAS 建立認證。

2. 使用 SSMS 或 Azure Data Studio 連接到 Azure SQL Edge 實例，然後使用上一個步驟中的命令來建立認證。 請務必將命令取代為 `CREATE CREDENTIAL` 上一個步驟的實際輸出。

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. 下列命令會將 IronOreSilicaPrediction 備份至 Azure 儲存體容器。

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中還原資料庫

在 Azure SQL Edge 中，您可以從本機磁片、網路位置或 Azure Blob 儲存體帳戶進行還原。 如需 SQL Server 中還原和復原的詳細資訊，請參閱[還原和復原總覽](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15)。 如需 SQL Server 中簡單復原模式的總覽，請參閱[完整資料庫還原（簡單復原模式）](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15)。

### <a name="restore-from-a-local-disk"></a>從本機磁片還原

這個範例會使用您在上一個範例中所做的*IronOreSilicaPrediction*備份。 現在，您會將它還原為具有不同名稱的新資料庫。

1. 如果資料庫備份檔案尚未出現在容器中，您可以使用下列命令將檔案複製到容器中。 下列範例假設備份檔案存在於本機主機上，並將其複製到/var/opt/mssql/backup 資料夾中名為*sql1*的 Azure SQL Edge 容器。

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. 使用 SSMS 或 Azure Data Studio 連接到 Azure SQL Edge 實例，以執行還原命令。 在下列範例中， **IronOrePredictDB**會還原以建立新的資料庫， **IronOreSilicaPrediction_2**。

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. 執行 restore 命令之後，如果還原作業成功，您會在 [輸出] 視窗中看到類似下列的訊息。 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>從 URL 還原

Azure SQL Edge 也支援從 Azure 儲存體帳戶還原資料庫。 您可以從區塊 blob 或分頁 blob 備份進行還原。 在下列範例中，會還原區塊 blob 上的*IronOreSilicaPrediction_2020_04_16 .bak*資料庫備份檔案，以建立資料庫， *IronOreSilicaPrediction_3*。

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


