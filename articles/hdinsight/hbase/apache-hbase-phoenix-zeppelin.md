---
title: 使用 Apache Phoenix 在 Azure HDInsight 中執行 Apache 基底查詢
description: 瞭解如何使用 Apache Zeppelin 搭配 Phoenix 來執行 Apache 基底查詢。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 2a6b324c4caf9619566ba335ab2e5a318ab68bb6
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079423"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>使用 Apache Zeppelin 在 Apache HBase 上執行 Apache Phoenix 查詢 Azure HDInsight

Apache Phoenix 是一個建置在 HBase 上且具有開放原始碼的大規模平行關聯式資料庫層。 Phoenix 可讓您在 HBase 上使用類似 SQL 的查詢。 Phoenix 使用下的 JDBC 驅動程式，可讓您建立、刪除、改變 SQL 資料表、索引、視圖和順序。  您也可以使用 Phoenix 來個別和大量更新資料列。 Phoenix 使用 NOSQL 原生編譯，而不是使用 MapReduce 來編譯查詢，讓您能夠在 HBase 上建立低延遲的應用程式。

Apache Zeppelin 是開放原始碼的 web 型筆記本，可讓您使用互動式資料分析和語言（例如 SQL 和 Scala）來建立資料驅動、共同作業的檔。 它可協助資料開發人員 & 資料科學家開發、組織、執行及共用資料操作的程式碼。 它可讓您以視覺化方式呈現結果，而不需要參考命令列或需要叢集詳細資料。

HDInsight 使用者可以使用 Apache Zeppelin 來查詢 Phoenix 資料表。 Apache Zeppelin 與 HDInsight 叢集整合，而且不需要額外的步驟就能使用它。 只要使用 JDBC 解譯器建立 Zeppelin 筆記本，並開始撰寫 Phoenix SQL 查詢

## <a name="prerequisites"></a>必要條件

HDInsight 上的 Apache HBase 叢集。 請參閱[開始使用 Apache HBase](./apache-hbase-tutorial-get-started-linux.md)。

## <a name="create-an-apache-zeppelin-note"></a>建立 Apache Zeppelin 記事

1. 在下列 URL (`https://CLUSTERNAME.azurehdinsight.net/zeppelin`) 中，將 `CLUSTERNAME` 取代為您的叢集名稱。 在網頁瀏覽器中輸入該 URL。 輸入您的叢集登入使用者名稱與密碼。

1. 從 [Zeppelin] 頁面中，選取 [**建立新便箋**]。

    ![HDInsight 互動式查詢 Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. 從 [建立新記事]**** 對話方塊，輸入或選取下列值：

    - 附注名稱：輸入便箋的名稱。
    - 預設解譯器：從下拉式清單中選取 [ **jdbc** ]。

    然後選取 [**建立便箋**]。

1. 確定筆記本標頭顯示 [已連線] 狀態。 其以右上角的綠色點表示。

    ![Zeppelin Notebook 狀態](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin Notebook 狀態")

1. 建立 HBase 資料表。 輸入下列命令，然後按下**Shift + enter**：

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    第一行中的 **% jdbc （phoenix）** 語句會告訴筆記本使用 phoenix jdbc 解譯器。

1. View 已建立的資料表。

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

## <a name="next-steps"></a>下一步

- [Apache Phoenix 現在支援 Azure HDInsight 中的 Zeppelin](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix 文法](https://phoenix.apache.org/language/index.html)
