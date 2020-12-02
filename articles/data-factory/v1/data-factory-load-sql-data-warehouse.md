---
title: 將數 tb 的資料載入 Azure Synapse Analytics
description: 示範如何使用 Azure Data Factory 在15分鐘內將 1 TB 的資料載入 Azure Synapse Analytics
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 68c9e594201f0d0689a289e13f2c4ebf909c2f87
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457096"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>使用 Data Factory 在15分鐘內將 1 TB 載入 Azure Synapse Analytics
> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱 [使用 Data Factory，將資料複製到 Azure Synapse Analytics 或從中複製資料](../connector-azure-sql-data-warehouse.md)。


[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 是以雲端為基礎的向外延展資料庫，能夠處理大量資料，包括關聯式和非關聯式。  Azure Synapse Analytics 已針對企業資料倉儲工作負載優化，以大規模平行處理 (MPP) 架構為基礎。  它透過單獨調整儲存體和計算的彈性，來提供雲端彈性。

開始使用 Azure Synapse Analytics 現在比以往使用 **Azure Data Factory** 更容易。  Azure Data Factory 是完全受控的雲端架構資料整合服務，可用來將現有系統的資料填入 Azure Synapse Analytics，並在評估 Azure Synapse Analytics 和建立分析解決方案時省下寶貴的時間。 以下是使用 Azure Data Factory 將資料載入 Azure Synapse Analytics 的主要優點：

* **容易設定**：不需要編寫指令碼的 5 步驟直覺式精靈。
* **豐富的資料存放區支援**：適用于一組豐富內部部署和雲端式資料存放區的內建支援。
* **安全並符合標準**：資料是透過 HTTPS 或 ExpressRoute 傳輸，並且具有全域服務可確保資料絕不會離開地理界限
* **使用 polybase 的無與倫比效能** –使用 polybase 是將資料移至 Azure Synapse Analytics 的最有效方式。 您可以使用預備 Blob 功能來達到所有類型之資料存放區 (除了 Azure Blob 儲存體之外) 的高載入速度，而 Polybase 預設支援這項作業。

本文說明如何使用 Data Factory 複製嚮導，在15分鐘內將 1 TB 的資料從 Azure Blob 儲存體載入至 Azure Synapse Analytics，以超過 1.2 GBps 的輸送量。

本文提供逐步指示，說明如何使用「複製嚮導」將資料移至 Azure Synapse Analytics。

> [!NOTE]
>  如需有關將資料移入/移出 Azure Synapse Analytics Data Factory 功能的一般資訊，請參閱 [使用 Azure Data Factory 文章將資料移入和移出 Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) 。
>
> 您也可以使用 Visual Studio、PowerShell 等來建立管線。請參閱 [教學課程：將資料從 Azure Blob 複製到 Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 以取得快速逐步解說，其中包含在 Azure Data Factory 中使用複製活動的逐步指示。  
>
>

## <a name="prerequisites"></a>必要條件
* Azure Blob 儲存體︰這項實驗使用 Azure Blob 儲存體 (GRS) 來儲存 TPC-H 測試資料集。  如果您沒有 Azure 儲存體帳戶，請參閱[如何建立儲存體帳戶](../../storage/common/storage-account-create.md)。
* [TPC-H](http://www.tpc.org/tpch/) 資料︰我們將使用 TPC-H 作為測試資料集。  若要這麼做，您必須使用 TPC-H 工具組中的 `dbgen`，以協助您產生資料集。  您可以從 [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) 下載 `dbgen` 的原始程式碼並自行進行編譯，或者從 [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools) 下載編譯過的二進位檔。  使用下列命令執行 dbgen.exe，以針對分散到 10 個檔案的 `lineitem` 資料表產生 1 TB 一般檔案：

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    現在，將產生的檔案複製到 Azure Blob。  請參閱[使用 Azure Data Factory 將資料移進和移出內部部署檔案系統](data-factory-onprem-file-system-connector.md)，以了解如何使用 ADF 複製執行這樣作業。    
* Azure Synapse Analytics：此實驗會將資料載入至使用6000建立的 Azure Synapse Analytics Dwu

    如需如何建立 Azure Synapse Analytics 資料庫的詳細指示，請參閱 [建立 Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) 。  若要使用 Polybase 取得 Azure Synapse Analytics 的最佳載入效能，我們會選擇效能設定中允許的資料倉儲單位數目上限 (Dwu) ，也就是 6000 Dwu。

  > [!NOTE]
  > 從 Azure Blob 載入時，資料載入效能會與您為 Azure Synapse Analytics 設定的 Dwu 數目成正比：
  >
  > 將 1 TB 載入至 1000 DWU Azure Synapse Analytics 需要87分鐘 (~ 200 MBps 輸送量) 將 1 TB 載入至 2000 DWU Azure Synapse Analytics 需要46分鐘 (~ 380 MBps 輸送量) 將 1 TB 載入至 6000 DWU Azure Synapse Analytics 花費14分鐘 (~ 1.2 GBps 輸送量) 
  >
  >

    若要建立具有 6000 Dwu 的專用 SQL 集區，請將效能滑杆向右移動：

    ![效能滑桿](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    針對未設定 6,000 個 DWU 的現有資料庫，您可以使用 Azure 入口網站相應進行增加。  瀏覽至 Azure 入口網站中的資料庫，而且下圖所顯示的 [概觀] 面板中會有 [調整] 按鈕：

    ![[調整] 按鈕](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    按一下 [調整] 按鈕開啟下列面板，並將滑桿移到最大值，然後按一下 [儲存] 按鈕。

    ![[調整] 對話方塊](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    此實驗會使用資源類別將資料載入 Azure Synapse Analytics `xlargerc` 。

    若要達到最佳的可能輸送量，必須使用屬於資源類別的 Azure Synapse Analytics 使用者來執行複製 `xlargerc` 。  遵循[變更使用者資源類別範例](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)，了解如何執行這項作業。  
* 藉由執行下列 DDL 語句，在 Azure Synapse Analytics 資料庫中建立目的地資料表架構：

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  完成必要的步驟之後，即準備好使用複製精靈設定複製活動。

## <a name="launch-copy-wizard"></a>啟動複製精靈
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左上角的 [建立資源]，按一下 [智慧 + 分析]，然後按一下 [Data Factory]。
3. 在 [新增 Data Factory] 窗格中：

   1. 輸入 **LoadIntoSQLDWDataFactory** 作為 [名稱]。
       Azure Data Factory 的名稱在全域必須是唯一的。 如果您收到錯誤： **data factory 名稱 "LoadIntoSQLDWDataFactory" 無法使用**，請變更 data factory 的名稱 (例如 yournameLoadIntoSQLDWDataFactory) ，然後再試一次建立。 請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md) 主題，以了解 Data Factory 成品的命名規則。  
   2. 選取您的 Azure **訂用帳戶**。
   3. 針對資源群組，請執行下列其中一個步驟︰
      1. 選取 [使用現有的] 以選取現有的資源群組。
      2. 選取 [建立新的] 以輸入資源群組的名稱。
   4. 選取 Data Factory 的 [位置]。
   5. 選取刀鋒視窗底部的 [釘選到儀表板] 核取方塊。  
   6. 按一下 [建立]。
4. 建立完成之後，您會看到 [Data Factory] 刀鋒視窗，如下圖所示：

   ![Data Factory 首頁](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. 在 Data Factory 首頁，按一下 [資料複製] 圖格以啟動 [複製精靈]。

   > [!NOTE]
   > 如果您看到網頁瀏覽器停留在「授權中...」，請停用/取消核取 [封鎖第三方 Cookie 和站台資料] 設定 (或) 將它保持啟用並為 **login.microsoftonline.com** 建立例外狀況，然後再次嘗試啟動精靈。
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>步驟 1︰設定資料載入排程
第一個步驟是設定資料載入排程。  

在 [屬性]  頁面︰

1. 輸入 **CopyFromBlobToAzureSqlDataWarehouse** 作為 [工作名稱]
2. 選取 [立即執行一次] 選項。   
3. 按 [下一步]  。  

    ![複製精靈 - 屬性頁面](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>步驟 2：設定來源
本節示範來源設定步驟︰包含 1 TB TPC-H 明細行項目檔案的 Azure Blob。

1. 選取 [Azure Blob 儲存體] 作為資料存放區，然後按 [下一步]。

    ![複製精靈 - 選取來源頁面](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. 填寫 Azure Blob 儲存體帳戶的連接資訊，然後按 [下一步]。

    ![複製精靈 - 來源連接資訊](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. 選擇包含 TPC-H 明細行項目檔案的 **資料夾**，然後按 [下一步]。

    ![複製精靈 - 選取輸入資料夾](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. 按 [下一步]，即會自動偵測檔案格式設定。  請檢查以確定資料行分隔符號是 ' | '，而不是預設的逗號 '，'。  在預覽資料之後，請按 [下一步]。

    ![複製精靈 - 檔案格式設定](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>步驟 3︰設定目的地
本節說明如何 `lineitem` 在 Azure Synapse Analytics 資料庫中設定目的地：資料表。

1. 選擇 [ **Azure Synapse Analytics** ] 作為目的地存放區，然後按 **[下一步]**。

    ![複製精靈 - 選取目的地資料存放區](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. 填入 Azure Synapse Analytics 的連接資訊。  請務必指定本身為 `xlargerc` 角色成員的使用者 (如需詳細指示，請參閱 **必要條件** 一節)，然後按 [下一步]。

    ![複製精靈 - 目的地連接資訊](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. 選擇目的地資料表，然後按 [下一步]。

    ![複製精靈 - 資料表對應頁面](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. 在 [結構描述對應] 頁面中，將 [套用資料行對應] 選項保持不選取，然後按 [下一步]。

## <a name="step-4-performance-settings"></a>步驟 4：效能設定

預設會核取 [允許 Polybase]。  按 [下一步]  。

![複製精靈 - 結構描述對應頁面](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>步驟 5︰部署和監視載入結果
1. 按一下 [完成] 按鈕進行部署。

    ![複製嚮導-摘要頁面1](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. 部署完成之後，請按一下 `Click here to monitor copy pipeline` 監視複製執行進度。 選取您在 [活動時段] 清單中建立的複製管線。

    ![複製嚮導-摘要頁面2](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    您可以在右窗格的 [活動時段總管] 中檢視複製執行詳細資料，包括從來源讀取及寫入至目的地的資料量、期間，以及執行的平均輸送量。

    如您在下列螢幕擷取畫面中所見，從 Azure Blob 儲存體複製 1 TB 到 Azure Synapse Analytics 花了14分鐘的時間，有效達到 1.22 GBps 的輸送量！

    ![複製精靈 - 成功對話方塊](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>最佳作法
以下是執行 Azure Synapse Analytics 資料庫的一些最佳作法：

* 載入至 CLUSTERED COLUMNSTORE INDEX 時，請使用較大的資源類別。
* 對於更有效率的聯結，請考慮透過選取資料行使用雜湊散發，而不是預設循環配置資源散發。
* 若要更快的載入速度，請考慮使用暫時性資料的堆積。
* 在您完成載入至 Azure Synapse Analytics 之後，建立統計資料。

如需詳細資訊，請參閱 [Azure Synapse Analytics 的最佳做法](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) 。

## <a name="next-steps"></a>後續步驟
* [Data Factory 複製精靈](data-factory-copy-wizard.md) - 本文提供複製精靈的詳細資料。
* [複製活動效能和微調指南](data-factory-copy-activity-performance.md) - 本文包含參考效能測量和微調指南。