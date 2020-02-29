---
title: 連線到 VSTS
description: 使用 Visual Studio 查詢 Azure Synapse 分析。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 88dc534b8753311e49cafa9f84705258cdb0883d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198613"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>使用 Visual Studio 和 SSDT 連接到 Azure Synapse 分析
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

在短短幾分鐘內，使用 Visual Studio 來查詢 Azure Synapse 中的 SQL 集區。 這個方法會使用 Visual Studio 2019 中的 SQL Server Data Tools （SSDT）延伸模組。 

## <a name="prerequisites"></a>必要條件
若要使用本教學課程，您需要：

* 現有的 SQL 集區。 若要建立一個，請參閱[建立 SQL 集](sql-data-warehouse-get-started-provision.md)區。
* 適用於 Visual Studio 的 SSDT。 如果您有 Visual Studio，您可能已經有 Visual Studio 的 SSDT。 如需安裝指示和選項，請參閱 [安裝 Visual Studio 和 SSDT](sql-data-warehouse-install-visual-studio.md)。
* 完整的 SQL 伺服器名稱。 若要尋找此資訊，請參閱[連接到 SQL 集](sql-data-warehouse-connect-overview.md)區。

## <a name="1-connect-to-your-sql-pool"></a>1. 連接到您的 SQL 集區
1. 開啟 Visual Studio 2019。
2. 選取 [ **View** > **SQL Server 物件總管**] 來開啟 SQL Server 物件總管。
   
    ![SQL Server 物件總管](media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. 按一下 [加入 SQL Server] 圖示。
   
    ![加入 SQL Server](media/sql-data-warehouse-query-visual-studio/add-server.png)
4. 填寫 [連線到伺服器] 視窗中的欄位。
   
    ![連線到伺服器](media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **伺服器名稱**。 輸入先前找到的 **伺服器名稱** 。
   * **驗證**。 選取 [SQL Server 驗證] 或 [Active Directory 整合式驗證]。
   * [使用者名稱] 和 [密碼]。 如果上面已選取 [SQL Server 驗證]，請輸入使用者名稱和密碼。
   * 按一下 [連接]。
5. 若要瀏覽，請展開您的 Azure SQL 伺服器。 您可以檢視與伺服器相關聯的資料庫。 展開 AdventureWorksDW 以查看範例資料庫中的資料表。
   
    ![探索 AdventureWorksDW](media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. 執行範例查詢
現已建立對您的資料庫的連線，接著繼續撰寫查詢。

1. 在 [SQL Server 物件總管] 中您的資料庫上按一下滑鼠右鍵。
2. 選取 [新增查詢]。 隨即開啟 [新增查詢] 視窗。
   
    ![新增查詢](media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. 將下列 T-SQL 查詢複製到查詢視窗中：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 按一下綠色箭號或使用下列快捷方式來執行查詢： `CTRL`+`SHIFT`+`E`。
   
    ![執行查詢](media/sql-data-warehouse-query-visual-studio/run-query.png)
5. 查看查詢結果。 在此範例中，FactInternetSales 資料表有 60398 個資料列。
   
    ![查詢結果](media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>後續步驟
現在您可以進行連線和查詢，請嘗試[使用 Power BI 將資料視覺化](sql-data-warehouse-get-started-visualize-with-power-bi.md)。

若要設定您的環境以進行 Azure Active Directory 驗證，請參閱[向 SQL 集區進行驗證](sql-data-warehouse-authentication.md)。