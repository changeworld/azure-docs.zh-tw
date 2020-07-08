---
title: 使用 Visual Studio 和 SSDT 連線至 Synapse SQL 並進行查詢
description: 使用 Visual Studio 以利用 Azure Synapse Analytics 查詢 SQL 集區。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3f98a9d6dd79ba1da1a3be1e9e187ed4e8eca8ee
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962981"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>使用 Visual Studio 和 SSDT 連線至 Synapse SQL
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

使用 Visual Studio 以利用 Azure Synapse Analytics 查詢 SQL 集區。 這個方法會在 Visual Studio 2019 中使用 SQL Server Data Tools (SSDT) 擴充功能。 

> [!NOTE]
> SSDT 不支援 SQL 隨選 (預覽)。

## <a name="prerequisites"></a>Prerequisites
若要使用這個教學課程，您需要具備以下元件：

* 現有的 SQL 集區。 如果您沒有該集區，請參閱[建立 SQL 集區](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)，以完成此必要條件。
* 適用於 Visual Studio 的 SSDT。 如果您有 Visual Studio，您可能已經有此元件。 如需安裝指示和選項，請參閱 [安裝 Visual Studio 和 SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 完整的 SQL 伺服器名稱。 若要找到此伺服器名稱，請參閱[連線至 SQL 集區](connect-overview.md)。

## <a name="1-connect-to-sql-pool"></a>1.連線至 SQL 集區
1. 開啟 Visual Studio 2019。
2. 選取 [檢視] > [SQL Server 物件總管]，以開啟 SQL Server 物件總管。
   
    ![SQL Server 物件總管](./media/get-started-visual-studio/open-ssdt.png)
3. 按一下 [加入 SQL Server]  圖示。
   
    ![加入 SQL Server](./media/get-started-visual-studio/add-server.png)
4. 填寫 [連線到伺服器] 視窗中的欄位。
   
    ![連線到伺服器](./media/get-started-visual-studio/connection-dialog.png)
   
   * **伺服器名稱**：輸入先前找到的 **伺服器名稱** 。
   * **驗證**：選取 [SQL Server 驗證] 或 [Active Directory 整合式驗證]：
   * **使用者名稱**和**密碼**：如果上面已選取 [SQL Server 驗證]，請輸入使用者名稱和密碼。
   * 按一下 [ **連接**]。
5. 若要瀏覽，請展開您的 Azure SQL 伺服器。 您可以檢視與伺服器相關聯的資料庫。 展開 AdventureWorksDW 以查看範例資料庫中的資料表。
   
    ![探索 AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2.執行範例查詢
現已對您的資料庫建立連線，接下來您可以撰寫查詢。

1. 在 [SQL Server 物件總管] 中您的資料庫上按一下滑鼠右鍵。
2. 選取 [新增查詢]。 隨即開啟 [新增查詢] 視窗。
   
    ![新增查詢](./media/get-started-visual-studio/new-query2.png)
3. 將下列 T-SQL 查詢複製到查詢視窗：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 按一下綠色箭頭或執行下列捷徑以執行查詢：`CTRL`+`SHIFT`+`E`。
   
    ![執行查詢](./media/get-started-visual-studio/run-query.png)
5. 查看查詢結果。 在此範例中，FactInternetSales 資料表有 60398 個資料列。
   
    ![查詢結果](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>後續步驟
您現在可以連線並查詢，請嘗試[使用 Power BI 將資料視覺化](get-started-power-bi-professional.md)。
若要針對 Azure Active Directory 驗證設定您的環境，請參閱[驗證 SQL 集區](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
 