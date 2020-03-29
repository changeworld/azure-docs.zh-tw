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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951848"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>搭配使用 Apache Sqoop 與 HDInsight 中的 Hadoop

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

瞭解如何在 HDInsight 中使用 Apache Sqoop 在 HDInsight 群集和 Azure SQL 資料庫之間導入和匯出資料。

儘管 Apache Hadoop 是處理非結構化和半結構化資料（如日誌和檔）的自然選擇，但可能還需要處理存儲在關係資料庫中的結構化資料。

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) 是一種可在 Hadoop 叢集和關聯式資料庫之間傳送資料的工具。 此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入至 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Apache Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。 在本文中，您將 SQL Server 資料庫用於關係資料庫。

> [!IMPORTANT]  
> 本文設置一個測試環境來執行資料傳輸。 然後，從["運行 Sqoop 作業](#run-sqoop-jobs)"部分中的方法之一中選擇此環境的資料傳輸方法，下面進一步說明。

有關 HDInsight 群集支援的 Sqoop 版本，請參閱[HDInsight 提供的群集版本中有哪些新增功能？](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>了解案例

HDInsight 叢集附有某些範例資料。 您將用到以下兩個範例：

* 位於 的 Apache Log4j 日誌檔位於`/example/data/sample.log`。 下列記錄擷取自此檔案：

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* 名為 的`hivesampletable`Hive 表引用 位於 的資料`/hive/warehouse/hivesampletable`檔。 此資料表包含某些行動裝置資料。
  
  | 欄位 | 資料類型 |
  | --- | --- |
  | clientid |字串 |
  | querytime |字串 |
  | market |字串 |
  | deviceplatform |字串 |
  | devicemake |字串 |
  | devicemodel |字串 |
  | state |字串 |
  | country |字串 |
  | querydwelltime |double |
  | sessionid |BIGINT |
  | sessionpagevieworder |BIGINT |

在本文中，您可以使用這兩個資料集來測試 Sqoop 導入和匯出。

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>設置測試環境

群集、SQL 資料庫和其他物件通過使用 Azure 資源管理器範本通過 Azure 門戶創建。 該範本可以在 Azure[快速入門範本中找到](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)。 資源管理器範本調用 bacpac 包以將表架構部署到 SQL 資料庫。  Bacpac 套件位於公用 Blob 容器中 (https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac)。 如果您想要針對 Bacpac 檔案使用私用容器，請在範本中使用下列值︰

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> 使用範本或 Azure 入口網站匯入的方式只支援從 Azure Blob 儲存體匯入 BACPAC 檔案。

1. 選擇以下圖像以在 Azure 門戶中打開資源管理器範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 輸入下列屬性：

    |欄位 |值 |
    |---|---|
    |訂用帳戶 |從下拉清單中選擇 Azure 訂閱。|
    |資源群組 |從下拉清單中選擇資源組，或創建新資源組|
    |Location |從下拉式清單中選取區域。|
    |叢集名稱 |輸入 Hadoop 叢集的名稱。 僅使用小寫字母。|
    |叢集登入使用者名稱 |保留預填充的值`admin`。|
    |叢集登入密碼 |輸入密碼。|
    |Ssh 使用者名 |保留預填充的值`sshuser`。|
    |Ssh 密碼 |輸入密碼。|
    |Sql 管理員登錄 |保留預填充的值`sqluser`。|
    |Sql 管理員密碼 |輸入密碼。|
    |_artifacts 位置 | 使用預設值，除非您想在其他位置使用自己的 bacpac 檔。|
    |_artifacts位置 Sas 權杖 |保留空白。|
    |巴派克檔案名 |使用預設值，除非您要使用自己的 bacpac 檔。|
    |Location |使用預設值。|

    Azure SQL 伺服器名稱將為`<ClusterName>dbserver`。 資料庫名稱將為`<ClusterName>db`。 預設存儲帳戶名稱將為`e6qhezrh2pdqu`。

3. 選取 [我同意上方所述的條款及條件]****。

4. 選取 [購買]****。 您會看到新的圖格，標題為「提交範本部署的部署」。 大約需要 20 分鐘的時間來建立叢集和 SQL Database。

## <a name="run-sqoop-jobs"></a>執行 Sqoop 工作

HDInsight 可以使用各種方法執行 Sqoop 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| **使用此方法** | ...一個 **互動式** 殼層 | ...**批次處理** | ...從此 **用戶端作業系統** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (目前) |
| [Azure 電源外殼](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>限制

* 批量匯出 - 使用基於 Linux 的 HDInsight，用於將資料匯出到 Microsoft SQL Server 或 Azure SQL 資料庫的 Sqoop 連接器當前不支援批量插入。
* 批次處理 - 使用以 Linux 為基礎的 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是將插入作業批次處理。

## <a name="next-steps"></a>後續步驟

現在，您已經學會了如何使用 Sqoop。 若要深入了解，請參閱：

* [搭配 HDInsight 使用 Apache Hive](../hdinsight-use-hive.md)
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找可將資料上傳至 HDInsight/Azure Blob 儲存體的其他方法。
* [使用 Apache Sqoop 在 Apache Hadoop on HDInsight 與 SQL Database 之間匯入及匯出資料](./apache-hadoop-use-sqoop-mac-linux.md)