---
title: 使用 VSTS (先前的 SQL DW) 連線到專用的 SQL 集區
description: 使用 Visual Studio 在 Azure Synapse Analytics 中查詢專用的 SQL 集區 (先前的 SQL DW) 。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2b81ddedbcb254a840e85d41cf9d69c78b149bbd
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121391"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>使用 Visual Studio 和 SSDT 連接到 Azure Synapse Analytics 中的專用 SQL 集區 (先前的 SQL DW) 

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

在短短幾分鐘內，使用 Visual Studio 來查詢 Azure Synapse 中 (先前 SQL DW) 的專用 SQL 集區。 這個方法會在 Visual Studio 2019 中使用 SQL Server Data Tools (SSDT) 擴充功能。 

## <a name="prerequisites"></a>Prerequisites
若要使用本教學課程，您需要：

* 現有的專用 SQL 集區 (先前為 SQL DW) 。 若要建立一個，請參閱 [ (先前的 SQL DW) 建立專用的 sql 集 ](create-data-warehouse-portal.md)區。
* 適用於 Visual Studio 的 SSDT。 如果您有 Visual Studio，可能已經有 Visual Studio 的 SSDT。 如需安裝指示和選項，請參閱 [安裝 Visual Studio 和 SSDT](sql-data-warehouse-install-visual-studio.md)。
* 完整的 SQL 伺服器名稱。 若要尋找此資訊，請參閱 [ (先前的 SQL DW) 連接到專用的 sql 集 ](sql-data-warehouse-connect-overview.md)區。

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. 連接到您先前的 SQL DW (專用的 SQL 集區) 
1. 開啟 Visual Studio 2019。
2. 選取 [ **View** SQL Server 物件總管] 來開啟 SQL Server 物件總管  >  ****。
   
    ![SQL Server 物件總管](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. 按一下 [加入 SQL Server]  圖示。
   
    ![加入 SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. 填寫 [連線到伺服器] 視窗中的欄位。
   
    ![連線到伺服器](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **伺服器名稱**。 輸入先前找到的 **伺服器名稱** 。
   * **驗證**。 選取 [SQL Server 驗證] 或 [Active Directory 整合式驗證]。
   * **使用者名稱** 和 **密碼**。 如果上面已選取 [SQL Server 驗證]，請輸入使用者名稱和密碼。
   * 按一下 [ **連接**]。
5. 若要瀏覽，請展開您的 Azure SQL 伺服器。 您可以檢視與伺服器相關聯的資料庫。 展開 AdventureWorksDW 以查看範例資料庫中的資料表。
   
    ![探索 AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2.執行範例查詢
現已建立對您的資料庫的連線，接著繼續撰寫查詢。

1. 在 [SQL Server 物件總管] 中您的資料庫上按一下滑鼠右鍵。
2. 選取 [新增查詢]。 隨即開啟 [新增查詢] 視窗。
   
    ![新增查詢](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. 將下列 T-SQL 查詢複製到查詢視窗：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 按一下綠色箭頭或執行下列捷徑以執行查詢：`CTRL`+`SHIFT`+`E`。
   
    ![執行查詢](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. 查看查詢結果。 在此範例中，FactInternetSales 資料表有 60398 個資料列。
   
    ![查詢結果](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>後續步驟
您現在可以連線並查詢，請嘗試[使用 Power BI 將資料視覺化](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)。

若要為 Azure Active Directory 驗證設定您的環境，請參閱 [先前的 SQL DW) 驗證專用的 sql 集區 (](sql-data-warehouse-authentication.md)。