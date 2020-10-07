---
title: 快速入門：使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive
description: 在此快速入門中，您將了解如何使用 Apache Zeppelin 執行 Apache Hive 查詢。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 49b576fd511d17616880e5d981fd3f649de797df
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "79367919"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>快速入門：使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢

在此快速入門中，您將會了解如何使用 Apache Zeppelin 在 Azure HDInsight 中執行 [Apache Hive](https://hive.apache.org/) 查詢。 HDInsight 互動式查詢叢集包含 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook，可供您用來執行互動式 Hive 查詢。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

HDInsight 互動式查詢叢集。 請參閱[建立叢集](../hadoop/apache-hadoop-linux-tutorial-get-started.md)以建立 HDInsight 叢集。  請務必選擇 [互動式查詢]  叢集類型。

## <a name="create-an-apache-zeppelin-note"></a>建立 Apache Zeppelin 記事

1. 在下列 URL (`https://CLUSTERNAME.azurehdinsight.net/zeppelin`) 中，將 `CLUSTERNAME` 取代為您的叢集名稱。 在網頁瀏覽器中輸入該 URL。

2. 輸入您的叢集登入使用者名稱與密碼。 透過 [Zeppelin] 頁面，您可以建立新的記事或開啟現有記事。 **HiveSample** 包含一些 Hive 查詢範例。  

    ![HDInsight 互動式查詢 Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. 選取 [建立新記事]  。

4. 從 [建立新記事]  對話方塊，輸入或選取下列值：

    - 記事名稱：輸入記事的名稱。
    - 預設解譯器：從下拉式清單中選取 [jdbc]  。

5. 選取 [建立記事]  。

6. 在程式碼區段中輸入下列 Hive 查詢，然後按 **Shift + Enter**：

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight 互動式查詢 Zeppelin 執行查詢](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    第一行裡面的 **%jdbc(hive)** 陳述式會指示筆記本使用 Hive JDBC 解譯器。

    此查詢應該會傳回一個 Hive 資料表，其名稱為 hivesampletable  。

    以下是您可以對 **hivesampletable** 執行的另外兩個 Hive 查詢：

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    相較於傳統的 Hive，查詢結果的傳回速度會快很多。

### <a name="additional-examples"></a>其他範例

1. 建立資料表。 在 Zeppelin Notebook 中執行下列程式碼：

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. 將資料載入到新的資料表。 在 Zeppelin Notebook 中執行下列程式碼：

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. 插入單一記錄。 在 Zeppelin Notebook 中執行下列程式碼：

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

如需其他語法，請參閱 [Hive 語言手冊](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)。

## <a name="clean-up-resources"></a>清除資源

完成此快速入門之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地刪除該叢集。 您也需支付 HDInsight 叢集的費用 (即使未使用該叢集)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

若要刪除叢集，請參閱[使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢。 若要深入了解 Hive 查詢，下一篇文章將會說明如何使用 Visual Studio 來執行查詢。

> [!div class="nextstepaction"]
> [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
