---
title: 在 Azure HDInsight 中使用阿帕奇鳳凰運行 Apache 基礎查詢
description: 瞭解如何使用阿帕奇澤佩林運行與鳳凰的阿帕奇基地查詢。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392237"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>使用 Apache Zepelin 在 Azure HDInsight 中通過 Apache HBase 運行阿帕奇鳳凰查詢

Apache Phoenix 是一個建置在 HBase 上且具有開放原始碼的大規模平行關聯式資料庫層。 鳳凰允許您在 HBase 上使用 SQL 之類的查詢。 Phoenix 使用下面的 JDBC 驅動程式來創建、刪除、更改 SQL 表、索引、視圖和序列。  您還可以使用"鳳凰"單獨和批量更新行。 Phoenix 使用 NOSQL 本機編譯，而不是使用 MapReduce 編譯查詢，從而能夠在 HBase 之上創建低延遲應用程式。

Apache Zepelin 是一款基於 Web 的開源筆記本，使您能夠使用互動式資料分析和 SQL 和 Scala 等語言創建資料驅動的協作文檔。 它可説明資料開發人員&資料科學家開發、組織、執行和共用資料操作代碼。 它允許您視覺化結果，而無需引用命令列或需要群集詳細資訊。

HDInsight 使用者可以使用 Apache Zepelin 查詢鳳凰表。 Apache Zepelin 與 HDInsight 群集集成，沒有其他步驟使用它。 只需使用 JDBC 解譯器創建 Zeppelin 筆記本，並開始編寫鳳凰 SQL 查詢

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache HBase 群集。 請參閱[從 Apache HBase 開始](./apache-hbase-tutorial-get-started-linux.md)。

## <a name="create-an-apache-zeppelin-note"></a>建立 Apache Zeppelin 記事

1. 在下列 URL (`https://CLUSTERNAME.azurehdinsight.net/zeppelin`) 中，將 `CLUSTERNAME` 取代為您的叢集名稱。 在網頁瀏覽器中輸入該 URL。 輸入您的叢集登入使用者名稱與密碼。

1. 從 Zepelin 頁面中，選擇 **"創建新注釋**"。

    ![HDInsight 互動式查詢 Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. 從 [建立新記事]**** 對話方塊，輸入或選取下列值：

    - 注釋名稱：輸入注釋的名稱。
    - 預設解譯器：從下拉清單中選擇**jdbc。**

    然後選擇 **"創建注釋**"。

1. 確保筆記本頭顯示已連接狀態。 它由右上角的綠點表示。

    ![Zeppelin Notebook 狀態](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin Notebook 狀態")

1. 建立 HBase 資料表。 輸入以下命令，然後按 **"移位 + 輸入**：

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    第一行中的 **%jdbc（鳳凰）** 語句告訴筆記本使用鳳凰 JDBC 解譯器。

1. 查看創建的表。

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. 將值插入資料表。

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. 查詢資料表。

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. 刪除記錄。

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. 卸除資料表。

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>後續步驟

- [阿帕奇鳳凰現在支援澤佩林在Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [阿帕奇鳳凰語法](https://phoenix.apache.org/language/index.html)
