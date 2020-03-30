---
title: 教程：如何將 SQLite 資料庫移轉到 Azure SQL 資料庫無伺服器
description: 瞭解如何使用 Azure 資料工廠執行從 SQLite 到 Azure SQL 資料庫無伺服器的離線遷移。
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780505"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>如何將 SQLite 資料庫移轉到無伺服器 Azure SQL 資料庫
對於許多人，SQLite 提供了他們第一次體驗資料庫和 SQL 程式設計。 它包含在許多作業系統和流行的應用程式中，使 SQLite 成為世界上部署和使用最廣泛的資料庫引擎之一。 而且，由於它很可能是許多人使用的第一個資料庫引擎，它通常最終可能是專案或應用程式的核心部分。 在這種情況下，如果專案或應用程式增長超過初始 SQLite 實現，開發人員可能需要將其資料移轉到可靠、集中的資料存儲。

Azure SQL 資料庫無伺服器是單個資料庫的計算層，該資料庫根據工作負載需求自動縮放計算，並記錄每秒使用的計算量。 無伺服器計算層還會在僅計費存儲的非活動期間自動暫停資料庫，並在活動返回時自動復原資料庫。

執行以下步驟後，資料庫將遷移到 Azure SQL 資料庫無伺服器狀態，從而使您能夠使資料庫可供雲中的其他使用者或應用程式使用，並且只需支付使用的內容，只需最少的應用程式代碼更改即可。

## <a name="prerequisites"></a>Prerequisites
- Azure 訂閱
- 要遷移的 SQLite2 或 SQLite3 資料庫
- Windows 環境
  - 如果沒有本地 Windows 環境，則可以使用 Azure 中的 Windows VM 進行遷移。 使用 Azure 檔和存儲資源管理器移動並使 SQLite 資料庫檔案在 VM 上可用。

## <a name="steps"></a>步驟

1. 在無伺服器計算層中預配新的 Azure SQL 資料庫。

    ![Azure 門戶的螢幕截圖，顯示 Azure sql 資料庫無伺服器的預配示例](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. 確保您的 SQLite 資料庫檔案在 Windows 環境中可用。 如果您還沒有 SQLite ODBC 驅動程式（例如，http://www.ch-werner.de/sqliteodbc/)開源中有許多可用驅動程式）。

3. 為資料庫創建系統 DSN。 確保使用與系統體系結構匹配的資料來源管理員應用程式（32 位與 64 位）。 您可以在系統設置中找到正在運行的版本。

    - 環境中的開放 ODBC 資料來源管理員。
    - 按一下系統 DSN 選項卡，然後按一下"添加"
    - 選擇您安裝的 SQLite ODBC 連接器，並為連接指定一個有意義的名稱，例如 sqlite 遷移源
    - 將資料庫名稱設置為 .db 檔
    - 儲存並結束

4. 下載並安裝自託管的集成運行時。 最簡單的方法是快速安裝選項，如文檔中詳細說明。 如果選擇手動安裝，則需要向應用程式提供身份驗證金鑰，該金鑰可以位於資料工廠實例中，通過：

    - 啟動 ADF（從 Azure 門戶中的服務創建和監視器）
    - 按一下左側的"作者"選項卡（藍色鉛筆）
    - 按一下連接（左下角），然後按一下集成運行時
    - 添加新的自託管集成運行時，給它一個名稱，選擇選項*2*。

5. 為數據工廠中的源 SQLite 資料庫創建新的連結服務。

    ![顯示 Azure 資料工廠中空連結服務邊欄選項卡的螢幕截圖](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. 在"連接"中，在"連結服務"下，按一下"新建"

7. 搜索並選擇"ODBC"連接器


    ![在 Azure 資料工廠的連結服務邊欄選項卡中顯示 ODBC 連接器徽標的螢幕截圖](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. 為連結的服務指定一個有意義的名稱，例如"sqlite_odbc"。 從"通過集成運行時連接"下拉清單中選擇集成運行時。 將以下內容輸入連接字串，將初始目錄變數替換為 .db 檔的檔路徑，將 DSN 替換為系統 DSN 連接的名稱： 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 將身份驗證類型設置為匿名

10. 測試連線

    ![顯示 Azure 資料工廠中成功連接的螢幕截圖](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. 為無伺服器 SQL 目標創建另一個連結服務。 使用連結的服務嚮導選擇資料庫，並提供 SQL 身份驗證憑據。

    ![顯示 Azure 資料工廠中選擇的 Azure SQL 資料庫的螢幕截圖](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. 從 SQLite 資料庫中提取創建表語句。 可以通過在資料庫檔案上執行下面的 Python 腳本來執行此操作。

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. 通過在 Azure 門戶中的查詢編輯器中複製 CreateTables.sql 檔中的 CREATE 表語句，在無伺服器 SQL 目標環境中創建著陸表。

14. 返回到資料工廠的主畫面，然後按一下"複製資料"以運行作業創建嚮導。

    ![顯示 Azure 資料工廠中的複製資料嚮導徽標的螢幕截圖](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. 使用核取方塊從源 SQLite 資料庫中選擇所有表，並將它們映射到 Azure SQL 中的目標表。 作業運行後，您已成功將資料從 SQLite 遷移到 Azure SQL！

## <a name="next-steps"></a>後續步驟

- 要開始，請參閱[快速入門：使用 Azure 門戶在 Azure SQL 資料庫中創建單個資料庫](sql-database-single-database-get-started.md)。
- 如需資源限制，請參閱[無伺服器計算層級資源限制](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。
