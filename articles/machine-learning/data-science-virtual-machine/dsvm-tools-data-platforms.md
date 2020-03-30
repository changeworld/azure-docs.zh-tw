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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282319"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>資料科學虛擬機器上所支援的資料平台

借助資料科學虛擬機器 （DSVM），您可以針對各種資料平臺構建分析。 除了遠端資料平台的介面之外，DSVM 也提供快速開發和原型設計的本機執行個體。

DSVM 支援以下資料平臺工具。

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| 這是什麼？   | 本機關聯式資料庫執行個體      |
| 支援的 DSVM 版本      | Windows 2016： SQL 伺服器 2017， Windows 2019： SQL Server 2019      |
| 典型的使用案例      | 在本機使用較小的資料集進行快速的開發 <br/> 執行資料庫內 R   |
| 範例的連結      |    紐約市資料集的一個小示例將載入到 SQL 資料庫中：<br/>  `nyctaxi` <br/> 顯示 Microsoft 機器學習伺服器和資料庫內分析的 Jupyter 示例可在：<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM 的相關工具       | SQL Server Management Studio <br/> ODBC/JDBC 驅動程式<br/> pyodbc、RODBC<br />Apache 深入探詢      |

> [!NOTE]
> SQL 伺服器開發人員版本只能用於開發和測試目的。 您需要授權或其中一個 SQL Server VM，才能在生產環境中執行。


### <a name="setup"></a>安裝程式

資料庫伺服器已預配置，與 SQL Server 相關的 Windows 服務`SQL Server (MSSQLSERVER)`（如 ） 設置為自動運行。 唯一的手動步驟涉及通過使用 Microsoft 機器學習伺服器啟用資料庫內分析。 通過在 SQL 伺服器管理工作室 （SSMS） 中作為一次性操作運行以下命令來啟用分析。 在以電腦系統管理員身份登錄後運行此命令，在 SSMS 中打開新查詢，並確保所選資料庫為`master`：

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
要運行 SQL 伺服器管理工作室，可以在程式清單中搜索"SQL 伺服器管理工作室"，或使用 Windows 搜索查找和運行它。 當提示輸入憑據時，選擇**Windows 身份驗證**並使用電腦名稱稱```localhost```或在**SQL 伺服器名稱**欄位中。

### <a name="how-to-use-and-run-it"></a>如何使用和運行它

預設情況下，具有預設資料庫實例的資料庫伺服器會自動運行。 您可以在 VM 上使用 SQL Server Management Studio 之類的工具在本機存取 SQL Server 資料庫。 本地管理員帳戶對資料庫具有管理員存取權限。

此外，DSVM 還附帶了 ODBC 和 JDBC 驅動程式，用於從多種語言（包括 Python 和機器學習伺服器）編寫的應用程式中與 SQL Server、Azure SQL 資料庫和 Azure SQL 資料倉儲進行交談。

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>它是如何在 DSVM 上配置和安裝的？ 

 SQL Server 以標準方式安裝。 它位於 `C:\Program Files\Microsoft SQL Server`。 資料庫內機器學習伺服器實例在 中找到`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`。 DSVM 還具有一個單獨的獨立機器學習伺服器實例，該實例安裝在`C:\Program Files\Microsoft\R Server\R_SERVER`中。 這兩個機器學習伺服器實例不共用庫。


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (獨立)

| | |
| ------------- | ------------- |
| 這是什麼？   | 流行的 Apache Spark 平臺的獨立（單個節點在進程內）實例;快速、大規模資料處理和機器學習的系統     |
| 支援的 DSVM 版本      | Linux     |
| 典型的使用案例      | • 在本地快速開發 Spark/PySpark 應用程式，使用更小的資料集，並在 Azure HDInsight 等大型 Spark 群集上部署<br/> • 測試微軟機器學習伺服器火花上下文 <br />• 使用 SparkML 或微軟的開源[MMLSpark](https://github.com/Azure/mmlspark)庫構建 ML 應用程式 |
| 範例的連結      |    Jupyter 範例： <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> 微軟機器學習伺服器（火花上下文）：/dsvm/示例/MRS/MRSSparkCoNtextSample.R |
| DSVM 的相關工具       | PySpark、Scala<br/>Jupyter (Spark/PySpark 核心)<br/>微軟機器學習伺服器，SparkR，Sparklyr <br />Apache 深入探詢      |

### <a name="how-to-use-it"></a>如何使用它
您可以通過運行`spark-submit`或`pyspark`命令在命令列上提交 Spark 作業。 您也可以使用 Spark 核心建立新的 Notebook，以便建立 Jupyter Notebook。

您可以使用 SparkR、Sparkr 和 Microsoft 機器學習伺服器等庫使用 R 中的 Spark，這些庫在 DSVM 上可用。 請參閱上表中範例的指標。

### <a name="setup"></a>安裝程式
在 Ubuntu Linux DSVM 版本的 Microsoft 機器學習伺服器的 Spark 上下文中運行之前，必須完成一次性設置步驟，以啟用本地單個節點 Hadoop HDFS 和 Yarn 實例。 根據預設，Hadoop 服務已安裝但是在 DSVM 上已停用。 要啟用它們，第一次以 root 身份運行以下命令：

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

當您不再需要使用與 Hadoop 相關的服務時，可以通過運行```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```來停止這些服務。

提供一個示例，演示如何在遠端 Spark 上下文中開發和測試 MRS（這是 DSVM 上的獨立 Spark 實例），並在`/dsvm/samples/MRS`目錄中提供。


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>它是如何在 DSVM 上配置和安裝的？ 
|Platform|安裝位置 ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


使用 Microsoft MMLSpark 機器學習庫預裝的庫用於訪問 Azure Blob 存儲或 Azure 資料湖存儲的資料，這些庫預先安裝在$SPARK_HOME/jars 中。 Spark 啟動時，這些 JAR 會自動載入。 預設情況下，Spark 使用本地磁片上的資料。 

對於 DSVM 上的 Spark 實例，要訪問存儲在 Blob 存儲或 Azure 資料湖存儲中的資料`core-site.xml`，必須基於 $SPARK_HOME/conf/core site.xml.template 中的範本創建和配置該檔。 還必須具有適當的憑據才能訪問 Blob 存儲和 Azure 資料湖存儲。 （請注意，範本檔使用預留位置進行 Blob 存儲和 Azure 資料湖存儲配置。

有關創建 Azure 資料湖存儲服務憑據的詳細資訊，請參閱[使用 Azure 資料湖存儲 Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)進行身份驗證。 在核心 site.xml 檔中輸入 Blob 存儲或 Azure 資料湖存儲的憑據後，可以通過 wasb://或adl://的 URI 首碼引用存儲在這些源中的資料。

