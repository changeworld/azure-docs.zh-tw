---
title: 教學課程：如何將 SQLite 資料庫遷移至 Azure SQL Database 無伺服器
description: 了解如何使用 Azure Data Factory，執行從 SQLite 至 Azure SQL Database 無伺服器的離線移轉。
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: 6e60403344a0341b4aee74a001287c09ba67e114
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448908"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>如何將 SQLite 資料庫遷移至 Azure SQL Database 無伺服器
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

對於許多人而言，SQLite 為他們提供了第一次的資料庫和 SQL 程式設計體驗。 其包含在許多作業系統中，而熱門應用程式讓 SQLite 成為世界上最廣泛部署和使用的資料庫引擎。 而且，由於這可能是許多人使用的第一個資料庫引擎，因此通常會成為專案或應用程式的中心部分。 當專案或應用程式成長到超過初始 SQLite 實作時，開發人員可能需要將其資料遷移至可靠且集中的資料存放區。

Azure SQL Database 無伺服器是適用於單一資料庫的計算層，可根據工作負載需求自動擴縮計算，並以每秒使用的計算量來計費。 無伺服器計算層也會在只有儲存體仍在計費的資料庫非使用中期間自動暫停資料庫，並在有活動傳回時自動繼續執行資料庫。

遵循下列步驟之後，您的資料庫就會遷移至 Azure SQL Database 無伺服器，讓您可以將資料庫提供給雲端中的其他使用者或應用程式使用，並只為您所用的部分付費，您只需變更最少的應用程式程式碼就能辦到了。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶
- 您想要遷移的 SQLite2 或 SQLite3 資料庫
- Windows 環境
  - 如果您沒有本機 Windows 環境，您可以使用 Azure 中的 Windows VM 進行移轉。 使用 Azure 檔案儲存體和儲存體總管，讓 SQLite 資料庫檔案能在 VM 上移動及使用。

## <a name="steps"></a>步驟

1. 在無伺服器計算層中佈建新的 Azure SQL Database。

    ![顯示 Azure SQL Database 無伺服器佈建範例的 Azure 入口網站螢幕擷取畫面](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. 確定您的 Windows 環境中有 SQLite 資料庫檔案可供使用。 如果您還沒有 SQLite ODBC 驅動程式，請先進行安裝 (有許多開放原始碼版本可供使用，例如： http://www.ch-werner.de/sqliteodbc/) )。

3. 建立資料庫的系統 DSN。 請確定您使用的資料來源管理員應用程式符合您的系統架構 (32 位元與 64 位元)。 您可以在系統設定中找到您正在執行的版本。

    - 在您的環境中開啟 [ODBC 資料來源管理員]。
    - 按一下 [系統 DSN] 索引標籤，然後按一下 [新增]
    - 選取您已安裝的 SQLite ODBC 連接器，並為該連線提供有意義的名稱，例如 sqlitemigrationsource
    - 將資料庫名稱設定為 .db 檔案
    - 儲存並結束

4. 下載並安裝自我裝載整合執行階段。 若要這麼做，最簡單的方式就是使用 [快速安裝] 選項，如文件中所述。 如果您選擇手動安裝，您將需要使用驗證金鑰來提供應用程式，您可以在 Data Factory 執行個體中找到金鑰，方法是：

    - 啟動 ADF (從 Azure 入口網站中服務的 [撰寫和監視])
    - 按一下左側的 [撰寫] 索引標籤 (藍色鉛筆)
    - 依序按一下 [連線] (左下角) 和 [整合執行階段]
    - 加入新的自我裝載整合執行階段並為其命名，然後選取 [選項 2]。

5. 在您的 Data Factory 中為來源 SQLite 資料庫建立新的連結服務。

    ![顯示 Azure Data Factory 中有空白連結服務分頁的螢幕擷取畫面](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. 在 [連結服務] 底下的 [連線] 中，按一下 [新增]。

7. 搜尋並選取 "ODBC" 連接器

   ![顯示 Azure Data Factory [連結服務] 分頁中有 [ODBC 連接器] 標誌的螢幕擷取畫面](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. 為已連結的服務提供有意義的名稱，例如 "sqlite_odbc"。 從 [透過整合執行階段連線] 下拉式清單中選取您的整合執行階段。 在連接字串中輸入下列內容，將初始目錄變數取代為 .db 檔案的檔案路徑，並將 DSN 取代為系統 DSN 連線的名稱：

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 將驗證類型設定為 [匿名]

10. 測試連線

    ![顯示 Azure Data Factory 中成功連線的螢幕擷取畫面](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. 為您的無伺服器 SQL 目標建立另一個連結服務。 使用連結服務精靈來選取資料庫，並提供 SQL 驗證認證。

    ![顯示 Azure Data Factory 中已選取 Azure SQL Database 的螢幕擷取畫面](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. 從 SQLite 資料庫中擷取 CREATE TABLE 陳述式。 您可以在資料庫檔案上執行下列 Python 指令碼來執行這項操作。

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

13. 藉由複製 CreateTables.sql 檔案中的 CREATE 資料表陳述式，並在 Azure 入口網站的查詢編輯器中執行 SQL 陳述式，即可在無伺服器 SQL 目標環境中建立登陸資料表。

14. 返回 Data Factory 的主畫面，然後按一下 [資料複製]，以透過作業建立精靈來執行。

    ![顯示 Azure Data Factory 中複製資料精靈標誌的螢幕擷取畫面](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. 使用核取方塊選取來源 SQLite 資料庫中的所有資料表，並將其對應至 Azure SQL 中的目標資料表。 作業執行完成後，表示您已成功將資料從 SQLite 遷移至 Azure SQL 了！

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[快速入門：使用 Azure 入口網站在 Azure SQL Database 中建立單一資料庫](single-database-create-quickstart.md)。
- 如需資源限制，請參閱[無伺服器計算層級資源限制](../../sql-database/sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。
