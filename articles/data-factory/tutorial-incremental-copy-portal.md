---
title: 使用 Azure 入口網站以累加方式複製資料表
description: 在本教學課程中，您會建立 Azure Data Factory 與管線，以將差異資料從 Azure SQL Database 中的資料表載入至 Azure Blob 儲存體。
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 6dba148f0cde81905bc66f7750ff5e04edc948aa
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566383"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>使用 Azure 入口網站以累加方式將資料從 Azure SQL Database 載入至 Azure Blob 儲存體

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教學課程中，您會建立 Azure Data Factory 與管線，以將差異資料從 Azure SQL Database 中的資料表載入至 Azure Blob 儲存體。

您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 準備資料存放區來儲存水位線值。
> * 建立資料處理站。
> * 建立連結的服務。
> * 建立來源、接收及水位線資料集。
> * 建立管道。
> * 執行管道。
> * 監視管道執行。
> * 檢閱結果
> * 將更多資料新增至來源。
> * 再次執行管線。
> * 監視第二次管線執行
> * 檢閱第二次執行的結果


## <a name="overview"></a>概觀
高階解決方案圖表如下：

![以累加方式載入資料](media/tutorial-Incremental-copy-portal/incrementally-load.png)

以下是建立此解決方案的重要步驟：

1. **選取水位線資料行**。
    選取來源資料存放區中的一個資料行，可用於切割每次執行時新增或更新的記錄。 一般來說，當建立或更新資料列時，這個選取的資料行 (例如，last_modify_time 或 ID) 中的資料會持續增加。 此資料行中的最大值就作為水位線。

2. **準備資料存放區來儲存水位線值**。 在本教學課程中，您會將水位線值儲存在 SQL 資料庫中。

3. **使用下列工作流程建立管線**：

    此解決方案中的管道有下列活動：

    * 建立兩個查閱活動。 使用第一個查閱活動來取出最後一個水位線值。 使用第二個查閱活動來取出新的水位線值。 這些水位線值會傳遞給複製活動。
    * 建立複製活動，以複製來源資料存放區的資料列，而這些資料列的水位線資料行值大於舊水位線值，且小於新水位線值。 然後，它會將來源資料存放區的差異資料複製到 Blob 儲存體作為新檔案。
    * 建立 StoredProcedure 活動，以更新下次執行的管線水位線值。


如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件
* **Azure SQL Database**。 您需要使用資料庫作為來源資料存放區。 如果您在 Azure SQL Database 中沒有資料庫，請參閱[在 Azure SQL Database 中建立資料庫](../azure-sql/database/single-database-create-quickstart.md)，按照步驟建立資料庫。
* **Azure 儲存體**。 您需要使用 Blob 儲存體作為接收資料存放區。 如果您沒有儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-account-create.md)，按照步驟來建立儲存體帳戶。 建立名為 adftutorial 的容器。 

### <a name="create-a-data-source-table-in-your-sql-database"></a>在 SQL 資料庫中建立資料來源資料表
1. 開啟 SQL Server Management Studio。 在 [伺服器總管] 中，以滑鼠右鍵按一下資料庫，然後選擇 [新增查詢]。

2. 對 SQL 資料庫執行下列 SQL 命令，以建立名為 `data_source_table` 的資料表作為資料來源存放區：

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    在本教學課程中，您會使用 LastModifytime 作為水位線資料行。 下表顯示資料來源存放區中的資料：

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>在 SQL 資料庫中建立另一個資料表來儲存高水位線值

1. 對 SQL 資料庫執行下列 SQL 命令，以建立名為 `watermarktable` 的資料表來儲存水位線值：  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. 使用來源資料存放區的資料表名稱來設定高水位線的預設值。 在本教學課程中，資料表名稱是 data_source_table。

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. 檢閱資料表 `watermarktable` 中的資料。

    ```sql
    Select * from watermarktable
    ```
    輸出：

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>在 SQL 資料庫中建立預存程序

執行下列命令，在您的 SQL 資料庫中建立預存程序：

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
2. 在左側功能表上，選取 [建立資源] > [整合] > [Data Factory]：

   ![在 [新增] 窗格中選取資料處理站](./media/doc-common-process/new-azure-data-factory-menu.png)

3. 在 [新增 Data Factory] 頁面中，輸入 [ADFTutorialOnPremDF] 作為 [名稱]。

   Azure Data Factory 的名稱必須是「全域唯一的」。 如果您看到有以下錯誤的紅色驚嘆號，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。

    *Data factory 名稱 "ADFIncCopyTutorialDF" 無法使用*
4. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。
5. 針對 [資源群組]，請執行下列其中一個步驟︰

      - 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。
      - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
        若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。  
6. 針對 [版本] 選取 [V2]。
7. 選取 Data Factory 的 [位置]  。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database、Azure SQL 受控執行個體等) 和計算 (HDInsight 等) 可位於其他區域。
8. 按一下 [建立]。      
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。

   ![Data Factory 首頁](./media/doc-common-process/data-factory-home-page.png)
10. 按一下 [撰寫與監視] 圖格，以在另一個索引標籤中啟動 Azure Data Factory 使用者介面 (UI)。

## <a name="create-a-pipeline"></a>建立管線
在本教學課程中，您會建立具有兩個查閱活動、一個複製活動和一個 StoredProcedure 活動的管線，這些活動都在一個管線中鏈結。

1. 在 Data Factory 使用者介面的 [開始使用] 頁面中，按一下 [建立管線] 圖格。

   ![Data Factory 使用者介面的開始使用網頁](./media/doc-common-process/get-started-page.png)    
3. 在 [屬性] 底下的 [一般] 面板中，針對 [名稱] 指定 **IncrementalCopyPipeline**。 然後按一下右上角的 [屬性] 圖示來摺疊面板。

4. 讓我們新增第一個查閱活動來取得舊的浮水印值。 在 [活動] 工具箱中展開 [一般]，並將 [查閱] 活動拖放至管線設計工具介面。 將活動名稱變更為 **LookupOldWaterMarkActivity**。

   ![第一個查閱活動 - 名稱](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. 切換至 [設定] 索引標籤，然後按一下 [+ 新增] 以新增 **來源資料集**。 在此步驟中，您會建立資料集來代表 **浮水印資料表** 中的資料。 此資料表包含先前複製作業中所使用的舊浮水印。

6. 在 [新增資料集] 視窗中選取 [Azure SQL Database]，然後按一下 [繼續]。 您會看到系統為該資料集開啟新視窗。

7. 在該資料集的 [設定屬性] 視窗中，輸入 **WatermarkDataset** 作為 [名稱]。

8. 在 [已連結的服務] 視窗中，選取 [新增]，然後執行下列步驟：

    1. 輸入 **AzureSqlDatabaseLinkedService** 作為 [名稱]。
    2. 選取要供 **伺服器名稱** 使用的伺服器。
    3. 從下拉式清單中選取您的 **資料庫名稱**。
    4. 輸入您的 **使用者名稱** & **密碼**。
    5. 若要測試您的 SQL 資料庫連線，請按一下 [測試連線]。
    6. 按一下 [完成] 。
    7. 確認已為 [已連結的服務] 選取 [AzureSqlDatabaseLinkedService]。

        ![新增連結服務視窗](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. 選取 [完成]。
9. 在 [連線] 索引標籤中，為 [資料表] 選取 [[dbo].[watermarktable]]。 如果您想要預覽資料表中的資料，請按一下 [預覽資料]。

    ![浮水印資料集 - 連線設定](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. 按一下頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器。 在 [查閱] 活動的 [屬性] 視窗中，確認已為 [來源資料集] 欄位選取 **WatermarkDataset**。

11. 在 [活動] 工具箱中展開 [一般]，並將另一個 [查閱] 活動拖放至管線設計工具介面，然後在 [屬性] 視窗的 [一般] 索引標籤中，將名稱設為 **LookupNewWaterMarkActivity**。 此查閱活動會從資料表取得新浮水印值，該資料表具備要複製到目的地的來源資料。

12. 在第二個 [查閱] 活動的 [屬性] 視窗中，切換到 [設定] 索引標籤，然後按一下 [新增]。 您建立的資料集會指向來源資料表，其中包含新浮水印值 (LastModifyTime 最大值)。

13. 在 [新增資料集] 視窗中選取 [Azure SQL Database]，然後按一下 [繼續]。
14. 在 [設定屬性] 視窗中，輸入 [SourceDataset] 作為 [名稱]。 選取 [AzureSqlDatabaseLinkedService] 作為 [連結服務]。
15. 選取 **[dbo].[data_source_table]** 作為 [資料表]。 您稍後可在本教學課程中指定對此資料集的查詢。 查詢會優先於您在此步驟中指定的資料表。
16. 選取 [完成]。
17. 按一下頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器。 在 [查閱] 活動的 [屬性] 視窗中，確認已為 [來源資料集] 欄位選取 [SourceDataset]。
18. 為[使用查詢] 欄位選取 [查詢]，並輸入下列查詢：您從 **data_source_table** 中選取的只有 **LastModifytime** 的最大值。 請確定您也已勾選 [僅限第一列]。

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![第二個查閱活動 - 查詢](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. 在 [活動] 工具箱中，展開 [移動和轉換]，並從 [活動] 工具箱中拖放 [複製] 活動，以及將名稱設定為 **IncrementalCopyActivity**。

20. 透過將 [查閱] 活動所附加的 **綠色按鈕** 拖曳至 [複製] 活動 **，即可將兩個 [查閱] 活動同時連線至 [複製] 活動**。 當您看到 [複製] 活動的框線顏色變為藍色時即鬆開滑鼠按鈕。

    ![將 [查閱] 活動連線至 [複製] 活動](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. 選取 [複製] 活動並確認您在 [屬性] 視窗中看到活動的屬性。

22. 在 [屬性] 視窗中切換至 [來源] 索引標籤，並執行下列步驟：

    1. 為 [來源資料集] 欄位選取 [SourceDataset]。
    2. 為 [使用查詢] 欄位選取 [查詢]。
    3. 為 [查詢] 欄位輸入下列 SQL 查詢。

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![複製活動 - 來源](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. 切換至 [接收] 索引標籤，然後按一下 [接收資料集] 欄位的 [+ 新增]。

24. 在本教學課程中，接收資料存放區是 Azure Blob 儲存體類型。 因此，選取 [Azure Blob 儲存體]，然後按一下 [新增資料集] 視窗中的 [繼續]。
25. 在 [選取格式] 視窗中，選取您資料的格式類型，然後按一下 [繼續]。
25. 在 [設定屬性] 視窗中，輸入 **SinkDataset** 作為 [名稱]。 為 [已連結的服務] 選取 [+ 新增]。 在此步驟中，您將建立與 **Azure Blob 儲存體** 的連線 (連結的服務)。
26. 在 [新增連結服務 (Azure Blob 儲存體)] 視窗中，執行下列步驟：

    1. 輸入 **AzureStorageLinkedService** 作為 [名稱]。
    2. 為 [儲存體帳戶名稱] 選取 Azure 儲存體帳戶。
    3. 測試連線，然後按一下 [完成]。

27. 在 [設定屬性] 視窗中，確認已為 [已連結的服務] 選取 [AzureStorageLinkedService]。 然後選取 [完成]。
28. 移至 SinkDataset 的 [連線] 索引標籤，然後執行下列步驟：
    1. 在 [檔案路徑] 欄位中，輸入 **adftutorial/incrementalcopy**。 **adftutorial** 是 blob 容器名稱而 **incrementalcopy** 是資料夾名稱。 此程式碼片段假設您在 Blob 儲存體中有一個名為 adftutorial 的 Blob 容器。 建立容器 (若不存在)，或設為現有容器的名稱。 如果輸出資料夾 **incrementalcopy** 不存在，Azure Data Factory 將會自動建立。 您也可以對 **檔案路徑** 使用 [瀏覽] 按鈕來瀏覽至 blob 容器中的資料夾。
    2. 為 [檔案路徑] 欄位的 [檔案] 部分選取 [新增動態內容 [Alt+P]]，然後在開啟的視窗中輸入 `@CONCAT('Incremental-', pipeline().RunId, '.txt')`。 然後選取 [完成]。 系統會使用運算式來動態產生此檔案名稱。 每個管線執行都有唯一的識別碼。 複製活動會使用執行識別碼來產生檔案名稱。

28. 按一下頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到 **管線** 編輯器。
29. 在 [活動] 工具箱中展開 [一般]，並將 [預存程序] 活動從 [活動] 工具箱拖放至管線設計工具介面。 將 [複製] 活動的綠色 (成功) 輸出 **連線** 至 [預存程序] 活動。

24. 選取管線設計工具中的 [預存程序活動]，將其名稱變更為 **StoredProceduretoWriteWatermarkActivity**。

25. 切換至 [SQL 帳戶] 索引標籤，然後選取 [AzureSqlDatabaseLinkedService] 作為 [已連結的服務]。

26. 切換至 [預存程序] 索引標籤，然後執行下列步驟：

    1. 針對 [預存程序名稱]，選取 **usp_write_watermark**。
    2. 若要指定預存程序參數的值，請按一下 [匯入參數]，然後輸入參數的下列值：

        | 名稱 | 類型 | 值 |
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | String | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

        ![預存程序活動 - 預存程序設定](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. 若要驗證管線設定，請按一下工具列上的 [驗證]。 確認沒有任何驗證錯誤。 若要關閉 [管線驗證報告] 視窗，請按一下 [>>]。   

28. 選取 [全部發佈] 按鈕，將實體 (連結的服務、資料集和管線) 發佈至 Azure Data Factory 服務。 請等候直至您看見成功發佈的訊息。


## <a name="trigger-a-pipeline-run"></a>觸發管線執行
1. 按一下工具列上的 [新增觸發程序]，然後按一下 [立即觸發]。

2. 在 [管線執行] 視窗中，選取 [完成]。

## <a name="monitor-the-pipeline-run"></a>監視管道執行

1. 切換至左側的 [監視] 索引標籤。 您可查看手動觸發程序所觸發的管線執行狀態。 您可以使用 [管線名稱] 資料行下的連結來檢視執行詳細資料，以及重新執行管線。

2. 若要查看與管線執行相關聯的活動執行，請選取 [管線名稱] 資料行下的連結。 如需有關活動執行的詳細資料，請選取 [活動名稱] 資料行下的 [詳細資料] 連結 (眼鏡圖示)。 選取頂端的 [所有管線執行] 以回到管線執行檢視。 若要重新整理檢視，請選取 [重新整理]。


## <a name="review-the-results"></a>檢閱結果
1. 透過使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)等工具來連線到您的 Azure 儲存體帳戶。 確認輸出檔案已建立於 **adftutorial** 容器的 **incrementalcopy** 資料夾中。

    ![第一個輸出檔案](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. 開啟輸出檔，請注意，所有的資料都會從 **data_source_table** 複製到 blob 檔案。

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. 檢查 `watermarktable` 中的最新值。 您會看到水位線值已更新。

    ```sql
    Select * from watermarktable
    ```

    輸出如下：

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>將更多資料新增至來源

將新資料插入您的資料庫 (資料來源存放區) 中。

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

您的資料庫中更新的資料如下：

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```

## <a name="trigger-another-pipeline-run"></a>觸發另一個管線執行

1. 切換至 [編輯] 索引標籤。如果管線沒有在設計工具中開啟，請在樹狀檢視中按一下它。

2. 按一下工具列上的 [新增觸發程序]，然後按一下 [立即觸發]。


## <a name="monitor-the-second-pipeline-run"></a>監視第二次管線執行

1. 切換至左側的 [監視] 索引標籤。 您可查看手動觸發程序所觸發的管線執行狀態。 您可以使用 [管線名稱] 資料行下的連結來檢視活動詳細資料，以及重新執行管線。

2. 若要查看與管線執行相關聯的活動執行，請選取 [管線名稱] 資料行下的連結。 如需有關活動執行的詳細資料，請選取 [活動名稱] 資料行下的 [詳細資料] 連結 (眼鏡圖示)。 選取頂端的 [所有管線執行] 以回到管線執行檢視。 若要重新整理檢視，請選取 [重新整理]。


## <a name="verify-the-second-output"></a>確認第二個輸出

1. 在 blob 儲存體中，您會看到已建立另一個檔案。 在本教學課程中，新的檔案名稱是 `Incremental-<GUID>.txt`。 開啟該檔案，您會在其中看到兩列記錄。

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. 檢查 `watermarktable` 中的最新值。 您會看到水位線值再次更新。

    ```sql
    Select * from watermarktable
    ```
    範例輸出：

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>後續步驟
在本教學課程中，您已執行下列步驟：

> [!div class="checklist"]
> * 準備資料存放區來儲存水位線值。
> * 建立資料處理站。
> * 建立連結的服務。
> * 建立來源、接收及水位線資料集。
> * 建立管道。
> * 執行管道。
> * 監視管道執行。
> * 檢閱結果
> * 將更多資料新增至來源。
> * 再次執行管線。
> * 監視第二次管線執行
> * 檢閱第二次執行的結果

在本教學課程中，管線已從 SQL Database 中的單一資料表將資料複製到 Blob 儲存體。 請前進到下列教學課程，了解如何將資料從 SQL Server 資料庫中的多個資料表複製到 SQL 資料庫。

> [!div class="nextstepaction"]
>[以累加方式將 SQL Server 中多個資料表的資料載入到 Azure SQL Database](tutorial-incremental-copy-multiple-tables-portal.md)
