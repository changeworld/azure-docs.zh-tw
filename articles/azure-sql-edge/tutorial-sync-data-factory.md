---
title: 使用 Azure Data Factory 從 Azure SQL Edge (預覽) 同步資料
description: 了解如何在 Azure SQL Edge (預覽) 與 Azure Blob 儲存體之間同步資料
keywords: SQL Edge, 從 SQL Edge 同步資料, SQL Edge 資料處理站
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 1238505a10214c315bd5f2ceb428cf097b3ef5c6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593452"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>教學課程：使用 Azure Data Factory 將 SQL Edge 中的資料同步至 Azure Blob 儲存體

在本教學課程中，您會使用 Azure Data Factory 將 Azure SQL Edge 執行個體中資料表的資料，以累加方式同步至 Azure Blob 儲存體。

## <a name="before-you-begin"></a>開始之前

如果您尚未在 Azure SQL Edge 部署中建立資料庫或資料表，請使用下列其中一種方法建立：

* 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) 或 [Azure Data Studio](/sql/azure-data-studio/download/) 連線到 SQL Edge。 執行 SQL 指令碼來建立資料庫和資料表。
* 透過直接連線至 SQL Edge 模組，使用 [SQLCMD](/sql/tools/sqlcmd-utility/) 建立 SQL 資料庫和資料表。 如需詳細資訊，請參閱[使用 sqlcmd 連線至資料庫引擎](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)。
* 使用 SQLPackage.exe 將 DAC 封裝檔案部署至 SQL Edge 容器。 若要將此程序自動化，您可以將 SqlPackage 檔案 URI 指定為模組所需屬性設定的一部分。 您也可以直接使用 SqlPackage.exe 用戶端工具，將 DAC 封裝部署至 SQL Edge。

    如需有關如何下載 SqlPackage.exe 的資訊，請參閱[下載並安裝 sqlpackage](/sql/tools/sqlpackage-download/)。 以下是 SqlPackage.exe 的一些範例命令。 如需詳細資訊，請參閱 SqlPackage.exe 文件。

    **建立 DAC 封裝**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **套用 DAC 封裝**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>建立 SQL 資料表及用於儲存和更新浮水印層級的程序

浮水印資料表用於儲存最後一個時間戳記，亦即資料已與 Azure 儲存體同步的截止時間。 Transact-SQL (T-SQL) 儲存的程序用於在每次同步後更新浮水印資料表。

在 SQL Edge 執行個體上執行下列命令：

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>建立 Data Factory 管線

在本節中，您會建立 Azure Data Factory 管線將 Azure SQL Edge 內資料表中的資料同步至 Azure Blob 儲存體。

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>使用 Data Factory UI 建立資料處理站

遵循此[教學課程](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)中的指示建立資料處理站。

### <a name="create-a-data-factory-pipeline"></a>建立 Data Factory 管線

1. 在 Data Factory 使用者介面的 [開始使用] 頁面上，選取 [建立管線]。

    ![建立 Data Factory 管線](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. 在管線 [屬性] 視窗的 [一般] 頁面上，輸入 **PeriodicSync** 作為名稱。

3. 新增 [查閱] 活動以取得舊浮水印值。 在 [活動] 窗格中展開 [一般]，並將 [查閱] 活動拖曳至管線設計工具介面。 將活動名稱變更為 **OldWatermark**。

    ![新增舊浮水印查閱](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. 切換至 [設定] 索引標籤，然後選取 [新增] 以新增**來源資料集**。 您現在將建立資料集來代表浮水印資料表中的資料。 此資料表包含先前複製作業中所使用的舊浮水印。

5. 在 [新增資料集] 視窗中選取 [Azure SQL Server]，然後選取 [繼續]。  

6. 在該資料集的 [設定屬性] 視窗中，於 [名稱] 底下輸入 **WatermarkDataset**。

7. 在 [已連結的服務] 視窗中，選取 [新增]，然後完成下列步驟：

    1. 在 [名稱] 底下，輸入 **SQLDBEdgeLinkedService**。

    2. 在 [伺服器名稱] 底下，輸入您的 SQL Edge 伺服器詳細資料。

    3. 從清單中選取您的**資料庫名稱**。

    4. 輸入您的 [使用者名稱] 和 [密碼]。

    5. 若要測試與 SQL Edge 執行個體的連線，請選取 [測試連線]。

    6. 選取 [建立]。

    ![建立連結的服務](media/tutorial-sync-data-factory/create-linked-service.png)

    7. 選取 [確定]。

8. 在 [設定] 索引標籤中，選取 [編輯]。

9. 在 [連線] 索引標籤中，為 [資料表] 選取 [[dbo].[watermarktable]]。 如果您想要預覽資料表中的資料，請選取 [預覽資料]。

10. 選取頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器。 在 [查閱] 活動的屬性視窗中，確認已在 [來源資料集] 清單中選取 **WatermarkDataset**。

11. 在 [活動] 窗格中展開 [一般]，並將另一個 [查閱] 活動拖曳至管線設計工具介面。 在屬性視窗的 [一般] 索引標籤中，將名稱設為 **NewWatermark**。 此查閱活動會從包含來源資料的資料表取得新浮水印值，因此可複製到目的地。

12. 在第二個 [查閱] 活動的屬性視窗中，切換至 [設定] 索引標籤，然後選取 [新增] 建立一個資料集以指向包含新浮水印值的來源資料表。

13. 在 [新增資料集] 視窗中，選取 **SQL Edge 執行個體**，然後選取 [繼續]。

    1. 在 [設定屬性] 視窗中，於 [名稱] 底部輸入 **SourceDataset**。 在 [已連結的服務] 底下，選取 [SQLDBEdgeLinkedService]。

    2. 在 [資料表] 底下，選取您要同步的資料表。 您也可以指定此資料集的查詢，如本教學課程稍後所述。 查詢會優先於您在此步驟中指定的資料表。

    3. 選取 [確定]。

14. 選取頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器。 在 [查閱] 活動的屬性視窗中，確認已在 [來源資料集] 清單中選取 [SourceDataset]。

15. 在 [使用查詢] 底下選取 [查詢]。 更新下列查詢中的資料表名稱，然後輸入查詢。 您將只會從資料表中選取 `timestamp` 的最大值。 請務必選取 [僅第一列]。

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![選取查詢](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. 在 [活動] 窗格中展開 [移動和轉換]，然後從 [活動] 窗格將 [複製] 活動拖曳至設計工具介面。 將活動的名稱設定為 **IncrementalCopy**。

17. 透過將 [查閱] 活動所附加的綠色按鈕拖曳至 [複製] 活動，即可將兩個 [查閱] 活動同時連線至 [複製] 活動。 當您看到 [複製] 活動的框線顏色變為藍色時，即鬆開滑鼠按鍵。

18. 選取 [複製] 活動並確認您在 [屬性] 視窗中看到活動的屬性。

19. 在 [屬性] 視窗中切換至 [來源] 索引標籤，並完成下列步驟：

    1. 在 [來源資料集] 方塊中選取 [SourceDataset]。

    2. 在 [使用查詢] 底下選取 [查詢]。

    3. 在 [查詢] 方塊中輸入 SQL 查詢。 以下是查詢範例：

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. 在 [接收] 索引標籤上，於 [接收資料集] 底下選取 [新增]。

21. 在本教學課程中，接收資料存放區是 Azure Blob 儲存體資料存放區。 選取 [Azure Blob 儲存體]，然後選取 [新增資料集] 視窗中的 [繼續]。

22. 在 [選取格式] 視窗中，選取您資料的格式，然後選取 [繼續]。

23. 在 [設定屬性] 視窗中，於 [名稱] 底部輸入 **SinkDataset**。 在 [連結服務] 底下選取 [新增]。 您將建立與 Azure Blob 儲存體的連線 (連結的服務)。

24. 在 [新增已連結的服務 (Azure Blob 儲存體)] 視窗中，完成下列步驟：

    1. 在 [名稱] 底下，輸入 **AzureStorageLinkedService**。

    2. 在 [儲存體帳戶名稱] 底下，選取您 Azure 訂用帳戶的 Azure 儲存體帳戶。

    3. 測試連線，然後選取 [完成]。

25. 在 [設定屬性] 視窗中，確認已在 [已連結的服務] 底下選取 [AzureStorageLinkedService]。 選取 [建立]，再選取 [確定]。

26. 在 [接收] 索引標籤中，選取 [編輯]。

27. 移至 [SinkDataset] 的 [連線] 索引標籤，然後完成下列步驟：

    1. 在 [檔案路徑] 底下，輸入 asdedatasync/incrementalcopy，其中 asdedatasync 是 Blob 容器名稱，*incrementalcopy* 是資料夾名稱。 建立容器 (若不存在)，或使用現有容器的名稱。 如果輸出資料夾 incrementalcopy 不存在，Azure Data Factory 將會自動建立。 您也可以對**檔案路徑**使用 [瀏覽] 按鈕來瀏覽至 blob 容器中的資料夾。

    2. 為 [檔案路徑] 欄位的 [檔案] 部分選取 [新增動態內容 [Alt+P]]，然後在開啟的視窗中輸入 **@CONCAT('Incremental-', pipeline().RunId, '.txt')** 。 選取 [完成]。 運算式會以動態方式產生此檔案名稱。 每個管線執行都有唯一的識別碼。 複製活動會使用執行識別碼來產生檔案名稱。

28. 選取頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器。

29. 在 [活動] 窗格中展開 [一般]，並將 [預存程序] 活動從 [活動] 窗格拖曳至管線設計工具介面。 將 [複製] 活動的綠色 (成功) 輸出連線至 [預存程序] 活動。

30. 選取管線設計工具中的 [預存程序活動]，並將其名稱變更為 **SPtoUpdateWatermarkActivity**。

31. 切換至 [SQL 帳戶] 索引標籤，然後在 [已連結的服務] 底下選取 [*SQLDBEdgeLinkedService]。

32. 切換至 [預存程序] 索引標籤，然後完成下列步驟：

    1. 在 [預存程序名稱] 底下，選取 [[dbo].[usp_write_watermark]]。

    2. 若要指定預存程序參數的值，請選取 [匯入參數]，然後輸入參數的下列值：

    |名稱|類型|值|
    |-----|----|-----|
    |LastModifiedtime|Datetime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|String|@{activity('OldWaterMark').output.firstRow.TableName}|

33. 若要驗證管線設定，請選取工具列上的 [驗證]。 確認沒有任何驗證錯誤。 若要關閉 [管線驗證報告] 視窗，請選取 **>>** 。

34. 選取 [全部發佈] 按鈕，將實體 (連結的服務、資料集和管線) 發佈至 Azure Data Factory 服務。 請稍候，直到您看到確認發佈作業已成功的訊息。

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>根據排程觸發管線

1. 選取管線工具列上的 [新增觸發程序]，然後選取 [新增/編輯]，再選取 [新增]。

2. 將您的觸發程序命名為 **HourlySync**。 在 [類型] 底下，選取 [排程]。 將 [週期] 設定為每隔 1 小時。

3. 選取 [確定]。

4. 選取 [全部發佈]。

5. 選取 [立即觸發]。

6. 切換至左側的 [監視] 索引標籤。 您可以看到手動觸發程序所觸發的管線執行狀態。 選取 [重新整理] 即可重新整理清單。

## <a name="next-steps"></a>後續步驟

本教學課程中的 Azure Data Factory 管線會以每小時一次的頻率，將 SQL Edge 執行個體上資料表的資料複製到 Azure Blob 儲存體中的某個位置。 若想了解使用 Data Factory 的其他案例，請參閱這些[教學課程](../data-factory/tutorial-copy-data-portal.md)。
