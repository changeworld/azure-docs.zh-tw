---
title: 使用 SSMS 連線
description: 使用 SQL 伺服器管理工作室 (SSMS) 連接到並查詢 Azure 突觸分析。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743857"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>使用 SQL 伺服器管理工作室 (SSMS) 連線到 Azure 同步分析

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 機器學習](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

使用 SQL 伺服器管理工作室 (SSMS) 連接到和查詢 Azure 突觸中的數據倉庫。

## <a name="prerequisites"></a>Prerequisites

若要使用本教學課程，您需要：

* 現有的 SQL 池。 要建立一個,請參閱[建立 SQL 池](create-data-warehouse-portal.md)。
* SQL Server Management Studio (SSMS) 已安裝。 如果您還沒有,請免費[下載SSMS。](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* 完整的 SQL 伺服器名稱。 要尋找此資訊,請參考到[SQL 池](sql-data-warehouse-connect-overview.md)。

## <a name="1-connect-to-your-sql-pool"></a>1. 連接到 SQL 池

1. 開啟 SSMS。
2. 以選擇**檔案** > **連線物件資源管理員**開啟物件資源管理員。

    ![SQL Server 物件總管](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. 填寫 [連線到伺服器] 視窗中的欄位。

   ![連線到伺服器](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **伺服器名稱**. 輸入先前找到的 **伺服器名稱** 。
   * **驗證**： 選取 [SQL Server 驗證]**** 或 [Active Directory 整合式驗證]****。
   * **使用者名稱**與**密碼**。 如果上面已選取 [SQL Server 驗證]，請輸入使用者名稱和密碼。
   * 按一下 [ **連接**]。
4. 若要瀏覽，請展開您的 Azure SQL 伺服器。 您可以檢視與伺服器相關聯的資料庫。 展開 AdventureWorksDW 以查看範例資料庫中的資料表。

   ![探索 AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. 執行範例查詢

現已建立對您的資料庫的連線，接著繼續撰寫查詢。

1. 在 [SQL Server 物件總管] 中您的資料庫上按一下滑鼠右鍵。
2. 選取 [新增查詢]****。 隨即開啟 [新增查詢] 視窗。

   ![新增查詢](./media/sql-data-warehouse-query-ssms/new-query.png)
3. 將以下 T-SQL 查詢複製到查詢視窗:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. 點選`Execute`或使用以下捷徑執行查詢: `F5`

   ![執行查詢](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. 查看查詢結果。 在此範例中，FactInternetSales 資料表有 60398 個資料列。

   ![查詢結果](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>後續步驟

現在,您可以連接和查詢,請嘗試[使用 Power BI 視覺化資料](sql-data-warehouse-get-started-visualize-with-power-bi.md)。 要為 Azure 的目錄認證設定環境,請參閱[對 SQL 池進行身份驗證](sql-data-warehouse-authentication.md)。
