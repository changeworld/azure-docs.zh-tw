---
title: 備份和還原資料庫 - Azure SQL Edge (預覽)
description: 了解 Azure SQL Edge 中的備份和還原功能 (預覽)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 902576f82faa18fbb0e7c7eaed5c06993bd379cc
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235176"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>在 Azure SQL Edge 中備份和還原資料庫 (預覽) 

Azure SQL Edge 會以 Linux 上的最新版 Microsoft SQL Server 資料庫引擎為基礎，提供備份和還原資料庫的功能，就如同在 Linux 上的 SQL Server 和容器上執行的 SQL Server 中執行這些功能一樣。 備份和還原元件提供基本的防護措施，可保護 Azure SQL Edge 資料庫中所儲存的資料。 若要將重大資料遺失的風險降至最低，建議您定期備份資料庫，以定期保存您對資料所做的修改。 規劃完善的備份和還原策略有助於保護資料庫，以防止各種失敗所導致的資料遺失。 藉由還原一組備份並復原資料庫來測試您的策略，以做好有效因應災害的準備。

若要深入了解備份為何重要的原因，請參閱 [SQL Server 資料庫的備份與還原](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)。

Azure SQL Edge 支援備份到本機儲存體或 Azure Blob，也支援從這兩個項目中進行還原。 如需備份至 Azure Blob 儲存體及從中進行還原的詳細資訊，請參閱[使用 Microsoft Azure Blob 儲存體服務進行 SQL Server 備份及還原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/)和 [SQL Server 備份至 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

## <a name="backing-up-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中備份資料庫

Azure SQL Edge 支援的備份類型和 SQL Server 所支援的類型一樣。 如需 SQL Server 中支援的備份類型完整清單，請參閱[備份概觀](/sql/relational-databases/backup-restore/backup-overview-sql-server/)。

> [!IMPORTANT] 
> 根據預設，Azure SQL Edge 中建立的資料庫會使用簡單復原模式。 因此，記錄備份無法在這些資料庫上執行。 如果需要在這些資料庫上執行記錄備份，系統管理員必須將資料庫復原模式變更為完整復原模式。 如需 SQL Server 支援的復原模式完整清單，請參閱[復原模式概觀](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)。

### <a name="backup-to-local-disk"></a>備份至本機磁碟

在下面提供的範例中，BACKUP DATABASE Transact-SQL 命令會用來在容器中建立資料庫備份。 基於此範例的目的，我們會建立名為 "backup" 的新資料夾來儲存備份檔案。

1. 建立用於備份的資料夾。 此命令必須在您 Azure SQL Edge 容器執行所在的主機上執行。 在下列命令中，以您部署中的 Azure SQL Edge 容器名稱取代 **<AzureSQLEdge_Container_Name>** 。

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. 使用 SQL Server Management Studio (SSMS) 或使用 Azure Data Studio (ADS) 連線到 Azure SQL Edge 執行個體，然後執行備份資料庫命令來備份您的使用者資料庫。 在下列範例中，我們會備份 IronOreSilicaPrediction 資料庫。

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. 當您執行命令且資料庫備份成功後，您會在 SSMS 或 ADS 的結果區段中看到類似下列的訊息。

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

### <a name="backup-to-url"></a>備份至 URL

Azure SQL Edge 支援備份至分頁 Blob 和區塊 Blob。 如需有關分頁 Blob 和區塊 Blob 的詳細資訊，請參閱[備份到區塊 Blob 與分頁 Blob](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob)。 在下列範例中，我們會將 IronOreSilicaPrediction 資料庫備份至區塊 Blob。 

1. 備份至區塊 Blob 的第一個設定步驟是產生共用存取簽章 (SAS) 權杖，以用來在 Azure SQL Edge 上建立 SQL Server 認證。 此指令碼會建立關聯到預存存取原則的共用存取簽章。 如需詳細資訊，請參閱[共用存取簽章，第 1 部分：了解 SAS 模型](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。 這個指令碼也會撰寫在 SQL Server 上建立認證所需的 T-SQL 命令。 下方指令碼會假設您已有 Azure 訂用帳戶，而且其中包含用於備份的儲存體帳戶和儲存體容器。

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

    成功執行指令碼之後，請將 CREATE CREDENTIAL 命令複製到查詢工具，再連線到 SQL Server 執行個體，然後執行命令以建立含共用存取簽章的認證。

2. 使用 SQL Server Management Studio (SSMS) 或使用 Azure Data Studio (ADS) 連線到 Azure SQL Edge 執行個體，並使用上一個步驟中的命令建立認證。 請務必將 CREATE CREDENTIAL 命令取代為上一個步驟的實際輸出。

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

## <a name="restoring-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中還原資料庫

Azure SQL Edge 支援從本機磁碟、網路位置或 Azure Blob 儲存體帳戶進行還原。 如需在 SQL Server 中進行還原及復原的概觀，請參閱[還原及復原概觀](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15)。 如需 SQL Server 中簡單復原模式的概觀，請參閱[完整資料庫還原 (簡單復原模式)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15)。

### <a name="restore-from-local-disk"></a>從本機磁碟還原

此範例會使用上一個範例中執行的 IronOreSilicaPrediction 備份，來還原為不同名稱的新資料庫。

1. 如果資料庫備份檔案尚未出現在容器中，您可以使用下列命令將檔案複製到容器中。 下列範例會假設備份檔案存在於本機主機上，而我們要將其複製到 /var/opt/mssql/backup 資料夾中名為 sql1 的 Azure SQL Edge 容器。

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. 使用 SQL Server Management Studio (SSMS) 或使用 Azure Data Studio (ADS) 連線到 Azure SQL Edge 執行個體以執行還原命令。 在以下範例中，我們會還原 **IronOrePredictDB.bak**，以建立新的資料庫：**IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. 執行還原命令且還原作業成功之後，您會在輸出視窗中看到類似下列的訊息。 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>從 URL 還原

Azure SQL Edge 也支援從 Azure 儲存體帳戶還原資料庫。 您可以從區塊 Blob 或分頁 Blob 備份執行還原。 在下面提供的範例中，我們會還原區塊 Blob 上的 IronOreSilicaPrediction_2020_04_16.bak 資料庫備份檔案，然後建立 IronOreSilicaPrediction_3 資料庫。

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


