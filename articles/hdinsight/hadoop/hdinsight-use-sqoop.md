---
title: 使用 Azure HDInsight (Apache Hadoop) 執行 Apache Sqoop 作業
description: 了解如何從工作站使用 Azure PowerShell，在 Hadoop 叢集與 Azure SQL 資料庫之間執行 Sqoop 匯入和匯出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74951848"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>搭配使用 Apache Sqoop 與 HDInsight 中的 Hadoop

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

瞭解如何在 HDInsight 中使用 Apache Sqoop，在 HDInsight 叢集與 Azure SQL 資料庫之間匯入和匯出資料。

雖然 Apache Hadoop 是處理非結構化和半結構化資料（例如記錄和檔案）的自然選擇，但也可能需要處理儲存在關係資料庫中的結構化資料。

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) 是一種可在 Hadoop 叢集和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入至 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Apache Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。 在本文中，您使用的是關係資料庫的 SQL Server 資料庫。

> [!IMPORTANT]  
> 本文會設定測試環境來執行資料傳輸。 接著，您可以從[執行 Sqoop 作業](#run-sqoop-jobs)一節中的其中一種方法，為此環境選擇資料傳輸方法，如下所示。

如需 HDInsight 叢集上支援的 Sqoop 版本，請參閱[hdinsight 所提供叢集版本的新功能。](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>了解案例

HDInsight 叢集附有某些範例資料。 您將用到以下兩個範例：

* Apache Log4j 記錄檔，位於`/example/data/sample.log`。 下列記錄擷取自此檔案：

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* 名為`hivesampletable`的 Hive 資料表，它會參考位於的資料`/hive/warehouse/hivesampletable`檔。 此資料表包含某些行動裝置資料。
  
  | 欄位 | 資料類型 |
  | --- | --- |
  | clientid |字串 |
  | querytime |字串 |
  | market |字串 |
  | deviceplatform |字串 |
  | devicemake |字串 |
  | devicemodel |字串 |
  | State |字串 |
  | country |字串 |
  | querydwelltime |double |
  | sessionid |BIGINT |
  | sessionpagevieworder |BIGINT |

在本文中，您會使用這兩個資料集來測試 Sqoop 匯入和匯出。

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>設定測試環境

叢集、SQL database 和其他物件會透過使用 Azure Resource Manager 範本的 Azure 入口網站來建立。 範本可在[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)中找到。 Resource Manager 範本會呼叫 bacpac 封裝，將資料表架構部署到 SQL 資料庫。  Bacpac 套件位於公用 Blob 容器中 (https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac)。 如果您想要針對 Bacpac 檔案使用私用容器，請在範本中使用下列值︰

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> 使用範本或 Azure 入口網站匯入的方式只支援從 Azure Blob 儲存體匯入 BACPAC 檔案。

1. 選取下列影像，以在 Azure 入口網站中開啟 [Resource Manager] 範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 輸入下列屬性：

    |欄位 |值 |
    |---|---|
    |訂用帳戶 |從下拉式清單中選取您的 Azure 訂用帳戶。|
    |資源群組 |從下拉式清單中選取您的資源群組，或建立一個新的|
    |Location |從下拉式清單中選取區域。|
    |叢集名稱 |輸入 Hadoop 叢集的名稱。 僅使用小寫字母。|
    |叢集登入使用者名稱 |保留預先填入的值`admin`。|
    |叢集登入密碼 |輸入密碼。|
    |SSH 使用者名稱 |保留預先填入的值`sshuser`。|
    |SSH 密碼 |輸入密碼。|
    |Sql 系統管理員登入 |保留預先填入的值`sqluser`。|
    |Sql 管理密碼 |輸入密碼。|
    |_artifacts 位置 | 除非您想要在不同位置使用您自己的 bacpac 檔案，否則請使用預設值。|
    |_artifacts 位置 Sas 權杖 |保留空白。|
    |Bacpac 檔案名 |除非您想要使用自己的 bacpac 檔案，否則請使用預設值。|
    |Location |使用預設值。|

    Azure SQL Server 名稱會是`<ClusterName>dbserver`。 資料庫名稱將是`<ClusterName>db`。 預設的儲存體帳戶名稱將是`e6qhezrh2pdqu`。

3. 選取 [我同意上方所述的條款及條件]****。

4. 選取 [購買]  。 您會看到新的圖格，標題為「提交範本部署的部署」。 大約需要 20 分鐘的時間來建立叢集和 SQL Database。

## <a name="run-sqoop-jobs"></a>執行 Sqoop 工作

HDInsight 可以使用各種方法執行 Sqoop 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此方法** | ...一個 **互動式** 殼層 | ...**批次處理** | ...從此 **用戶端作業系統** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (目前) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>限制

* 大量匯出-使用以 Linux 為基礎的 HDInsight，用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database 的 Sqoop 連接器目前不支援大量插入。
* 批次處理 - 使用以 Linux 為基礎的 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是將插入作業批次處理。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用 Sqoop。 若要深入了解，請參閱：

* [搭配 HDInsight 使用 Apache Hive](../hdinsight-use-hive.md)
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找可將資料上傳至 HDInsight/Azure Blob 儲存體的其他方法。
* [使用 Apache Sqoop 在 Apache Hadoop on HDInsight 與 SQL Database 之間匯入及匯出資料](./apache-hadoop-use-sqoop-mac-linux.md)