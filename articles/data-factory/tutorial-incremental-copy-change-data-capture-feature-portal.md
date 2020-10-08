---
title: 使用異動資料擷取以累加方式複製資料
description: 在本教學課程中，您會建立 Azure Data Factory 管道，以累加方式將差異資料從 Azure SQL 受控執行個體資料庫中的資料表複製到 Azure 儲存體。
services: data-factory
ms.author: nihurt
author: hurtn
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: ''
ms.date: 05/04/2020
ms.openlocfilehash: 06dd55ce400667939fca4b0f48159f8b7dde66c6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825143"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>使用異動資料擷取 (CDC)，以累加方式從 Azure SQL 受控執行個體將資料載入 Azure 儲存體

在本教學課程中，您會建立 Azure data factory 與管道，以根據來源 Azure SQL 受控執行個體資料庫中的**異動資料擷取 (CDC)** 資訊，將差異資料載入至 Azure blob 儲存體。  

您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 準備來源資料存放區
> * 建立資料處理站。
> * 建立連結的服務。
> * 建立來源和接收資料集。
> * 建立、偵錯及執行管道以檢查變更的資料
> * 修改來源資料表中的資料
> * 完成、執行和監視完整的累加複製管道

## <a name="overview"></a>概觀
資料存放區 (例如 Azure SQL 受控執行個體 (MI) 和 SQL Server) 支援的異動資料擷取技術，可用於識別變更的資料。  本教學課程說明如何使用 Azure Data Factory 搭配 SQL 異動資料擷取技術，以累加方式將差異資料從 Azure SQL 受控執行個體載入 Azure Blob 儲存體。  如需有關 SQL 異動資料擷取技術更具的體資訊，請參閱 [SQL Server 中的異動資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)。

## <a name="end-to-end-workflow"></a>端對端工作流程
以下是使用異動資料擷取技術，以累加方式載入資料的一般端對端工作流程步驟。

> [!NOTE]
> Azure SQL MI 和 SQL Server 都支援異動資料擷取技術。 本教學課程使用 Azure SQL 受控執行個體做為來源資料存放區。 但您也可以使用內部部署的 SQL Server。

## <a name="high-level-solution"></a>高階解決方案
在本教學課程中，您會建立管道來執行下列作業：  

   1. 建立**查閱活動**以計算 SQL Database CDC 資料表中已變更記錄的數目，並將其傳遞給 IF 條件活動。
   2. 建立 **If 條件**以檢查是否有變更的記錄，如果有，則叫用複製活動。
   3. 建立**複製活動**，將 CDC 資料表之間所插入/更新/刪除的資料複製到 Azure Blob 儲存體。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件
* **Azure SQL Database 受控執行個體**。 您需要使用資料庫作為**來源**資料存放區。 如果您沒有 Azure SQL Database 受控執行個體，請參閱 [建立 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) 一文，以瞭解建立的步驟。
* **Azure 儲存體帳戶**。 您需要使用 Blob 儲存體作為**接收**資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-account-create.md)一文，按照步驟來建立帳戶。 建立名為 **raw** 的容器。 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>在 Azure SQL Database 中建立資料來源資料表

1. 啟動 **SQL Server Management Studio**，並連線到您的 Azure SQL 受控執行個體伺服器。
2. 在**伺服器總管**中，以滑鼠右鍵按一下您的**資料庫**，然後選擇 [新增查詢]。
3. 針對您的 Azure SQL 受控執行個體資料庫執行下列 SQL 命令，藉此建立名為 `customers` 的資料表做為資料來源存放區。  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. 藉由執行下列 SQL 查詢，在您的資料庫和來源資料表 (customers)上啟用**異動資料擷取**機制：

    > [!NOTE]
    > - 以具有 customers 資料表之 Azure SQL MI 的結構描述取代 &lt;您的來源結構描述名稱&gt;。
    > - 異動資料擷取不會在變更要追蹤之資料表的交易中執行任何動作。 而是會將插入、更新和刪除作業寫入交易記錄中。 如果您沒有定期且有系統地清除儲放在變更資料表中的資料，這項資料將無限制地成長。 如需詳細資訊，請參閱 [啟用資料庫的異動資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?enable-change-data-capture-for-a-database=&view=sql-server-ver15)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. 執行下列命令，將資料插入 customers 資料表：

    ```sql
     insert into customers 
        (customer_id, first_name, last_name, email, city) 
     values 
        (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
        (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > 在啟用異動資料擷取之前，不會先擷取資料表的任何歷程記錄變更。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
1. 在左側功能表上，選取 [建立資源] > [資料 + 分析] > [資料處理站]：

   ![在 [新增] 窗格中選取資料處理站](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站] 頁面中，輸入 **ADFTutorialDataFactory** 作為 [名稱]。

     ![新增資料處理站頁面](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您收到錯誤，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。

    *Data factory 名稱 "ADFTutorialDataFactory" 無法使用。*
3. 針對 [版本] 選取 [V2]。
4. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。
5. 針對 [資源群組]，請執行下列其中一個步驟︰

   1. 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。
   2. 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
    若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。  
5. 選取 Data Factory 的 [位置]  。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。
6. 取消選取 [啟用 GIT]。     
7. 按一下 [建立]。
8. 部署完成後，按一下 [移至資源]

   ![此螢幕擷取畫面顯示部署已完成的訊息，以及前往資源的選項。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。

   ![此螢幕擷取畫面顯示您已部署的資料處理站。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. 按一下 [撰寫與監視] 圖格，以在另一個索引標籤中啟動 Azure Data Factory 使用者介面 (UI)。
11. 在 [開始使用] 頁面中，切換至左面板中的 [編輯] 索引標籤，如下圖所示：

    ![建立管線按鈕](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>建立連結的服務
您在資料處理站中建立的連結服務會將您的資料存放區和計算服務連結到資料處理站。 在本節中，您會建立 Azure 儲存體帳戶和 Azure SQL MI 的連結服務。

### <a name="create-azure-storage-linked-service"></a>建立 Azure 儲存體連結服務。
在此步驟中，您會將您的 Azure 儲存體帳戶連結到 Data Factory。

1. 按一下 [連線]，然後按一下 [+ 新增]。

   ![新增連線按鈕](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. 在 [新增連結服務] 視窗中，選取 [Azure Blob 儲存體]，然後按一下 [繼續]。

   ![選取 Azure Blob 儲存體](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. 在 [新增連結服務] 視窗中，執行下列步驟：

   1. 輸入 **AzureStorageLinkedService** 作為 [名稱]。
   2. 為 [儲存體帳戶名稱] 選取 Azure 儲存體帳戶。
   3. 按一下 [檔案] 。

   ![Azure 儲存體帳戶設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>建立 Azure SQL MI Database 連結服務。
在此步驟中，您會將 Azure SQL MI Database 連結至資料處理站。

> [!NOTE]
> 對於 SQL MI 的使用者，請參閱[此處內容](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database-managed-instance#prerequisites)，瞭解透過公用與私人端點存取的相關資訊 如果是私人端點的使用者，則必須使用自我裝載整合執行階段來執行此管道。 這同樣適用於在 VM 或 VNet 使用案例中執行 SQL Server 內部部署的使用者。

1. 按一下 [連線]，然後按一下 [+ 新增]。
2. 在 [新增連結服務] 視窗中，選取 [Azure SQL Database 受控執行個體]，然後按一下 [繼續]。
3. 在 [新增連結服務] 視窗中，執行下列步驟：

   1. 在 [名稱] 欄位中，輸入 **AzureSqlMI1**。
   2. 在 [伺服器名稱] 欄位中選取您的 SQL server。
   4. 在 [資料庫名稱] 欄位中選取您的 SQL 資料庫。
   5. 在 [使用者名稱] 欄位輸入使用者的名稱。
   6. 在 [密碼] 欄位輸入使用者的密碼。
   7. 按一下 [測試連線] 以測試連線。
   8. 按一下 [儲存] 以儲存連結服務。

   ![Azure SQL MI Database 連結服務設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>建立資料集
在此步驟中，您會建立資料集來代表資料來源和資料目的地。

### <a name="create-a-dataset-to-represent-source-data"></a>建立資料集來代表來源資料
在此步驟中，您會建立資料集來代表來源資料。

1. 在樹狀檢視中，按一下 [+] (加號)，然後按一下 [資料集]。

   ![新增資料集功能表](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. 選取 **Azure SQL Database 受控執行個體**，然後按一下 [繼續]。

   ![來源資料集類型 - Azure SQL Database](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. 在 [設定屬性] 索引標籤中，設定資料集名稱和連線資訊：
 
   1. 針對**連結服務**選取 [AzureSqlMI1]。
   2. 選取 **[dbo].[dbo_customers_CT] **做為**資料表名稱**。  注意：當 [customers] 資料表上啟用 CDC 時，會自動建立此資料表。 變更的資料永遠不會直接從這個資料表查詢，而是透過 [CDC 函式](https://docs.microsoft.com/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql?view=sql-server-ver15) 進行解壓縮。

   ![來源連線](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>建立資料集來表示要複製到接收資料存放區的資料。
在此步驟中，您會建立資料集來代表從來源資料存放區複製的資料。 您已在 Azure Blob 儲存體中建立資料湖容器，做為必要條件的一部分。 建立容器 (若不存在)，或設為現有容器的名稱。 在此教學課程中，會使用觸發時間以動態方式產生輸出檔案名稱，稍後將會設定此觸發時間。

1. 在樹狀檢視中，按一下 [+] (加號)，然後按一下 [資料集]。

   ![新增資料集功能表](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. 選取 [Azure Blob 儲存體]，然後按一下 [繼續]。

   ![接收資料集類型：Azure Blob 儲存體](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. 選取 [DelimitedText]，然後按一下 [繼續]。

   ![接收資料集格式-DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. 在 [設定屬性] 索引標籤中，設定資料集名稱和連線資訊：

   1. 選取 [AzureStorageLinkedService] 作為 [連結服務]。
   2. 針對 **filePath** 的**容器**部分，輸入**原始資料**。
   3. 啟用**以第一個資料列做為標題**
   4. 按一下 [確定]。

   ![接收資料集 - 連線](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>建立管道以複製變更的資料
在此步驟中，您會建立管道，而且管道會先使用**查閱活動**，檢查變更資料表中變更的記錄數目。 IF 條件活動會檢查已變更記錄的數目是否大於零，並執行**複製活動**，將插入/更新/刪除的資料從 Azure SQL Database 複製到 Azure Blob 儲存體。 最後，會設定輪轉視窗觸發程序，而且開始和結束時間會傳遞至活動做為開始和結束視窗參數。 

1. 在 [Data Factory] 使用者介面中，切換至 [編輯] 索引標籤。按一下左窗格中的 [+] (加號)，然後按一下 [管線]。

    ![新增管線功能表](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. 您會看到用於設定管線的新索引標籤。 你也會在樹狀檢視中看到該管線。 在 [屬性] 視窗中，將管線的名稱變更為 **IncrementalCopyPipeline**。

    ![管線名稱](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. 在 [活動] 工具箱中展開 [一般]，並將 [查閱] 活動拖放至管線設計工具介面。 將活動的名稱設定為 **GetChangeCount**。 此活動會指定的時間範圍內取得變更資料表中的記錄數目。

    ![查閱活動 - 名稱](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. 切換至 [屬性] 視窗中的 [設定]：
   1. 針對 [來源資料集] 欄位，指定 SQL MI 資料集名稱。
   2. 選取 [查詢] 選項，並在查詢方塊中輸入下列內容：
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. 啟用 [僅第一個資料列]

    ![查閱活動 - 設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. 按一下 [預覽資料] 按鈕，確認查閱活動可以取得有效的輸出

    ![查閱活動 - 預覽](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. 在 [活動]  工具箱中展開 [反覆項目和條件式]，然後將 **If 條件**活動拖放至管道設計工具介面。 將活動的名稱設定為 **HasChangedRows**。 

    ![If 條件活動 - 名稱](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. 切換至 [屬性] 視窗中的 [活動]：

   1. 輸入下列**運算式**
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. 按一下鉛筆圖示以編輯 True 條件。

   ![If 條件活動 - 設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. 在 [活動] 工具箱中展開 [一般]，然後將 [等候] 活動拖放至管道設計工具介面。 這是一個暫時的活動，可用於偵錯 If 條件，稍後將在本教學課程中變更。 

   ![If 條件 True - 等候](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. 按一下 IncrementalCopyPipeline 階層連結以返回主要管道。

8. 在**偵錯**模式中執行管道以確認管道執行成功。 

   ![管道 - 偵錯](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. 接下來，返回 True 條件步驟，並刪除**等候**活動。 在 [活動] 工具箱中，展開 [移動與轉換]，然後將 [複製] 活動拖放至管道設計工具介面。 將活動的名稱設定為 **IncrementalCopyActivity**。 

   ![複製活動 - 名稱](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. 在 [屬性] 視窗中切換至 [來源] 索引標籤，並執行下列步驟：

   1. 針對 [來源資料集] 欄位，指定 SQL MI 資料集名稱。 
   2. 為 [使用查詢] 選取 [查詢]。
   3. 針對**查詢**輸入下列程式。

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![複製活動 - 來源設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. 按一下 [預覽] 以確認查詢正確地傳回變更後的資料列。

    ![此螢幕擷取畫面顯示驗證查詢的預覽。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. 切換至 [接收] 索引標籤，並針對 [接收資料集] 欄位指定 Azure 儲存體資料集。

    ![顯示 [接收] 索引標籤的螢幕擷取畫面。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. 按一下以回到主要管道畫布，然後將 [查閱] 活動逐一連線至 [If 條件] 活動。 將附加至 [查閱] 活動的 [綠色] 按鈕拖曳至 [If 條件] 活動。

    ![將查閱和複製活動連線](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. 按一下工具列上的 [驗證]。 確認沒有任何驗證錯誤。 按一下 **>>** 關閉 [管線驗證報告] 視窗。

    ![驗證按鈕](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. 按一下 [偵錯] 以測試管道，並確認已在儲存位置產生檔案。

    ![累加管道 debug-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. 按一下 [全部發佈] 按鈕，將實體 (連結的服務、資料集和管道) 發佈至 Data Factory 服務。 請靜待 [發佈成功] 訊息顯示。

    ![發佈按鈕](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>設定輪轉視窗觸發程序和 CDC 視窗參數 
在此步驟中，您會建立輪轉視窗觸發程序，以頻繁的排程執行作業。 您將使用輪轉視窗觸發程序的 WindowStart 和 WindowEnd 系統變數，並將它們當做參數傳遞至管道，以便用於 CDC 查詢中。

1. 瀏覽至 **IncrementalCopyPipeline** 管道的 [參數] 索引標籤，然後使用 [+ 新增] 按鈕，將兩個參數 (**triggerStartTime** 和 **triggerEndTime**) 新增至管道，這將代表輪轉視窗的開始和結束時間。 為了進行偵錯，請以 **YYYY-MM-DD HH24:MI:SS.FFF** 的格式來新增預設值，但請確認資料表上的 triggerStartTime 並未在 CDC 之前啟用，否則會導致錯誤發生。

    ![立即觸發功能表](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. 按一下 [查閱] 活動的 [設定] 索引標籤，並將查詢設定為使用開始和結束參數。 將下列內容複製到查詢中：
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. 在 **If 條件**活動為 True 的情況下，瀏覽至**複製**活動，然後按一下 [來源] 索引標籤。將下列內容複製到查詢中：
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. 按一下**複製**活動的 [接收] 索引標籤，然後按一下 [開啟] 來編輯資料集屬性。 按一下 [參數] 索引標籤，然後新增名為 **triggerStart** 的參數    

    ![此螢幕擷取畫面顯示將新參數新增至 [參數] 索引標籤的情形。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. 接下來，設定資料集屬性，將資料儲存在具有日期型分割區的 **customers/incremental** 子目錄中。
   1. 按一下資料集屬性的 [連線] 索引標籤，並針對 [目錄] 和 [檔案] 區段新增動態內容。 
   2. 按一下文字方塊下的動態內容連結，在 [目錄] 區段中輸入下列運算式：
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. 在 [檔案] 區段中輸入下列運算式。 這會根據觸發程序開始日期和時間來建立檔案名稱，並以 csv 附檔名做為尾碼：
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![接收資料集設定 - 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. 按一下 [IncrementalCopyPipeline] 索引標籤，在 [複製] 活動中，瀏覽返回至 [接收] 設定。 
   5. 展開資料集屬性，並在 triggerStart 參數值中輸入具有下列運算式的動態內容：
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![接收資料集設定 - 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. 按一下 [偵錯] 以測試管道，並確定已如預期的產生了資料夾結構和輸出檔案。 下載並開啟檔案以驗證內容。 

    ![累加式複製偵錯 - 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. 藉由檢閱管道執行的輸入參數，確保將參數插入查詢中。

    ![累加式複製偵錯 - 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. 按一下 [全部發佈] 按鈕，將實體 (連結的服務、資料集和管道) 發佈至 Data Factory 服務。 請靜待 [發佈成功] 訊息顯示。
9. 最後，設定輪轉視窗觸發程序以定期執行管道，並設定開始和結束時間參數。 
   1. 按一下 [新增觸發程序] 按鈕，然後選取 [新增/編輯]

   ![加入新的觸發程序](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. 輸入觸發程序名稱並指定開始時間，此時間就形同偵錯視窗的結束時間。

   ![輪轉視窗觸發程序](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. 在下一個畫面中，分別為 start 和 end 參數指定下列值。
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![輪轉視窗觸發程序 - 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> 請注意，觸發程序只會在發佈後執行。 此外，輪轉視窗的預期行為是從開始日期到目前為止，執行所有歷程記錄間隔。 如需有關輪轉視窗觸發程序的詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger)。 
  
10. 使用 **SQL Server Management Studio** 藉由執行下列 SQL，對 customer 資料表進行一些額外的變更：
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. 按一下 [全部發佈] 按鈕。 請靜待 [發佈成功] 訊息顯示。  
12. 數分鐘後，管道就會觸發，並將新的檔案載入 Azure 儲存體


### <a name="monitor-the-incremental-copy-pipeline"></a>監視累加複製管線
1. 按一下左側的 [監視] 索引標籤。 您會在清單中看到管線執行和其狀態。 若要重新整理清單，按一下 [重新整理]。 將滑鼠停留在管道名稱附近，即可存取重新執行動作和使用情況報告。

    ![管線執行](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. 若要檢視與管道執行相關聯的活動執行情況，請按一下管道名稱。 如果偵測到變更的資料，則會有三個活動 (包括複製活動)，否則清單中只會有兩個項目。 若要切換回管道執行情況檢視，請按一下頂端的 [所有管道] 連結。

    ![活動執行](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>檢閱結果
您會在 `raw` 容器的 `customers/incremental/YYYY/MM/DD` 資料夾中看到第二個檔案。

![累加複製的輸出檔案](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>後續步驟
進入下列教學課程，深入了解如何只根據其 LastModifiedDate 複製全新和變更檔案：

> [!div class="nextstepaction"]
>[依 lastmodifieddate 複製新檔案](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
