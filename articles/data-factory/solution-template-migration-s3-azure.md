---
title: 將資料從 Amazon S3 遷移至 Azure Data Lake Storage Gen2
description: 瞭解如何透過使用外部控制資料表，將資料分割清單儲存在 AWS S3 上，以使用 Azure Data Factory 的解決方案範本，從 Amazon S3 遷移資料。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414767"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>將資料從 Amazon S3 遷移至 Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

您可以使用範本，將包含數十萬個檔案的數個數據，從 Amazon S3 遷移到 Azure Data Lake Storage Gen2。 

 > [!NOTE]
 > 如果您想要將小型資料磁片區從 AWS S3 複製到 Azure （例如，少於 10 TB），使用[Azure Data Factory 資料複製工具](copy-data-tool.md)會更有效率且容易。 本文所述的範本超出您所需的內容。

## <a name="about-the-solution-templates"></a>關於解決方案範本

當遷移超過 10 TB 的資料時，建議使用資料分割。 若要分割資料，請利用 [前置詞] 設定來依名稱篩選 Amazon S3 上的資料夾和檔案，然後每個 ADF 複製作業一次只能複製一個分割區。 您可以同時執行多個 ADF 複製工作，以獲得更好的輸送量。

資料移轉通常需要一次性的歷程記錄資料移轉，並定期將變更從 AWS S3 同步處理到 Azure。 以下有兩個範本，其中一個範本涵蓋一次歷程記錄資料移轉，另一個範本涵蓋將變更從 AWS S3 同步處理至 Azure。

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>供範本將歷程記錄資料從 Amazon S3 遷移至 Azure Data Lake Storage Gen2

此範本（*範本名稱：將歷程記錄資料從 AWS S3 遷移至 Azure Data Lake Storage Gen2*）假設您已在 Azure SQL Database 的外部控制資料表中寫入分割區清單。 因此，它會使用*查閱*活動來抓取外部控制資料表中的分割區清單、逐一查看每個資料分割，並讓每個 ADF 複製作業一次複製一個分割區。 完成任何複製作業之後，它會使用*預存*程式活動來更新控制資料表中每個資料分割的複製狀態。

此範本包含五個活動：
- **Lookup**會抓取尚未複製到外部控制資料表 Azure Data Lake Storage Gen2 的資料分割。 資料表名稱是*s3_partition_control_table* ，而從資料表載入資料的查詢是 *"SELECT PARTITIONPREFIX From s3_partition_control_table WHERE SuccessOrFailure = 0"*。
- **ForEach**會從*查閱*活動取得分割區清單，並將每個資料分割逐一查看至*TriggerCopy*活動。 您可以將*batchCount*設定為同時執行多個 ADF 複製作業。 我們已在此範本中設定2。
- **ExecutePipeline**會執行*CopyFolderPartitionFromS3*管線。 建立另一個管線讓每個複製作業複製分割區的原因，是因為它可讓您輕鬆地重新執行失敗的複製作業，以便再次從 AWS S3 重載該特定的分割區。 載入其他分割區的所有其他複製作業都不會受到影響。
- [**複製**] 會將每個磁碟分割從 AWS S3 複製到 Azure Data Lake Storage Gen2。
- **SqlServerStoredProcedure**會更新在控制資料表中複製每個資料分割的狀態。

此範本包含兩個參數：
- **AWS_S3_bucketName**是您想要在其中遷移資料的 AWS S3 上的值區名稱。 如果您想要在 AWS S3 上遷移多個值區的資料，您可以在外部控制資料表中新增一個以上的資料行，以儲存每個資料分割的值區名稱，同時也更新您的管線，據此抓取該資料行中的資料。
- **Azure_Storage_fileSystem**是您想要將資料移轉到的 Azure Data Lake Storage Gen2 上的檔案系統名稱。

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用於範本，只將變更的檔案從 Amazon S3 複製到 Azure Data Lake Storage Gen2

此範本（*範本名稱：將差異資料從 AWS S3 複製到 Azure Data Lake Storage Gen2*）會使用每個檔案的 LastModifiedTime，將新的或更新的檔案從 AWS S3 複製到 Azure。 請注意，如果您的檔案或資料夾已經以時間間隔資訊分割，做為 AWS S3 上檔案或資料夾名稱的一部分（例如/yyyy/mm/dd/file.csv），您可以移至本[教學](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)課程，以取得更具效能的方法來累加式載入新檔案。 此範本假設您已在 Azure SQL Database 的外部控制資料表中撰寫資料分割清單。 因此，它會使用*查閱*活動來抓取外部控制資料表中的分割區清單、逐一查看每個資料分割，並讓每個 ADF 複製作業一次複製一個分割區。 當每個複製作業開始從 AWS S3 複製檔案時，它會依賴 LastModifiedTime 屬性來識別並複製新的或更新的檔案。 完成任何複製作業之後，它會使用*預存*程式活動來更新控制資料表中每個資料分割的複製狀態。

此範本包含七個活動：
- **Lookup**會從外部控制資料表中抓取資料分割。 資料表名稱是*s3_partition_delta_control_table* ，而從資料表載入資料的查詢是 *"select distinct PartitionPrefix from s3_partition_delta_control_table"*。
- **ForEach**會從*查閱*活動取得分割區清單，並將每個資料分割逐一查看至*TriggerDeltaCopy*活動。 您可以將*batchCount*設定為同時執行多個 ADF 複製作業。 我們已在此範本中設定2。
- **ExecutePipeline**會執行*DeltaCopyFolderPartitionFromS3*管線。 建立另一個管線讓每個複製作業複製分割區的原因，是因為它可讓您輕鬆地重新執行失敗的複製作業，以便再次從 AWS S3 重載該特定的分割區。 載入其他分割區的所有其他複製作業都不會受到影響。
- **Lookup**會從外部控制資料表中抓取上次複製作業執行時間，以便透過 LastModifiedTime 來識別新的或更新的檔案。 資料表名稱是*s3_partition_delta_control_table* ，而從資料表載入資料的查詢是 *"Select max （JobRunTime） as LastModifiedTime From s3_partition_delta_control_table where PartitionPrefix = ' @ {pipeline （）. parameters. prefixStr} ' 和 SuccessOrFailure = 1"*。
- **複製**只會將每個磁碟分割的新檔案或變更的檔案從 AWS S3 複製到 Azure Data Lake Storage Gen2。 *ModifiedDatetimeStart*的屬性會設定為上次複製作業執行時間。 *ModifiedDatetimeEnd*的屬性會設定為目前的複製作業執行時間。 請注意，時間會套用至 UTC 時區。
- **SqlServerStoredProcedure**會更新複製每個資料分割的狀態，並在控制資料表成功時複製執行時間。 SuccessOrFailure 的資料行設定為1。
- **SqlServerStoredProcedure**會更新複製每個資料分割的狀態，並在 [控制資料表] 失敗時複製執行時間。 SuccessOrFailure 的資料行設定為0。

此範本包含兩個參數：
- **AWS_S3_bucketName**是您想要在其中遷移資料的 AWS S3 上的值區名稱。 如果您想要在 AWS S3 上遷移多個值區的資料，您可以在外部控制資料表中新增一個以上的資料行，以儲存每個資料分割的值區名稱，同時也更新您的管線，據此抓取該資料行中的資料。
- **Azure_Storage_fileSystem**是您想要將資料移轉到的 Azure Data Lake Storage Gen2 上的檔案系統名稱。

## <a name="how-to-use-these-two-solution-templates"></a>如何使用這兩個解決方案範本

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>供範本將歷程記錄資料從 Amazon S3 遷移至 Azure Data Lake Storage Gen2 

1. 在 Azure SQL Database 中建立控制資料表，以儲存 AWS S3 的資料分割清單。 

    > [!NOTE]
    > 資料表名稱為 s3_partition_control_table。
    > 控制資料表的架構為 PartitionPrefix 和 SuccessOrFailure，其中 PartitionPrefix 是 S3 中的前置詞設定，用來依名稱篩選 Amazon S3 中的資料夾和檔案，而 SuccessOrFailure 是複製每個分割區的狀態：0表示此分割區尚未複製到 Azure，1表示此分割區已成功複製到 Azure。
    > [控制資料表] 中定義了5個數據分割，而 [複製每個分割區] 的預設狀態為0。

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. 在 [控制資料表] 的相同 Azure SQL Database 上建立預存程式。 

    > [!NOTE]
    > 預存程式的名稱為 sp_update_partition_success。 它將由您 ADF 管線中的 SqlServerStoredProcedure 活動叫用。

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. 移至將歷程**記錄資料從 AWS S3 遷移至 Azure Data Lake Storage Gen2**範本。 輸入外部控制資料表的連接，AWS S3 作為資料來源存放區，並 Azure Data Lake Storage Gen2 做為目的地存放區。 請注意，外部控制資料表和預存程式都是相同連接的參考。

    ![建立新的連線](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. 選取 [**使用此範本**]。

    ![使用此範本](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. 您會看到已建立2個管線和3個資料集，如下列範例所示：

    ![檢閱管線](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. 選取 [ **Debug**]，輸入**參數**，然後選取 **[完成]**。

    ![按一下 [Debug] * *](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. 您會看到類似下列範例的結果：

    ![檢閱結果](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用於範本，只將變更的檔案從 Amazon S3 複製到 Azure Data Lake Storage Gen2

1. 在 Azure SQL Database 中建立控制資料表，以儲存 AWS S3 的資料分割清單。 

    > [!NOTE]
    > 資料表名稱為 s3_partition_delta_control_table。
    > 控制資料表的架構為 PartitionPrefix、JobRunTime 和 SuccessOrFailure，其中 PartitionPrefix 是 S3 中的前置詞設定，用來依名稱篩選 Amazon S3 中的資料夾和檔案，JobRunTime 是複製作業執行時的日期時間值，而 SuccessOrFailure 是複製每個分割區的狀態：0表示此分割區已成功複製到 azure。
    > [控制資料表] 中定義了5個數據分割。 JobRunTime 的預設值可以是一次性歷程記錄資料移轉開始的時間。 ADF 複製活動會在 AWS S3 上複製在該時間之後最後修改的檔案。 複製每個分割區的預設狀態為1。

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. 在 [控制資料表] 的相同 Azure SQL Database 上建立預存程式。 

    > [!NOTE]
    > 預存程式的名稱為 sp_insert_partition_JobRunTime_success。 它將由您 ADF 管線中的 SqlServerStoredProcedure 活動叫用。

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. 移至將**差異資料從 AWS S3 複製到 Azure Data Lake Storage Gen2**範本。 輸入外部控制資料表的連接，AWS S3 作為資料來源存放區，並 Azure Data Lake Storage Gen2 做為目的地存放區。 請注意，外部控制資料表和預存程式都是相同連接的參考。

    ![建立新的連線](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. 選取 [**使用此範本**]。

    ![使用此範本](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. 您會看到已建立2個管線和3個資料集，如下列範例所示：

    ![檢閱管線](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. 選取 [ **Debug**]，輸入**參數**，然後選取 **[完成]**。

    ![按一下 [Debug] * *](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. 您會看到類似下列範例的結果：

    ![檢閱結果](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. 您也可以從 [ *select * from s3_partition_delta_control_table]* 查詢查看控制資料表的結果，您會看到類似下列範例的輸出：

    ![檢閱結果](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>後續步驟

- [從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
- [移動檔案](solution-template-move-files.md)