---
title: 管理 Azure HDInsight 上的 Spark 應用程式相依性
description: 本文提供如何在 HDInsight Spark 叢集中為 PySpark 和 Scala 應用程式管理 Spark 相依性的簡介。
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: dafb4485ae9b10d89fa36bd790dcf3a799054de3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064112"
---
# <a name="manage-spark-application-dependencies"></a>管理 Spark 應用程式相依性

在本文中，您將瞭解如何管理在 HDInsight 上執行的 Spark 應用程式的相依性。 我們在 Spark 應用程式和叢集範圍涵蓋了 Scala 和 PySpark。

根據您的使用者案例，使用快速連結跳至區段：
* [使用 Jupyter 筆記本設定 Spark 作業 jar 相依性](#use-jupyter-notebook)
* [使用 Use Azure Toolkit for IntelliJ 設定 Spark 作業 jar 相依性](#use-azure-toolkit-for-intellij)
* [設定 Spark 叢集的 jar 相依性](#jar-libs-for-cluster)
* [安全地管理 Jar 相依性](#safely-manage-jar-dependencies)
* [使用 Jupyter 筆記本設定 Spark 作業 Python 套件](#use-jupyter-notebook-1)
* [安全地管理適用于 Spark 叢集的 Python 套件](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>一個 Spark 作業的 Jar 程式庫
### <a name="use-jupyter-notebook"></a>使用 Jupyter 筆記本
當 Spark 會話以 Scala 的 Spark 核心開始 Jupyter Notebook 時，您可以從下列設定套件：

* [Spark 套件](https://spark-packages.org/)上的[Maven 存放庫](https://search.maven.org/)或由社區貢獻的套件。
* 儲存在叢集主要儲存體上的 Jar 檔案。

您將使用 `%%configure` 魔術將筆記本設定為使用外部套件。 在使用外部套件的 Notebook 中，確定您在第一個程式碼單元中呼叫 `%%configure` magic。 這可確保將核心設定為在啟動工作階段之前即使用此套件。

>
>[!IMPORTANT]  
>如果您忘記在第一個單元中設定核心，您可以搭配 `-f` 參數使用 `%%configure`，但這會重新啟動工作階段，而所有進度都將遺失。

**Maven 存放庫或 Spark 套件中套件的範例**

從 Maven 存放庫尋找封裝之後，請收集 **GroupId**、 **ArtifactId**和 **Version**的值。 串連三個值，其中以冒號分隔 (**:**)。

   ![串連封裝架構](./media/apache-spark-manage-dependencies/spark-package-schema.png "串連封裝架構")

請確定您收集的值符合您的叢集。 在此情況下，我們會使用適用于 Scala 2.11 的 Spark Cosmos DB 連接器套件，以及適用于 HDInsight 的 Spark 2.3 3.6 Spark 叢集。 如果您不確定，請 `scala.util.Properties.versionString` 在 Spark 核心的程式碼資料格中執行，以取得叢集 Scala 版本。 執行 `sc.version` 以取得叢集 Spark 版本。

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**儲存在主要儲存體上的 Jar 範例**

在叢集主要儲存體上使用 jar 檔案的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 。 `wasb://` 適用於 Azure 儲存體，`abfs://` 適用於 Azure Data Lake Storage Gen2 或 `adl://` 適用於 Azure Data Lake Storage Gen1。 如果已針對 Azure 儲存體或 Data Lake Storage Gen2 啟用安全傳輸，則 URI 會是 `wasbs://` 或 `abfss://` 。 請參閱 [安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

針對多個 jar 檔案使用以逗號分隔的 jar 路徑清單，允許 Glob。 Jar 包含在驅動程式和執行程式的類程式中。

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

設定外部封裝之後，您可以在程式碼資料格中執行匯入，以確認是否已正確放置封裝。

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>使用 Azure Toolkit for IntelliJ
[Azure Toolkit for IntelliJ 外掛程式](./apache-spark-intellij-tool-plugin.md) 提供 UI 體驗，以將 Spark Scala 應用程式提交至 HDInsight 叢集。 它會提供 `Referenced Jars` 和 `Referenced Files` 屬性，以在提交 Spark 應用程式時設定 jar 程式庫路徑。 查看更多有關 [如何使用 HDInsight Azure Toolkit for IntelliJ 外掛程式的](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)詳細資料。

![[提交 Spark] 對話方塊](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>叢集的 Jar 程式庫
在某些情況下，您可能會想要在叢集層級設定 jar 相依性，以便根據預設，每個應用程式都可以使用相同的相依性來設定。 方法是將 jar 路徑新增至 Spark 驅動程式和執行程式類別路徑。

1. 執行下列範例腳本動作，以將 jar 檔案從主要儲存體複製 `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` 到叢集本機檔案系統 `/usr/libs/sparklibs` 。 需要執行此步驟，因為 linux 會使用 `:` 來分隔類別路徑清單，但 HDInsight 僅支援像這樣的配置儲存體路徑 `wasb://` 。 如果您直接將遠端存放裝置路徑新增至類別路徑，該路徑將無法正常運作。

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. 從 Ambari 變更 Spark 服務設定，以更新類別路徑。 移至 **Ambari > Spark > 設置 > 自訂 Spark2-預設值**。 **加入屬性** ，如下所示。 `:`如果您要新增多個路徑，請使用來分隔路徑。 允許 glob。

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![變更 Spark 預設設定](./media/apache-spark-manage-dependencies/change-spark-default-config.png "變更 Spark 預設設定")

3. 儲存變更的設定，並重新啟動受影響的服務。

   ![重新開機受影響的服務](./media/apache-spark-manage-dependencies/restart-impacted-services.png "重新開機受影響的服務")

您可以使用 [腳本動作](../hdinsight-hadoop-customize-cluster-linux.md)將步驟自動化。 [新增 Hive 自訂程式庫](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)的腳本動作是很好的參考。 變更 Spark 服務設定時，請務必使用 Ambari Api，而不是直接修改設定檔。 

## <a name="safely-manage-jar-dependencies"></a>安全地管理 Jar 相依性
HDInsight 叢集有內建的 jar 相依性，而這些 jar 版本的更新會在一段時間內進行。 若要避免內建 jar 和您所提供的 jar 之間發生版本衝突，請考慮 [將應用程式](./safely-manage-jar-dependency.md)相依性加上陰影。

## <a name="python-packages-for-one-spark-job"></a>適用于一個 Spark 作業的 Python 套件
### <a name="use-jupyter-notebook"></a>使用 Jupyter 筆記本
HDInsight Jupyter 筆記本 PySpark 核心不支援直接從 PyPi 或 Anaconda 套件存放庫安裝 Python 套件。 如果您有 `.zip` 、 `.egg` 或相依性 `.py` ，而且想要針對一個 Spark 會話參考這些相依性，請遵循下列步驟：

1. 執行下列範例腳本動作，以將主要儲存體的檔案或檔案複製 `.zip` `.egg` 到叢集 `.py` `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` 本機檔案系統 `/usr/libs/pylibs` 。 需要執行此步驟，因為 linux 會使用 `:` 來分隔搜尋路徑清單，但 HDInsight 僅支援像這樣的配置儲存體路徑 `wasb://` 。 當您使用時，遠端存放裝置路徑將無法正常運作 `sys.path.insert` 。

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. 在筆記本中，以 PySpark 核心在程式碼資料格中執行下列程式碼：

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. 執行 `import` 以檢查套件是否已順利包含。  

## <a name="python-packages-for-cluster"></a>適用于叢集的 Python 套件
您可以透過腳本動作使用 conda 命令，將 Python 套件從 Anaconda 安裝到叢集。 安裝的套件會在叢集層級，並套用至所有應用程式。 

HDInsight Spark 叢集有兩個內建的 Python 安裝，Anaconda Python 2.7 和 Anaconda Python 3.5。 若要深入瞭解服務的預設 Python 設定，以及如何在不中斷叢集的情況下安全地安裝外部 Python 套件，請參閱更多詳細資料以 [安全地管理叢集的 Python](./apache-spark-python-package-installation.md)相依性。
