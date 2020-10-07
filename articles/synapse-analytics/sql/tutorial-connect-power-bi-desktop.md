---
title: 教學課程：將 SQL 隨選連線至 Power BI Desktop 並建立報告
description: 在本教學課程中，您將了解如何將 Azure Synapse Analytics 中的 SQL 隨選連線至 Power BI Desktop，並根據檢視建立示範報告。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5c86825d6dce8681e114ec930add751b6beae085
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539549"
---
# <a name="tutorial-use-sql-on-demand-with-power-bi-desktop--create-a-report"></a>教學課程：使用 SQL 隨選搭配 Power BI Desktop 並建立報告

在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> - 建立示範資料庫
> - 建立用於報告的檢視
> - 將 Power BI Desktop 連線到 SQL 隨選
> - 根據檢視來建立報告

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- [Power BI Desktop](https://powerbi.microsoft.com/downloads/) - 將資料視覺化並建立報表所需的項目。
- [Azure Synapse 工作區](https://docs.microsoft.com/azure/synapse-analytics/quickstart-synapse-studio) - 建立資料庫、外部資料來源與檢視所需的項目。

選擇性：

- SQL 查詢工具，例如 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 或 [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)。

下列參數的值：

| 參數                                 | 描述                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 隨選服務端點位址    | 作為伺服器名稱                                   |
| SQL 隨選服務端點區域     | 用來判斷範例中使用的儲存體 |
| 用來存取端點的使用者名稱和密碼 | 用來存取端點                               |
| 您將用來建立檢視的資料庫     | 範例中用來作為起點的資料庫       |

## <a name="1---create-database"></a>1 - 建立資料庫

請針對示範環境建立您自己的示範資料庫。 您會使用此資料庫來檢視中繼資料，而非儲存實際資料。

藉由執行下列 Transact-SQL (T-SQL) 指令碼來建立示範資料庫 (並視需要卸除現有資料庫)：

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 - 建立資料來源

需要有資料來源才能讓 SQL 隨選服務存取儲存體中的檔案。 為與您端點位於相同區域的儲存體帳戶建立資料來源。 雖然 SQL 隨選可存取來自不同區域的儲存體帳戶，但讓儲存體和端點位於相同區域將會提供較佳的效能。

執行下列 Transact-SQL (T-SQL) 指令碼來建立資料來源：

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3 - 準備檢視

藉由執行下列 Transact-SQL (T-SQL) 指令碼來根據外部示範資料建立檢視以供 Power BI 取用：

使用下列查詢在資料庫 `Demo` 內建立檢視 `usPopulationView`：

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

示範資料包含下列資料集：

美國各縣依性別和種族分類的人口統計，資料來源是 2000 年和 2010 年的十年人口普查，並採用 Parquet 格式。

| 資料夾路徑                                                  | 描述                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | 資料在示範儲存體帳戶中的父資料夾               |
| /release/us_population_county/                               | 使用 Hive/Hadoop 資料分割配置，依年份來分割的 Parquet 格式美國人口資料檔案。 |

## <a name="4---create-power-bi-report"></a>4 - 建立 Power BI 報告

使用下列步驟建立 Power BI Desktop 的報告：

1. 開啟 Power BI Desktop 應用程式，然後選取 [取得資料]。

   ![開啟 Power BI Desktop 應用程式並選取 [取得資料]。](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. 選取 [Azure] > [Azure SQL Database]。 

   ![選取資料來源。](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. 在 [伺服器] 欄位中，輸入資料庫所在伺服器的名稱，然後在資料庫名稱中輸入 `Demo`。 選取 [匯入] 選項，然後選取 [確定]。 

   ![選取端點上的資料庫。](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. 選取慣用的驗證方法：

    - AAD 的範例 
  
        ![按一下 [登入]。](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - SQL 登入的範例 - 輸入您的使用者名稱和密碼。

        ![使用 SQL 登入。](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. 選取 `usPopulationView` 檢視，然後選取 [載入]。 

   ![在所選資料庫上選取一個檢視。](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. 等候作業完成，然後會出現快顯視窗指出 `There are pending changes in your queries that haven't been applied`。 選取 [套用變更]。 

   ![按一下 [套用變更]。](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. 等待 [套用查詢變更] 對話方塊消失，這可能需要幾分鐘的時間。 

   ![等待查詢完成。](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. 載入完成後，請依此順序選取下列資料行以建立報告：
   - countyName
   - 母體擴展
   - stateName

   ![選取您想要的資料行，以產生地圖報表。](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>清除資源

當您使用完這份報告之後，請使用下列步驟來刪除資源：

1. 刪除儲存體帳戶的認證

   ```sql
   DROP EXTENAL DATA SOURCE AzureOpenData
   ```

2. 刪除檢視

   ```sql
   DROP VIEW usPopulationView;
   ```

3. 卸除資料庫

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>後續步驟

前進到[查詢儲存體檔案](develop-storage-files-overview.md)以了解如何使用 Synapse SQL 來查詢儲存體檔案。
