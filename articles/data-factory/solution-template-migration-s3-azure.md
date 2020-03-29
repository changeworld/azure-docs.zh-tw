---
title: 將資料從 Amazon S3 遷移到 Azure 資料存儲第 2 代
description: 瞭解如何使用解決方案範本使用外部控制表在 AWS S3 上使用 Azure 資料工廠存儲分區清單，從而從 Amazon S3 遷移資料。
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
ms.openlocfilehash: e918fe01426202746f0225d25304b9c1b26cb74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927316"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>將資料從 Amazon S3 遷移到 Azure 資料存儲第 2 代

使用範本將由數億個檔組成的 PB 資料從 Amazon S3 遷移到 Azure 資料存儲第 2 代。 

 > [!NOTE]
 > 如果要將小資料卷從 AWS S3 複製到 Azure（例如，小於 10 TB），則使用[Azure 資料工廠複製資料工具](copy-data-tool.md)會更有效、更簡便。 本文中描述的範本不僅僅是您需要的範本。

## <a name="about-the-solution-templates"></a>關於解決方案範本

建議進行資料分區，尤其是在遷移超過 10 TB 的資料時。 要對資料進行分區，請利用"首碼"設置按名稱篩選 Amazon S3 上的資料夾和檔，然後每個 ADF 複製作業可以一次複製一個分區。 您可以同時運行多個 ADF 複製作業，以便提高輸送量。

資料移轉通常需要一次性歷史資料移轉，以及定期同步從 AWS S3 到 Azure 的更改。 下面有兩個範本，其中一個範本涵蓋一次性歷史資料移轉，另一個範本涵蓋同步從 AWS S3 到 Azure 的更改。

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用於將歷史資料從 Amazon S3 遷移到 Azure 資料存儲第 2 代

此範本（*範本名稱：將歷史資料從 AWS S3 遷移到 Azure 資料存儲第 2 代*）假定您在 Azure SQL 資料庫中的外部控制表中寫入了分區清單。 因此，它將使用*查找*活動從外部控制表檢索分區清單，反覆運算每個分區，並使每個 ADF 複製作業一次複製一個分區。 完成任何複製作業後，它使用*預存程序*活動更新複製控制表中每個分區的狀態。

該範本包含五個活動：
- **查找**從外部控制表檢索尚未複製到 Azure 資料湖存儲 Gen2 的分區。 表名稱*s3_partition_control_table，* 從表中載入資料的查詢為 *"從成功或失敗s3_partition_control_table選擇分區首碼 = 0"。*
- **ForEach**從*查找*活動獲取分區清單，並將每個分區重新添加到*觸發器複製*活動。 您可以將*批次處理計數*設置為同時運行多個 ADF 複製作業。 我們在此範本中設置了 2。
- **執行管道**執行*複製資料夾分區從S3*管道。 我們創建另一個管道以使每個複製作業複製分區的原因是，它使您能夠輕鬆重新運行失敗的複製作業，以便再次從 AWS S3 重新載入該特定分區。 載入其他分區的所有其他複製作業將不會受到影響。
- **將**每個分區從 AWS S3 複製到 Azure 資料湖存儲第 2 代。
- **SqlServer預存程序**更新複製控制表中每個分區的狀態。

該範本包含兩個參數：
- **AWS_S3_bucketName**是 AWS S3 上的存儲桶名稱，您希望從該位置遷移資料。 如果要從 AWS S3 上的多個存儲桶遷移資料，可以在外部控制表中再添加一列以存儲每個分區的存儲桶名稱，還可以更新管道以相應地從該列檢索資料。
- **Azure_Storage_fileSystem**是 Azure 資料存儲第 2 代伺服器上的檔案系統名稱，您希望將資料移轉到該天地。

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>範本僅將更改的檔從 Amazon S3 複製到 Azure 資料存儲庫第 2 代

此範本（*範本名稱：將增量資料從 AWS S3 複製到 Azure 資料存儲第 2 代*）使用每個檔的上次修改時間將新的或更新的檔僅從 AWS S3 複製到 Azure。 請注意，您的檔或資料夾是否已在 AWS S3 上作為檔或資料夾名稱的一部分使用時間片資訊進行分區（例如/yyyy/mm/dd/file.csv），您可以轉到[本教程](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)，以獲得增量載入新檔的性能更優異的方法。 此範本假定您在 Azure SQL 資料庫中的外部控制項表中編寫了分區清單。 因此，它將使用*查找*活動從外部控制表檢索分區清單，反覆運算每個分區，並使每個 ADF 複製作業一次複製一個分區。 當每個複製作業開始從 AWS S3 複製檔時，它僅依賴于 Last修改時間屬性來標識和複製新的或更新的檔。 完成任何複製作業後，它使用*預存程序*活動更新複製控制表中每個分區的狀態。

該範本包含七個活動：
- **查找**從外部控制表檢索分區。 表名稱*s3_partition_delta_control_table，* 從表中載入資料的查詢是 *"從s3_partition_delta_control_table中選擇不同的分區首碼*"。
- **ForEach**從*查找*活動獲取分區清單，並將每個分區重新添加到*觸發器DeltaCopy*活動。 您可以將*批次處理計數*設置為同時運行多個 ADF 複製作業。 我們在此範本中設置了 2。
- **執行管道**執行*增量複製資料夾從S3管道分區*。 我們創建另一個管道以使每個複製作業複製分區的原因是，它使您能夠輕鬆重新運行失敗的複製作業，以便再次從 AWS S3 重新載入該特定分區。 載入其他分區的所有其他複製作業將不會受到影響。
- **查找**從外部控制表檢索最後一個複製作業執行時間，以便可以通過"上次修改時間"標識新的或更新的檔。 表名稱*s3_partition_delta_control_table，* 從表中載入資料的查詢為 *"從 s3_partition_delta_control_table分區首碼[[管道（.參數.首碼.首碼.首碼）]和成功失敗 = 1"中選擇最大（作業執行時間）作為上次修改時間*。
- 僅為每個分區從 AWS S3 複製到 Azure 資料存儲單元 2**複製新**檔或更改的檔。 *修改的 DatetimeStart*的屬性設置為最後一個複製作業執行時間。 *修改的 DatetimeEnd*的屬性設置為當前複製作業執行時間。 請注意，時間已應用於 UTC 時區。
- **SqlServer預存程序**更新複製每個分區的狀態，並在控制表中複製運行時的狀態。 成功或失敗"列設置為 1。
- **SqlServer預存程序**更新複製每個分區的狀態，並在控制表中複製運行時的狀態。 成功或失敗"的列設置為 0。

該範本包含兩個參數：
- **AWS_S3_bucketName**是 AWS S3 上的存儲桶名稱，您希望從該位置遷移資料。 如果要從 AWS S3 上的多個存儲桶遷移資料，可以在外部控制表中再添加一列以存儲每個分區的存儲桶名稱，還可以更新管道以相應地從該列檢索資料。
- **Azure_Storage_fileSystem**是 Azure 資料存儲第 2 代伺服器上的檔案系統名稱，您希望將資料移轉到該天地。

## <a name="how-to-use-these-two-solution-templates"></a>如何使用這兩個解決方案範本

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>用於將歷史資料從 Amazon S3 遷移到 Azure 資料存儲第 2 代 

1. 在 Azure SQL 資料庫中創建一個控制表以存儲 AWS S3 的分區清單。 

    > [!NOTE]
    > 表名稱為s3_partition_control_table。
    > 控制表的架構是分區首碼和成功或失敗，其中分區首碼是 S3 中的首碼設置，用於按名稱篩選 Amazon S3 中的資料夾和檔，而成功失敗是複製每個分區的狀態：0 表示此分區具有未複製到 Azure，1 表示此分區已成功複製到 Azure。
    > 控制表中定義了 5 個分區，複製每個分區的預設狀態為 0。

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

2. 在同一 Azure SQL 資料庫上為控制項表創建預存程序。 

    > [!NOTE]
    > 預存程序的名稱為sp_update_partition_success。 它將由 ADF 管道中的 SqlServer預存程序活動調用。

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. 轉到**將歷史資料從 AWS S3 遷移到 Azure 資料存儲第 2 代**範本。 輸入到外部控制表的連接，AWS S3 作為資料來源存儲，Azure 資料存儲 Gen2 作為目標存儲。 請注意，外部控制表和預存程序是同一連接的引用。

    ![建立新的連線](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. 選擇**使用此範本**。

    ![使用此範本](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. 您將看到創建了 2 個管道和 3 個資料集，如以下示例所示：

    ![檢閱管線](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. 選擇 **"調試**"，輸入**參數**，然後選擇 **"完成**"。

    ![按一下 [調試]](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. 您將看到類似于以下示例的結果：

    ![檢閱結果](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>範本僅將更改的檔從 Amazon S3 複製到 Azure 資料存儲庫第 2 代

1. 在 Azure SQL 資料庫中創建一個控制表以存儲 AWS S3 的分區清單。 

    > [!NOTE]
    > 表名稱為s3_partition_delta_control_table。
    > 控制表的架構是分區首碼、作業執行時間和成功或失敗，其中分區首碼是 S3 中的首碼設置，用於按名稱篩選 Amazon S3 中的資料夾和檔，JobRunTime 是運行複製作業時的日期時間值，而成功或失敗是複製每個分區的狀態：0 表示此分區尚未複製到 Azure，1 表示此分區已成功複製到 Azure。
    > 控制表中定義了 5 個分區。 JobRunTime 的預設值可以是一次性歷史資料移轉開始的時間。 ADF 複製活動將複製 AWS S3 上上次修改後進行修改的檔。 複製每個分區的預設狀態為 1。

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

2. 在同一 Azure SQL 資料庫上為控制項表創建預存程序。 

    > [!NOTE]
    > 預存程序的名稱sp_insert_partition_JobRunTime_success。 它將由 ADF 管道中的 SqlServer預存程序活動調用。

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


3. 轉到從**AWS S3 複製到 Azure 資料存儲第 2 代範本的增量資料**。 輸入到外部控制表的連接，AWS S3 作為資料來源存儲，Azure 資料存儲 Gen2 作為目標存儲。 請注意，外部控制表和預存程序是同一連接的引用。

    ![建立新的連線](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. 選擇**使用此範本**。

    ![使用此範本](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. 您將看到創建了 2 個管道和 3 個資料集，如以下示例所示：

    ![檢閱管線](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. 選擇 **"調試**"，輸入**參數**，然後選擇 **"完成**"。

    ![按一下 [調試]](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. 您將看到類似于以下示例的結果：

    ![檢閱結果](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. 還可以通過查詢 *"從s3_partition_delta_control_table選擇*"* 來檢查控制表中的結果，您將看到類似于以下示例的輸出：

    ![檢閱結果](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>後續步驟

- [從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
- [移動檔案](solution-template-move-files.md)