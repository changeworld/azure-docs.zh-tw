---
title: 教學課程：載入紐約計程車資料
description: 教學課程使用 Azure 入口網站和 SQL Server Management Studio 從 Azure blob for Synapse SQL 載入紐約計程車資料。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cb5984ba5d5764ee2ffa3f28e2d95612c14f7e27
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93025930"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>教學課程：載入紐約計程車資料集

本教學課程會使用 [COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ，從 Azure blob 儲存體帳戶載入紐約計程車資料集。 本教學課程是使用 [Azure 入口網站](https://portal.azure.com)和 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS)：

> [!div class="checklist"]
>
> * 在 Azure 入口網站中建立 SQL 集區
> * 在 Azure 入口網站中設定伺服器層級的防火牆規則
> * 使用 SSMS 連線到資料倉儲
> * 建立針對載入資料指定的使用者
> * 建立範例資料集的資料表 
> * 使用 COPY T-sql 語句將資料載入資料倉儲
> * 在載入時，檢閱資料的進度

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>開始之前

開始本教學課程之前，請下載並安裝最新版的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS)。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-blank-database"></a>建立空白資料庫

SQL 集區會使用一組已定義的[計算資源](memory-concurrency-limits.md)來建立。 此資料庫建立於 [Azure 資源群組](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)和[邏輯 SQL 伺服器](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)內。

遵循以下步驟來建立空白資料庫。

1. 選取 Azure 入口網站左上角的 [建立資源]  。

2. 從 [ **新增** ] 頁面中選取 [ **資料庫** ]，然後選取 [ **新增** ] 頁面上的 [ **精選** ] **Azure Synapse Analytics** 。

    ![螢幕擷取畫面顯示 Azure 入口網站中的資料庫所選取的 SQL 資料倉儲。](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. 在表單中填寫以下資訊：

   | 設定            | 建議的值       | 描述                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *名稱**            | mySampleDataWarehouse | 如需有效的資料庫名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 |
   | **訂用帳戶**   | 您的訂用帳戶     | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組** | myResourceGroup       | 如需有效的資源群組名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 |
   | **選取來源**  | 空白資料庫        | 指定以建立空白資料庫。 請注意，資料倉儲是一種資料庫。 |

    ![螢幕擷取畫面顯示 [SQL 資料倉儲] 窗格，您可以在其中輸入這些值。](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. 選取 [伺服器]  ，為您的新資料庫建立及設定新伺服器。 在 **新伺服器表單** 表單中填寫下列資訊︰

    | 設定                | 建議的值          | 描述                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **伺服器名稱**        | 任何全域唯一名稱 | 如需有效的伺服器名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 |
    | **伺服器管理員登入** | 任何有效名稱           | 如需有效的登入名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 |
    | **密碼**           | 任何有效密碼       | 您的密碼至少要有 8 個字元，而且必須包含下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
    | **位置**           | 任何有效位置       | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |

    ![建立伺服器](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. 選取 [選取]  。

6. 選取 [ **效能等級** ] 以指定資料倉儲是 Gen1 或 Gen2，以及資料倉儲單位的數目。

7. 在本教學課程中，請選取 [SQL 集區 **Gen2** ]。 滑杆預設會設定為 **DW1000c** 。  請嘗試向上和向下移動以查看其運作方式。

    ![設定效能](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. 選取 [套用]  。
9. 在 [布建 **] 分頁** 中，為空白資料庫選取定序。 本教學課程使用預設值。 如需定序的詳細資訊，請參閱[定序](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

10. 現在您已經完成表單，請選取 [建立] 以布 **建** 資料庫。 佈建需要幾分鐘的時間。

11. 在工具列上選取 [通知]，以監視部署程序。
  
     ![螢幕擷取畫面顯示 [通知] 窗格已開啟且正在進行部署的 Azure 入口網站。](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>建立伺服器層級防火牆規則

伺服器層級的防火牆，可防止外部應用程式和工具連接到伺服器或伺服器上的任何資料庫。 若要啟用連線，您可以新增防火牆規則以啟用特定 IP 位址之連線。  遵循以下步驟建立用戶端 IP 位址的[伺服器層級防火牆規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

> [!NOTE]
> Azure Synapse Analytics 透過埠1433進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 1433，否則您無法連線到您的伺服器。

1. 部署完成之後，請從左側功能表中選取 **[sql** database]，然後選取 [ **sql 資料庫** ] 頁面上的 [ **mySampleDatabase** ]。 資料庫的概觀頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver-20180430.database.windows.net** )，並提供進一步的設定選項。

2. 在後續的快速入門中，請複製此完整伺服器名稱，才能用來連線到伺服器及其資料庫。 然後選取伺服器名稱以開啟 [伺服器設定]。

    ![尋找伺服器名稱](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. 選取伺服器名稱以開啟 [伺服器設定]。

    ![伺服器設定](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. 選取 [顯示防火牆設定]。 伺服器的 [防火牆設定] 頁面會隨即開啟。

    ![伺服器防火牆規則](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. 選取工具列上的 [新增用戶端 IP]，以將目前的 IP 位址新增至新的防火牆規則。 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

6. 選取 [儲存]。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則，在伺服器上開啟連接埠 1433。

7. 選取 **[確定]** ，然後關閉 [ **防火牆設定** ] 頁面。

您現在可以使用此 IP 位址連線到伺服器及其資料倉儲。 可從 SQL Server Management Studio 或您選擇的另一個工具來運作連線。 當您連線時，請使用先前建立的 ServerAdmin 帳戶。  

> [!IMPORTANT]
> 根據預設，已對所有 Azure 服務啟用透過 SQL Database 防火牆存取。 選取此頁面上的 [ **關閉** ]，然後選取 [ **儲存** ] 以停用所有 Azure 服務的防火牆。

## <a name="get-the-fully-qualified-server-name"></a>取得完整的伺服器名稱

請在 Azure 入口網站中取得伺服器的完整伺服器名稱。 稍後您在連線到伺服器時，要使用完整伺服器名稱。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從左側功能表中選取 **Azure Synapse Analytics** ，然後在 **Azure Synapse Analytics** 頁面上選取您的資料庫。
3. 在 Azure 入口網站中您資料庫的 [基本資訊] 窗格中，找到後複製 [伺服器名稱]。 在此範例中，完整名稱是 mynewserver 20180430.database.windows.net。

    ![連線資訊](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>以伺服器系統管理員身分連線到伺服器

本節使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) 建立與伺服器的連線。

1. 開啟 SQL Server Management Studio。

2. 在 [連線至伺服器] 對話方塊中，輸入下列資訊：

    | 設定        | 建議的值                            | 描述                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | 伺服器類型    | 資料庫引擎                            | 這是必要值                                       |
    | 伺服器名稱    | 完整伺服器名稱            | 名稱應該類似這樣︰ **mynewserver-20180430.database.windows.net** 。 |
    | 驗證 | SQL Server 驗證                  | 在本教學課程中，我們只設定了 SQL 驗證這個驗證類型。 |
    | 登入          | 伺服器系統管理員帳戶                   | 這是您在建立伺服器時指定的帳戶。 |
    | 密碼       | 伺服器系統管理員帳戶的密碼 | 這是您在建立伺服器時指定的密碼。 |

    ![連線至伺服器](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. 選取 [連接]  。 [物件總管] 視窗會在 SSMS 中開啟。

4. 在 [物件總管] 中展開 [資料庫]。 然後展開 [系統資料庫]  和 [主要資料庫]  來檢視主要資料庫中的物件。  展開 [mySampleDatabase]  可檢視新資料庫中的物件。

    ![資料庫物件](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>建立載入資料的使用者

伺服器系統管理員帳戶旨在執行管理作業，並不適合用於在使用者資料上執行查詢。 載入資料是需要大量記憶體的作業。 記憶體最大可根據設定的 [資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md) 和 [資源類別](resource-classes-for-workload-management.md) 來定義。

您最好建立載入資料專用的登入和使用者。 然後將載入使用者新增至可進行適當最大記憶體配置的[資源類別](resource-classes-for-workload-management.md)。

因為您目前以伺服器管理員的身分連線，就可以建立登入和使用者。 使用下列步驟來建立登入和名為 **LoaderRC20** 的使用者。 然後將使用者指派至 **staticrc20** 資源類別。

1. 在 SSMS 中，以滑鼠右鍵選取 [ **master** ] 以顯示下拉式功能表，然後選擇 [追加 **查詢** ]。 隨即開啟 [新增查詢] 視窗。

    ![主要資料庫上的新增查詢](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. 在查詢視窗中，輸入這些 T-SQL 命令來建立登入和名為 LoaderRC20 的使用者，以取代您自己的 'a123STRONGpassword!' 密碼。

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. 選取 [執行]。

4. 以滑鼠右鍵按一下 [mySampleDataWarehouse]  ，然後選擇 [新增查詢]  。 新的查詢視窗隨即開啟。  

    ![範例資料倉儲上的新查詢](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. 輸入下列 T-SQL 命令，針對 LoaderRC20 登入建立名為 LoaderRC20 的資料庫使用者。 第二行會在新的資料倉儲上授與新的使用者控制權限。  這些權限類似於讓使用者成為資料庫的擁有者。 第三行會將新的使用者新增為 staticrc20 [資源類別](resource-classes-for-workload-management.md)的成員。

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. 選取 [執行]。

## <a name="connect-to-the-server-as-the-loading-user"></a>以載入使用者身分連線到伺服器

載入資料的首要步驟是以 LoaderRC20 身分登入。  

1. 在物件總管中，選取 [連線] 下拉式功能表，然後選取 [ **資料庫引擎** **]** 。 [連線到伺服器]  對話方塊隨即出現。

    ![與新登入連線](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. 輸入完整伺服器名稱，以及輸入 **LoaderRC20** 作為登入。  輸入您 LoaderRC20 的密碼。

3. 選取 [連接]  。

4. 您的連線就緒時，會在 [物件總管] 中看到兩個伺服器連線。 一個是以 ServerAdmin 連線，另一個是以 MedRCLogin 連線。

    ![連線成功](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>建立範例資料的資料表

您已準備好開始將資料載入新資料倉儲的程序。 本教學課程的這個部分會說明如何使用 COPY 語句，從 Azure 儲存體 blob 載入紐約市計程車 cab 資料集。 若要瞭解如何將資料移至 Azure blob 儲存體，或直接從來源載入資料，請參閱 [載入總覽](design-elt-data-loading.md)。

執行下列 SQL 腳本，並指定您要載入之資料的相關資訊。 這項資訊包括資料所在位置、資料內容的格式，以及資料的資料表定義。

1. 在上一節中，您以 LoaderRC20 身分登入您的資料倉儲。 在 SSMS 中，以滑鼠右鍵按一下 [LoaderRC20] 連線，然後選取 [新增查詢]  。  新的查詢視窗隨即開啟。

    ![新的載入查詢視窗](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. 比較您的查詢視窗與上一個影像。  請確認您的 [新增查詢] 視窗是以 LoaderRC20 身分執行，並在 MySampleDataWarehouse 資料庫上執行查詢。 您可以使用這個查詢視窗來執行所有的載入步驟。

7. 執行下列 T-sql 語句來建立資料表：

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>將資料載入資料倉儲

本節使用 [COPY 語句，](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 從 Azure 儲存體 Blob 載入範例資料。  

> [!NOTE]
> 本教學課程會將資料直接載入最終資料表。 您通常會載入生產工作負載的臨時表。 當資料位於暫存資料表時，您可以執行任何必要的轉換。 

1. 執行下列語句以載入資料：

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. 檢視載入中的資料。 您正在載入數 Gb 的資料，並將其壓縮成高效能的叢集資料行存放區索引。 執行下列會使用動態管理檢視 (DMV) 來顯示載入狀態的查詢。

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. 檢視所有系統查詢。

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. 輕鬆看著資料順利載入資料倉儲中。

    ![檢視載入的資料表](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>清除資源

您需要支付計算資源和您載入資料倉儲之資料的費用。 這些會分開計費。

* 如果您需要將資料保留在儲存體中，可以在您不使用資料倉儲時暫停計算。 暫停計算時，您只需支付資料儲存體的費用，並在您準備好要使用資料時，隨時繼續計算。
* 如果您需要移除未來的費用，可以將資料倉儲刪除。

遵循下列步驟，視需要清除資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取您的資料倉儲。

    ![清除資源](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 若要暫停計算，請選取 [暫停] 按鈕。 資料倉儲暫停時，您會看到 [啟動]  按鈕。  若要繼續計算，請選取 [啟動]  。

3. 若要移除資料倉儲，而不需要支付計算或儲存體的費用，請選取 [ **刪除** ]。

4. 若要移除您所建立的伺服器，請選取上一個映射中的 [ **mynewserver-20180430.database.windows.net** ]，然後選取 [ **刪除** ]。  請謹慎使用這個，因為刪除伺服器會將所有指派給伺服器的資料庫刪除。

5. 若要移除此資源群組，請選取 [myResourceGroup]，然後選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立資料倉儲，以及建立載入資料的使用者。 您已使用簡單的 [COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) 將資料載入資料倉儲。

您進行了下列事項：
> [!div class="checklist"]
>
> * 在 Azure 入口網站中建立資料倉儲
> * 在 Azure 入口網站中設定伺服器層級的防火牆規則
> * 使用 SSMS 連線到資料倉儲
> * 建立針對載入資料指定的使用者
> * 建立範例資料的資料表
> * 使用 COPY T-sql 語句將資料載入資料倉儲
> * 在載入時，已檢閱資料的進度

前進到開發總覽，以瞭解如何將現有的資料庫移轉至 Azure Synapse Analytics：

> [!div class="nextstepaction"]
> [將現有的資料庫移轉至 Azure Synapse Analytics 的設計決策](sql-data-warehouse-overview-develop.md)

如需更多載入範例和參考，請參閱下列檔：

- [COPY 語句參考檔](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [複製每個驗證方法的範例](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [複製單一資料表的快速入門](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
