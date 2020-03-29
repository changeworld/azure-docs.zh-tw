---
title: 在 Azure 流分析作業中使用 SQL 資料庫參考資料
description: 本文說明如何在 Azure 入口網站和 Visual Studio 中使用 SQL Database 作為 Azure 串流分析作業的參考資料輸入。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: aebb590d93b3fb26151f15c176a2941845cdd50c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426507"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>使用 SQL 資料庫中的參考資料執行 Azure 流分析作業

Azure 串流分析支援以 Azure SQL Database 作為參考資料輸入的來源。 在 Azure 入口網站和 Visual Studio 中，您可以搭配串流分析工具使用 SQL Database 作為串流分析作業的參考資料。 本文會示範這兩種方法的執行方式。

## <a name="azure-portal"></a>Azure 入口網站

使用下列步驟來使用 Azure 入口網站將 Azure SQL Database 新增為參考輸入來源：

### <a name="portal-prerequisites"></a>入口網站必要條件

1. 建立串流分析作業。

2. 建立儲存體帳戶以供串流分析作業使用。

3. 建立具有資料集的 Azure SQL Database，以作為串流分析作業的參考資料。

### <a name="define-sql-database-reference-data-input"></a>定義 SQL Database 參考資料輸入

1. 在您的串流資料作業中，選取 [工作拓樸]**** 底下的 [輸入]****。 按一下 [新增參考輸入]**** 並選擇 [SQL Database]****。

   ![串流分析工作輸入](./media/sql-reference-data/stream-analytics-inputs.png)

2. 填寫串流分析輸入設定。 選擇資料庫名稱、伺服器名稱、使用者名稱和密碼。 如果您想要讓參考資料輸入定期重新整理，請選擇 [開啟] 來以 DD:HH:MM 格式指定重新整理頻率。 如果您具有重新整理頻率較短的大型資料集，則可以使用[差異查詢](sql-reference-data.md#delta-query)。

   ![SQL Database 參考設定](./media/sql-reference-data/sql-input-config.png)

3. 在 SQL 查詢編輯器中測試快照集查詢。 如需詳細資訊，請參閱[使用 Azure 入口網站的 SQL 查詢編輯器進行連線並查詢資料](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>在作業設定中指定儲存體帳戶

瀏覽到 [設定]**** 底下的 [儲存體帳戶設定]****，然後選取 [新增儲存體帳戶]****。

   ![串流分析 [儲存體帳戶設定]](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>啟動工作

在您已設定其他輸入、輸出及查詢之後，便可以啟動串流分析作業。

## <a name="tools-for-visual-studio"></a>Visual Studio 適用的工具

使用下列步驟來使用 Visual Studio 將 Azure SQL Database 新增為參考輸入來源：

### <a name="visual-studio-prerequisites"></a>Visual Studio 必要條件

1. [安裝適用於 Visual Studio 的串流分析工具](stream-analytics-tools-for-visual-studio-install.md)。 支援下列其中一個 Visual Studio 版本：

   * Visual Studio 2015
   * Visual Studio 2019

2. 熟悉[適用於 Visual Studio 的串流分析工具](stream-analytics-quick-create-vs.md)快速指南。

3. 建立儲存體帳戶。

### <a name="create-a-sql-database-table"></a>建立 SQL Database 資料表

使用 SQL Server Management Studio 來建立資料表以儲存您的參考資料。 請參閱[使用 SSMS 設計您的第一個 Azure SQL 資料庫](../sql-database/sql-database-design-first-database.md)以取得詳細資料。

用於下列範例中的範例資料表是建立自下列陳述式：

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>選擇您的訂用帳戶

1. 在 Visual Studio 的 [檢視]**** 功能表上，選取 [伺服器總管]****。

2. 以滑鼠右鍵按一下 [Azure]****，選取 [連線到 Microsoft Azure 訂用帳戶]****，然後以您的 Azure 帳戶登入。

### <a name="create-a-stream-analytics-project"></a>建立串流分析專案

1. 選取 [檔案] > [新增專案]****。 

2. 在左側的範本清單中，選取 [串流分析]****，然後選取 [Azure 串流分析應用程式]****。 

3. 輸入專案的 [名稱]****、[位置]**** 和 [解決方案名稱]****，然後選取 [確定]****。

   ![Visual Studio 中的新串流分析專案](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>定義 SQL Database 參考資料輸入

1. 建立新輸入。

   ![Visual Studio 中的新串流分析輸入](./media/sql-reference-data/stream-analytics-vs-input.png)

2. 按兩下 [方案總管]**** 中的 [Input.json]****。

3. 填寫 [串流分析輸入設定]****。 選擇資料庫名稱、伺服器名稱、重新整理類型，以及重新整理頻率。 以 `DD:HH:MM` 的格式指定重新整理頻率。

   ![Visual Studio 中的串流分析輸入設定](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   如果您選擇 [僅執行一次] 或 [定期執行]，系統會在 [Input.json]**** 檔案節點底下的專案中產生名為 **[Input Alias].snapshot.sql** 的 SQL 程式碼後置檔案。

   ![Visual Studio 中的輸入程式碼後置](./media/sql-reference-data/once-or-periodically-codebehind.png)

   如果您選擇 [搭配差異定期重新整理]，系統會產生兩個 SQL 程式碼後置檔案：**[Input Alias].snapshot.sql** 和 **[Input Alias].delta.sql**。

   ![方案總管中的程式碼後置](./media/sql-reference-data/periodically-delta-codebehind.png)

4. 在編輯器中開啟該 SQL 檔案，並寫入 SQL 查詢。

5. 如果您使用的是 Visual Studio 2019，並且已安裝 SQL Server 資料工具，則可以通過按一下 **"執行"** 來測試查詢。 精靈視窗將會顯示以協助您連線到 SQL 資料庫，查詢結果將會出現在底部視窗中。

### <a name="specify-storage-account"></a>指定儲存體帳戶

開啟 **JobConfig.json** 以指定儲存 SQL 參考快照集的儲存體帳戶。

   ![Visual Studio 中的串流分析作業設定](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>於本機測試並部署到 Azure

在將作業部署到 Azure 之前，您可以在本機針對即時輸入資料測試查詢邏輯。 如需此功能的詳細資料，請參閱[使用適用於 Visual Studio 的 Azure 串流分析工具 (預覽) 在本機測試即時資料](stream-analytics-live-data-local-testing.md)。 完成測試之後，請按一下 [提交至 Azure]****。 請參考[使用適用於 Visual Studio 的 Azure 串流分析工具建立串流分析作業](stream-analytics-quick-create-vs.md)快速入門來了解如何啟動作業。

## <a name="delta-query"></a>差異查詢

使用差異查詢時，建議使用 [Azure SQL Database 中的時態表](../sql-database/sql-database-temporal-tables.md)。

1. 在 Azure SQL 資料庫中創建臨時表。
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. 撰寫快照集查詢。 

   使用**\@snapshotTime**參數指示流分析運行時從 SQL 資料庫臨時表獲取在系統時間有效的參考資料集。 如果您不提供此參數，則可能會因時鐘誤差而取得不正確的基底參考資料集。 完整快照集查詢的範例如下所示：
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. 撰寫差異查詢。 
   
   此查詢檢索 SQL 資料庫中在開始時間、**\@增量開始時間和**結束時間**\@delta EndTime**中插入或刪除的所有行。 差異查詢必須傳回和快照集查詢相同的資料行，以集 **_operation_** 資料行。 此列定義在**\@增量 StartTime**和**\@delta EndTime**之間插入或刪除行。 如果記錄已插入，結果的資料列會被標示為 **1**；如果已刪除，則會被標示為 **2**。 

   針對已更新的記錄，時態表會透過擷取插入和刪除作業來進行記錄。 串流分析執行階段接著便會將差異查詢的結果套用到先前的快照集，以將參考資料保持為最新狀態。 差異查詢的範例如下所示：

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   請注意，除了差異查詢之外，串流分析執行階段可能會定期執行快照集查詢以儲存檢查點。

## <a name="test-your-query"></a>測試查詢
   請務必驗證查詢是否返回流分析作業將用作參考資料的預期資料集。 要測試查詢，請轉到門戶上的作業拓撲下輸入。 然後，您可以在 SQL 資料庫參考輸入上選擇示例資料。 示例可用後，您可以下載該檔並檢查正在返回的資料是否按預期。 如果要優化開發和測試反覆運算，建議使用[視覺化工作室的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)。 您還可以使用任何其他首選工具，首先確保查詢從 Azure SQL 資料庫返回正確的結果，然後在流分析作業中使用它。 

## <a name="faqs"></a>常見問題集

**在 Azure 串流分析中使用 SQL 參考資料輸入是否會產生額外成本？**

串流分析作業不會有額外的[串流單元成本](https://azure.microsoft.com/pricing/details/stream-analytics/)。 不過，串流分析作業必須要有關聯的 Azure 儲存體帳戶。 串流分析作業會查詢 SQL DB (在作業啟動和重新整理間隔期間) 以擷取參考資料集，並將該快照集儲存在儲存體帳戶中。 儲存這些快照集將會產生額外費用，其已詳述於 Azure 儲存體帳戶的[定價頁面](https://azure.microsoft.com/pricing/details/storage/)。

**如何確認已從 SQL DB 查詢資料快照集，並已將它用於 Azure 串流分析作業？**

有兩個指標按邏輯名稱（在指標 Azure 門戶下）進行篩選，可用於監視 SQL 資料庫參考資料輸入的運行狀況。

   * 輸入事件：此指標衡量從 SQL 資料庫參考資料集載入的記錄數。
   * InputEvent 位元組：此指標測量在流分析作業的記憶體中載入的參考資料快照的大小。 

結合這兩個計量，便可以推斷作業是否正在查詢 SQL 資料庫以擷取參考資料集，並將它載入記憶體。

**我是否需要特別類型的 Azure SQL Database？**

Azure 串流分析可搭配任何類型的 Azure SQL Database 運作。 不過，請務必了解針對參考資料輸入所設定的重新整理頻率，可能會影響查詢負載。 若要使用差異查詢選項，建議使用 Azure SQL Database 中的時態表。

**為何 Azure 串流分析會將快照集儲存在 Azure 儲存體帳戶？**

串流分析可保證僅只一次的事件處理，以及至少一次的事件傳遞。 在作業被暫時性問題影響的情況下，會需要少量的重新執行以還原狀態。 若要啟用重新執行，這些快照集必須儲存在 Azure 儲存體帳戶中。 如需檢查點重新執行的詳細資訊，請參閱 [Azure 串流分析作業中的檢查點和重新執行概念](stream-analytics-concepts-checkpoint-replay.md)。

## <a name="next-steps"></a>後續步驟

* [使用參考資料在串流分析中進行查閱](stream-analytics-use-reference-data.md)
* [快速入門：使用適用於 Visual Studio 的 Azure 串流分析工具建立串流分析作業](stream-analytics-quick-create-vs.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具 (預覽) 在本機測試即時資料](stream-analytics-live-data-local-testing.md)
