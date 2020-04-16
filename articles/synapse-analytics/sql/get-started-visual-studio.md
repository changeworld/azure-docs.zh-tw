---
title: 使用視覺化工作室與 SSDT 連線到並查詢 Synapse SQL
description: 使用視覺化工作室使用 Azure 同步分析查詢 SQL 池。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428559"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>透過視覺化工作室和 SSDT 連接到 Synapse SQL
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

使用視覺化工作室使用 Azure 同步分析查詢 SQL 池。 此方法在 Visual Studio 2019 中使用 SQL 伺服器數據工具 (SSDT) 擴展。 

### <a name="supported-tools-for-sql-on-demand-preview"></a>支援 SQL 以支援 SQL

當前不支援 SQL 按需(預覽)的可視化工作室。 但是,Azure 數據 Studi(預覽)o 是一個完全支援的工具。 SQL 伺服器管理工作室從版本 18.4 中得到部分支援,並且具有有限的功能,如連接和查詢。

## <a name="prerequisites"></a>Prerequisites
要使用本教學,您需要具有以下元件:

* 現有的 SQL 池。 如果沒有 SQL 池,請參閱[創建 SQL 池](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以完成此先決條件。
* 適用於 Visual Studio 的 SSDT。 如果您有 Visual Studio,則可能已有此元件。 如需安裝指示和選項，請參閱 [安裝 Visual Studio 和 SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 完整的 SQL 伺服器名稱。 要尋找此內容,請參考[連接到 SQL 池](connect-overview.md)。

## <a name="1-connect-to-sql-pool"></a>1. 連接到 SQL 池
1. 開啟 Visual Studio 2019。
2. 打開 SQL 伺服器物件資源管理員。 為此,請選擇 **「查看** > **SQL 伺服器物件資源管理員**」。
   
    ![SQL Server 物件總管](./media/get-started-visual-studio/open-ssdt.png)
3. 按一下 [加入 SQL Server] **** 圖示。
   
    ![加入 SQL Server](./media/get-started-visual-studio/add-server.png)
4. 填寫 [連線到伺服器] 視窗中的欄位。
   
    ![連線到伺服器](./media/get-started-visual-studio/connection-dialog.png)
   
   * **伺服器名稱**:輸入以前識別的**伺服器名稱**。
   * **認證**:選擇**SQL 伺服器認證**或**活動目錄整合身份驗證**:
   * **使用者名稱**和**密碼**:如果上面選擇了 SQL Server 身份驗證,請輸入使用者名和密碼。
   * 按一下 [ **連接**]。
5. 若要瀏覽，請展開您的 Azure SQL 伺服器。 您可以檢視與伺服器相關聯的資料庫。 展開 AdventureWorksDW 以查看範例資料庫中的資料表。
   
    ![探索 AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. 執行範例查詢
現在,已建立與資料庫的連接,您將編寫查詢。

1. 在 [SQL Server 物件總管] 中您的資料庫上按一下滑鼠右鍵。
2. 選取 [新增查詢]****。 隨即開啟 [新增查詢] 視窗。
   
    ![新增查詢](./media/get-started-visual-studio/new-query2.png)
3. 將以下 T-SQL 查詢複製到查詢視窗:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 執行查詢。 若要這麼做，請按一下綠色箭頭，或使用下列快速鍵： `CTRL`+`SHIFT`+`E`。
   
    ![執行查詢](./media/get-started-visual-studio/run-query.png)
5. 查看查詢結果。 在此範例中，FactInternetSales 資料表有 60398 個資料列。
   
    ![查詢結果](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>後續步驟
現在,您可以連接和查詢,請嘗試[使用 Power BI 視覺化資料](get-started-power-bi-professional.md)。
要為 Azure 的目錄認證設定環境,請參閱[對 SQL 池進行身份驗證](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
 