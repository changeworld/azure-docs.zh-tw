---
title: 與 VSTS 連線
description: 使用可視化工作室查詢 Azure 同步分析。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 174ee07e389e598fed6ed8487e60303fbce81f77
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416040"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>透過視覺化工作室與 SSDT 連接到 Azure 同步分析
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用 Visual Studio 只需幾分鐘即可查詢 Azure 突觸中的 SQL 池。 此方法在 Visual Studio 2019 中使用 SQL 伺服器數據工具 (SSDT) 擴展。 

## <a name="prerequisites"></a>Prerequisites
若要使用本教學課程，您需要：

* 現有的 SQL 池。 要建立一個,請參閱[建立 SQL 池](create-data-warehouse-portal.md)。
* 適用於 Visual Studio 的 SSDT。 如果您有視覺工作室,您可能已經擁有用於視覺工作室的 SSDT。 如需安裝指示和選項，請參閱 [安裝 Visual Studio 和 SSDT](sql-data-warehouse-install-visual-studio.md)。
* 完整的 SQL 伺服器名稱。 要尋找此資訊,請參考到[SQL 池](../sql/connect-overview.md)。

## <a name="1-connect-to-your-sql-pool"></a>1. 連接到 SQL 池
1. 開啟 Visual Studio 2019。
2. 以選擇**檢視** > **SQL Server 物件資源管理員開啟 SQL Server 物件資源管理員**。
   
    ![SQL Server 物件總管](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. 按一下 [加入 SQL Server] **** 圖示。
   
    ![加入 SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. 填寫 [連線到伺服器] 視窗中的欄位。
   
    ![連線到伺服器](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **伺服器名稱**. 輸入先前找到的 **伺服器名稱** 。
   * **驗證**： 選取 [SQL Server 驗證]**** 或 [Active Directory 整合式驗證]****。
   * **使用者名稱**與**密碼**。 如果上面已選取 [SQL Server 驗證]，請輸入使用者名稱和密碼。
   * 按一下 [ **連接**]。
5. 若要瀏覽，請展開您的 Azure SQL 伺服器。 您可以檢視與伺服器相關聯的資料庫。 展開 AdventureWorksDW 以查看範例資料庫中的資料表。
   
    ![探索 AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. 執行範例查詢
現已建立對您的資料庫的連線，接著繼續撰寫查詢。

1. 在 [SQL Server 物件總管] 中您的資料庫上按一下滑鼠右鍵。
2. 選取 [新增查詢]****。 隨即開啟 [新增查詢] 視窗。
   
    ![新增查詢](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. 將以下 T-SQL 查詢複製到查詢視窗:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 點選綠色箭頭或使用以下捷徑執行查詢: `CTRL` + `SHIFT` + `E`。
   
    ![執行查詢](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. 查看查詢結果。 在此範例中，FactInternetSales 資料表有 60398 個資料列。
   
    ![查詢結果](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>後續步驟
現在,您可以連接和查詢,請嘗試[使用 Power BI 視覺化資料](sql-data-warehouse-get-started-visualize-with-power-bi.md)。

要為 Azure 的目錄認證設定環境,請參閱[對 SQL 池進行身份驗證](sql-data-warehouse-authentication.md)。
