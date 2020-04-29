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
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282319"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>資料科學虛擬機器上所支援的資料平台

有了資料科學虛擬機器（DSVM），您就可以針對各種資料平臺建立您的分析。 除了遠端資料平台的介面之外，DSVM 也提供快速開發和原型設計的本機執行個體。

DSVM 支援下列資料平臺工具。

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| 這是什麼？   | 本機關聯式資料庫執行個體      |
| 支援的 DSVM 版本      | Windows 2016： SQL Server 2017、Windows 2019： SQL Server 2019      |
| 典型的使用案例      | 在本機使用較小的資料集進行快速的開發 <br/> 執行資料庫內 R   |
| 範例的連結      |    新的紐約市資料集的小型範例會載入至 SQL 資料庫：<br/>  `nyctaxi` <br/> Jupyter 範例顯示 Microsoft Machine Learning Server 和資料庫內分析，可以在下列位置找到：<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM 上的相關工具       | SQL Server Management Studio <br/> ODBC/JDBC 驅動程式<br/> pyodbc、RODBC<br />Apache 深入探詢      |

> [!NOTE]
> SQL Server Developer 版本只能用於開發和測試目的。 您需要授權或其中一個 SQL Server VM，才能在生產環境中執行。


### <a name="setup"></a>安裝程式

資料庫伺服器已經預先設定，而且與 SQL Server 相關的 Windows 服務（例如`SQL Server (MSSQLSERVER)`）會設為自動執行。 唯一的手動步驟包括使用 Microsoft Machine Learning Server 啟用資料庫內分析。 您可以藉由在 SQL Server Management Studio （SSMS）中執行下列命令，做為一次性動作來啟用分析。 當您以電腦系統管理員身分登入、在 SSMS 中開啟新的查詢，並確定選取的資料庫為下列情況時`master`，請執行此命令：

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
若要執行 SQL Server Management Studio，您可以在程式清單中搜尋「SQL Server Management Studio」，或使用 Windows 搜尋來尋找並執行。 當系統提示您輸入認證時，請選取 [ **Windows 驗證**] ```localhost``` ，並使用電腦名稱稱或在 [ **SQL Server 名稱**] 欄位中。

### <a name="how-to-use-and-run-it"></a>如何使用並加以執行

根據預設，具有預設資料庫實例的資料庫伺服器會自動執行。 您可以在 VM 上使用 SQL Server Management Studio 之類的工具在本機存取 SQL Server 資料庫。 本機系統管理員帳戶具有資料庫的管理員存取權。

此外，DSVM 也隨附 ODBC 和 JDBC 驅動程式，可與 SQL Server、Azure SQL 資料庫，以及從以多種語言（包括 Python 和 Machine Learning Server）撰寫的應用程式 Azure SQL 資料倉儲。

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>它如何在 DSVM 上進行設定和安裝？ 

 SQL Server 是以標準方式安裝。 它位於 `C:\Program Files\Microsoft SQL Server`。 在中，可找到資料庫內 Machine Learning Server 實例`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`。 DSVM 也有個別的獨立 Machine Learning Server 實例，其安裝位置`C:\Program Files\Microsoft\R Server\R_SERVER`為。 這兩個 Machine Learning Server 實例不會共用程式庫。


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (獨立)

| | |
| ------------- | ------------- |
| 這是什麼？   | 受歡迎的 Apache Spark 平臺的獨立（單一節點同進程）實例;一種系統，用於快速、大規模的資料處理和機器學習     |
| 支援的 DSVM 版本      | Linux     |
| 典型的使用案例      | * 以較小的資料集在本機上快速開發 Spark/PySpark 應用程式，並于稍後部署在大型 Spark 叢集上，例如 Azure HDInsight<br/> * 測試 Microsoft Machine Learning Server Spark 內容 <br />* 使用 SparkML 或 Microsoft 的開放原始碼[MMLSpark](https://github.com/Azure/mmlspark)程式庫來建立 ML 應用程式 |
| 範例的連結      |    Jupyter 範例： <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server （Spark 內容）：/dsvm/samples/MRS/MRSSparkCoNtextSample。R |
| DSVM 上的相關工具       | PySpark、Scala<br/>Jupyter (Spark/PySpark 核心)<br/>Microsoft Machine Learning Server、SparkR、Sparklyr <br />Apache 深入探詢      |

### <a name="how-to-use-it"></a>用法
您可以執行`spark-submit`或`pyspark`命令，在命令列上提交 Spark 作業。 您也可以使用 Spark 核心建立新的 Notebook，以便建立 Jupyter Notebook。

您可以使用 SparkR、Sparklyr 和 Microsoft Machine Learning Server 等程式庫（可在 DSVM 上取得），從 R 使用 Spark。 請參閱上表中範例的指標。

### <a name="setup"></a>安裝程式
在 Ubuntu Linux DSVM edition 的 Microsoft Machine Learning Server Spark 內容中執行之前，您必須完成一次性設定步驟，以啟用本機單一節點 Hadoop HDFS 和 Yarn 實例。 根據預設，Hadoop 服務已安裝但是在 DSVM 上已停用。 若要啟用它們，請在第一次時以 root 身分執行下列命令：

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

當您不再需要 Hadoop 相關服務時，可以藉由執行來停止```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```。

此`/dsvm/samples/MRS`範例示範如何在遠端 Spark 內容中開發和測試 MRS （這是 DSVM 上的獨立 Spark 實例），並可在目錄中使用。


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>它如何在 DSVM 上進行設定和安裝？ 
|平台|安裝位置 ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


使用 Microsoft MMLSpark 機器學習程式庫，從 Azure Blob 儲存體或 Azure Data Lake Storage 存取資料的程式庫，會預先安裝在 $SPARK _HOME/jars。 Spark 啟動時，這些 JAR 會自動載入。 根據預設，Spark 會使用本機磁片上的資料。 

若要讓 DSVM 上的 Spark 實例存取 Blob 儲存體或 Azure Data Lake Storage 中儲存的資料，您必須根據 $SPARK _HOME `core-site.xml` /conf/core-site.xml.template. 中找到的範本來建立和設定檔案。 您也必須具有適當的認證，才能存取 Blob 儲存體和 Azure Data Lake Storage。 （請注意，範本檔案會使用 Blob 儲存體和 Azure Data Lake Storage 設定的預留位置）。

如需建立 Azure Data Lake Storage 服務認證的詳細資訊，請參閱[使用 Azure Data Lake Storage Gen1 進行驗證](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)。 在 core-site.xml 中輸入 Blob 儲存體或 Azure Data Lake Storage 的認證之後，您可以透過 wasb://或 adl://的 URI 前置詞來參考儲存在這些來源中的資料。

