---
title: 支援的資料平台
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解 Azure 資料科學虛擬機器支援的資料平臺和工具。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 83c0fd796b7527c6f5e396a813def984b88ee9ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440349"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>資料科學虛擬機器上所支援的資料平台

透過資料科學虛擬機器 (DSVM) ，您可以針對各種不同的資料平臺來打造您的分析。 除了遠端資料平台的介面之外，DSVM 也提供快速開發和原型設計的本機執行個體。

DSVM 支援下列資料平臺工具。

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 本機關聯式資料庫執行個體      |
| 支援的 DSVM 版本      | Windows 2016： SQL Server 2017、Windows 2019： SQL Server 2019      |
| 典型的使用案例      | <ul><li>在本機使用較小的資料集進行快速的開發</li><li>執行資料庫內 R</li></ul> |
| 範例的連結      | <ul><li>紐約城市資料集的小型範例會載入至 SQL database：<br/>  `nyctaxi`</li><li>Jupyter 範例顯示 Microsoft Machine Learning Server 和資料庫內分析，可在下列位置找到：<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| DSVM 上的相關工具       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC 驅動程式</li><li>pyodbc、RODBC</li><li>Apache 深入探詢</li></ul> |

> [!NOTE]
> SQL Server Developer 版只能用於開發和測試用途。 您需要授權或其中一個 SQL Server VM，才能在生產環境中執行。


### <a name="setup"></a>安裝程式

資料庫伺服器已預先設定，且與 SQL Server (（例如) ）相關的 Windows 服務 `SQL Server (MSSQLSERVER)` 會設定為自動執行。 唯一的手動步驟牽涉到使用 Microsoft Machine Learning Server 啟用資料庫內分析。 您可以藉由執行下列命令來啟用分析，以在 SQL Server Management Studio (SSMS) 中執行一次性動作。 當您以電腦系統管理員身分登入、在 SSMS 中開啟新的查詢，並確定選取的資料庫為時，請執行此命令 `master` ：

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

 (以您的 VM 名稱取代% COMPUTERNAME%。 ) 

若要執行 SQL Server Management Studio，您可以在程式清單上搜尋「SQL Server Management Studio」，或使用 Windows Search 來尋找並執行。 當系統提示您輸入認證時，請選取 [ **Windows 驗證** ]，並使用電腦名稱稱或 ```localhost``` 在 **SQL Server 名稱** ] 欄位中。

### <a name="how-to-use-and-run-it"></a>如何使用和執行

依預設，具有預設資料庫實例的資料庫伺服器會自動執行。 您可以在 VM 上使用 SQL Server Management Studio 之類的工具在本機存取 SQL Server 資料庫。 本機系統管理員帳戶擁有資料庫的系統管理員存取權。

此外，DSVM 還隨附 ODBC 和 JDBC 驅動程式，可與以多種語言撰寫的應用程式（包括 Python 和 Machine Learning Server）進行 SQL Server、Azure SQL 資料庫和 Azure Synapse Analytics 的討論。

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>如何在 DSVM 上設定和安裝？ 

 SQL Server 是以標準方式安裝的。 它位於 `C:\Program Files\Microsoft SQL Server`。 在中找到資料庫內 Machine Learning Server 實例 `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` 。 DSVM 也有個別的獨立 Machine Learning Server 實例，安裝于 `C:\Program Files\Microsoft\R Server\R_SERVER` 。 這兩個 Machine Learning Server 實例不會共用程式庫。


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (獨立)

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 一種獨立的 (單一節點同進程) 的 Apache Spark 平臺實例;適用于快速、大規模資料處理和機器學習的系統     |
| 支援的 DSVM 版本      | Linux     |
| 典型的使用案例      | <ul><li>使用較小的資料集在本機快速開發 Spark/PySpark 應用程式，並于稍後於大型 Spark 叢集上部署，例如 Azure HDInsight</li><li>測試 Microsoft Machine Learning Server Spark 內容</li><li>使用 SparkML 或 Microsoft 的開放原始碼 [MMLSpark](https://github.com/Azure/mmlspark) 程式庫來建立 ML 應用程式</li></ul> |
| 範例的連結      |    Jupyter 範例：<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark 內容) ：/dsvm/samples/MRS/MRSSparkCoNtextSample。R</p> |
| DSVM 上的相關工具       | <ul><li>PySpark、Scala</li><li>Jupyter (Spark/PySpark 核心)</li><li>Microsoft Machine Learning Server、SparkR、Sparklyr</li><li>Apache 深入探詢</li></ul> |

### <a name="how-to-use-it"></a>用法
您可以藉由執行或命令，在命令列上提交 Spark 作業 `spark-submit` `pyspark` 。 您也可以使用 Spark 核心建立新的 Notebook，以便建立 Jupyter Notebook。

您可以使用 DSVM 中提供的程式庫（例如 SparkR、Sparklyr 和 Microsoft Machine Learning Server），從 R 使用 Spark。 請參閱上表中範例的指標。

### <a name="setup"></a>安裝程式
在 Ubuntu Linux DSVM edition Microsoft Machine Learning Server 的 Spark 內容中執行之前，您必須完成一次性設定步驟，以啟用本機單一節點 Hadoop HDFS 和 Yarn 實例。 根據預設，Hadoop 服務已安裝但是在 DSVM 上已停用。 若要啟用它們，請在第一次以 root 身份執行下列命令：

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

您可以在不再需要 Hadoop 相關服務時，使用這些服務來停止 ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` 。

示範如何在遠端 Spark 內容中開發和測試 MRS 的範例 (也就是在 DSVM) 上提供的獨立 Spark 實例，並可在目錄中取得 `/dsvm/samples/MRS` 。


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>如何在 DSVM 上設定和安裝？ 
|平台|安裝位置 ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


使用 Microsoft MMLSpark 機器學習程式庫從 Azure Blob 儲存體或 Azure Data Lake Storage 存取資料的程式庫，會預先安裝在 $SPARK _HOME/jars。 Spark 啟動時，這些 JAR 會自動載入。 根據預設，Spark 會使用本機磁片上的資料。 

若要讓 DSVM 上的 Spark 實例存取 Blob 儲存體或 Azure Data Lake Storage 中所儲存的資料，您必須 `core-site.xml` 根據 $SPARK _HOME/conf/core-site.xml 範本中找到的範本來建立和設定檔案。 您也必須具有適當的認證，才能存取 Blob 儲存體及 Azure Data Lake Storage。  (請注意，範本檔案使用 Blob 儲存體和 Azure Data Lake Storage 設定的預留位置。 ) 

如需有關建立 Azure Data Lake Storage 服務認證的詳細資訊，請參閱 [使用 Azure Data Lake Storage Gen1 進行驗證](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)。 在 core-site.xml 檔案中輸入 Blob 儲存體或 Azure Data Lake Storage 的認證之後，您可以透過 wasb://或 adl://的 URI 前置詞來參考儲存在這些來源中的資料。

