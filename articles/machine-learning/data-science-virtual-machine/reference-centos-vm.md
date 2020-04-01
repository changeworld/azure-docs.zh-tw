---
title: '參考: CentOS 資料科學虛擬機器'
titleSuffix: Azure Data Science Virtual Machine
description: CentOS 資料科學虛擬機器中包含的工具的詳細資訊
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 05da812023e7525b87a3d7ef1ce1940339f99791
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475492"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>參考: CentOS (Linux) 資料科學虛擬機器

Linux 資料科學虛擬機器 (DSVM) 是以 CentOS 為基礎的 Azure 虛擬機器。 Linux DSVM 隨附預先安裝的工具集合，可供您用來進行資料分析和機器學習。 

Linux DSVM 中包含的主要軟體元件如下︰

* Linux CentOS 散發套件作業系統。
* Microsoft Machine Learning Server。
* Anaconda Python 散發套件 (3.5 版和 2.7 版)，包含常用的資料分析程式庫。
* JuliaPro，這是 Julia 語言和常用科學與資料分析程式庫的精選散發套件。
* Spark 獨立執行個體和單一節點 Hadoop (HDFS、YARN)。
* JupyterHub，此為多使用者的 Jupyter Notebook 伺服器，支援 R、Python、PySpark 和 Julia 核心。
* Azure 儲存體總管。
* Azure CLI，此為用來管理 Azure 資源的 Azure 命令列介面。
* PostgresSQL 資料庫。
* 機器學習工具：
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK)，此為來自 Microsoft Research 的深度學習軟體工具組。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)，此為快速的機器學習系統，支援線上雜湊，allreduce、簡化、learning2search、主動式和互動式學習等技術。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/)，此工具可提供快速且正確的促進式決策樹實作。
  * [Rattle](https://togaware.com/rattle/)，此工具可協助您輕鬆地開始使用 R 進行資料分析和機器學習。 Rattle 提供 GUI 型資料探索以及使用自動 R 程式碼產生來建立模型的功能。
* Java、Python、Node.js、Ruby 和 PHP 中的 Azure SDK。
* R 和 Python 語言的程式庫，可用於 Azure Machine Learning 和其他 Azure 服務。
* 開發工具和編輯器 (RStudio、PyCharm、IntelliJ、Emacs、gedit、vi)。

資料科學涉及反覆進行一連串的工作︰

1. 尋找、載入及前置處理資料。
1. 建置與測試模型。
1. 部署要在智慧型應用程式中使用的模型。

資料科學家使用各種工具來完成這些工作。 尋找正確的軟體版本，然後下載、編譯並安裝這些軟體，是很耗費時間的工作。

Linux DSVM 可大幅減輕這樣的負擔。 使用 Linux DSVM 可以快速啟動分析專案。 Linux DSVM 可協助您用各種語言處理工作，包含 R、Python、SQL、Java 與 C++。 Eclipse 提供容易使用的 IDE，讓您開發和測試程式碼。 DSVM 中所含的 Azure SDK 可協助您在 Linux 上使用適用於 Microsoft 雲端平台的各種服務，來建置您的應用程式。 此外也會預先安裝其他語言，包括 Ruby、Perl、PHP 和 Node.js。

DSVM 映像不會產生軟體費用。 您僅需支付 Azure 硬體使用費，這是根據您使用 DSVM 映像所佈建的虛擬機器大小來評估。 有關計算費用的詳細資訊,請參閱 Azure 應用商店中[用於 Linux 的數據科學虛擬機器清單](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)。


## <a name="machine-learning-server"></a>Machine Learning Server

R 是其中一種最受歡迎的資料分析和機器學習語言。 如果您想要將 R 用於分析，DSVM 的 Machine Learning Server 具有 Microsoft R Open 和數學核心程式庫，可供您利用。 數學核心程式庫會將分析演算法中常見的數學運算作業最佳化。 R Open 可與 CRAN R 完全相容。CRAN 中發佈的任何 R 程式庫都可安裝在 R Open 上。 

您可以使用 Machine Learning Server 在 Web 服務中調整和運作 R 模型。 您可以在其中一個預設編輯器 (如 RStudio、vi 或 Emacs) 中編輯您的 R 程式。 Emacs 編輯器會預先安裝在 DSVM 上。 Emacs ESS (Emacs Speaks Statistics) 套件能夠簡化在 Emacs 編輯器中處理 R 檔案的程序。

要開啟 R 主控台,請在 shell 中輸入**R**。此命令將帶您到互動式環境。 若要開發 R 程式，在一般情況下，您可以使用編輯器 (例如 Emacs 或 vi)，然後在 R 中執行指令碼。RStudio 提供了完整的圖形化 IDE 可用來開發 R 程式。

DSVM 中包含可用來安裝[最熱門的 20 個 R 套件](https://www.kdnuggets.com/2015/06/top-20-r-packages.html)的 R 指令碼。 您可以在進入 R 互動式介面後執行此指令碼。 如前所述，若要開啟該介面，請在殼層中輸入 **R**。  

## <a name="python"></a>Python

Anaconda Python 會隨著 Python 3.5 和 2.7 環境一起安裝。 2.7 環境_root_稱為 root,3.5 環境稱為_py35_。 這個散發套件包含基本的 Python 以及大約 300 個最受歡迎的數學運算、工程設計和資料分析封裝。

py35 環境是預設環境。 若要啟用根 (2.7) 環境，請使用下列命令：

```bash
source activate root
```

若要再次啟用 py35 環境，請使用下列命令：

```bash
source activate py35
```

若要叫用 Python 互動式工作階段，請在殼層中輸入 **Python**。 

使用 Conda 或 pip 安裝其他 Python 程式庫。 對於 pip，如果您不想使用預設值，請先啟用正確的環境：

```bash
source activate root
pip install <package>
```

或指定 pip 的完整路徑：

```bash
/anaconda/bin/pip install <package>
```

對於 Conda，您一律應指定環境名稱 (py35 或 root)：

```bash
conda install <package> -n py35
```

如果您位於圖形化介面中，或已設定 X11 轉送，可以輸入 **pycharm** 來開啟 PyCharm Python IDE。 您可以使用預設的文字編輯器。 此外，您也可以使用 Spyder，這是與 Anaconda Python 散發套件配套的 Python IDE。 Spyder 需要圖形化桌面或 X11 轉寄。 圖形化桌面具有 Spyder 的捷徑。

## <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda 散發套件也隨附 Jupyter Notebook，這是一個共用程式碼與分析的環境。 請透過 JupyterHub 來存取 Jupyter Notebook。 您可以使用本機 Linux 使用者名稱和密碼來登入。

Jupyter Notebook 伺服器已預先設定 Python 2、Python 3 及 R 核心。 請使用 **Jupyter Notebook** 桌面圖示開啟瀏覽器，並存取 Jupyter Notebook 伺服器。 如果您透過 SSH 或 X2Go 用戶端來存取 DSVM，您也可以經由 https:\//localhost:8000/ 來存取 Jupyter Notebook 伺服器。

> [!NOTE]
> 如果您收到任何憑證警告，請繼續。

您可以從任何主機存取 Jupyter Notebook 伺服器。 請輸入**\//\<HT00\>** 000 00 000 000。

> [!NOTE]
> 在佈建 DSVM 時，防火牆依預設會開啟連接埠 8000。 

Microsoft 已封裝範例 Notebook，一個用於 Python，另一個用於 R。當您使用本機 Linux 使用者名稱和密碼向 Jupyter Notebook 進行驗證後，您將可在 Jupyter Notebook 的首頁上看到範例的連結。 若要建立新的 Notebook，請選取 [新增]****，然後選取您要使用的語言核心。 若未看到 [新增]**** 按鈕，請選取左上方的 **Jupyter** 圖示，以移至 Notebook 伺服器的首頁。

## <a name="spark-standalone"></a>獨立 Spark 

Spark 獨立模式的執行個體已預先安裝在 Linux DSVM，可協助您先在本機開發 Spark 應用程式，再於大型叢集上進行測試及部署。 

您可以透過 Jupyter 核心執行 PySpark 程式。 當您開啟 Jupyter 時，請選取 [新增]**** 按鈕，您應該會看到可用核心的清單。 **Spark - Python** 是 PySpark 核心，可讓您使用 Python 語言建置 Spark 應用程式。 您也可以使用像是 PyCharm 或 Spyder 的 Python IDE 來建置 Spark 程式。 

在此獨立執行個體中，Spark 堆疊會在呼叫用戶端程式中執行。 相較於在 Spark 叢集上進行開發，此功能可更輕鬆快速地對問題進行疑難排解。

Jupyter 提供範例 PySpark Notebook。 它位於 Jupyter 主目錄下的 SparkML 目錄中 ($HOME/notebooks/SparkML/pySpark)。 

如果您要以 R 對 Spark 進行程式設計，您可以使用 Machine Learning Server、SparkR 或 sparklyr。 

在 Machine Learning Server 的 Spark 內容中執行之前，您必須執行一次性設定步驟，以啟用本機單一節點 Hadoop HDFS 和 YARN 執行個體。 根據預設，Hadoop 服務已安裝但是在 DSVM 上已停用。 若要啟用 Hadoop 服務，您在第一次必須以 root 身分執行下列命令︰

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

不需要 Hadoop 相關服務時，您可以執行 `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn` 以停止這類服務。

/Dsvm/samples/MRS 目錄提供的範例會示範如何在遠端 Spark 內容 (DSVM 上的獨立 Spark 執行個體) 中開發和測試 Machine Learning Server。

## <a name="ides-and-editors"></a>IDE 和編輯器

您有數個程式碼編輯器可供選擇，包括 vi/VIM、Emacs、gedit、PyCharm、RStudio、Eclipse、LaTeX 和 IntelliJ。 

* gedit、Eclipse、IntelliJ、R Studio 和 PyCharm 都是圖形化編輯器。 若要加以使用，您必須登入圖形化桌面。 您可以使用桌面和應用程式功能表捷徑加以開啟。

* Vim 和 Emacs 是文字型編輯器。 在 Emacs 上，ESS 附加元件套件可讓您更輕鬆地在 Emacs 編輯器中使用 R。 您可以在 [ESS 網站](https://ess.r-project.org/)上找到更多相關資訊。

* Eclipse 是可延伸的 IDE 開放原始碼，可支援多種語言。 適用於 Java 開發人員的 Eclipse IDE 是安裝在 DSVM 上的版本。 您可以安裝數種常用語言的外掛程式來擴充環境。 

  Azure Toolkit for Eclipse 外掛程式會與 Eclipse 一起安裝在 DSVM 上。 您可以使用 Azure Toolkit for Eclipse，透過支援如 Java 等語言的 Eclipse 開發環境，來建立、開發、測試和部署 Azure 應用程式。

  Azure SDK for Java 也會隨 Azure Toolkit for Eclipse 安裝在 DSVM 上。 Azure SDK for Java 可讓您從 Java 環境中存取不同的 Azure 服務。 
  
  如需詳細資訊，請參閱 [適用於 Eclipse 的 Azure 工具組](/java/azure/eclipse/azure-toolkit-for-eclipse)。

* LaTeX 透過 texlive 套件以及名為[AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html)的 Emacs 附加元件進行安裝。 此套件可讓您更容易在 Emacs 中撰寫 LaTeX 文件。 

## <a name="databases"></a>資料庫

Linux DSVM 可讓您存取數個資料庫和命令列工具。

### <a name="postgressql"></a>PostgresSQL

開放原始碼資料庫 Postgres 可在 DSVM 上使用，且服務正在執行中，並已完成 initdb。 您必須建立資料庫和使用者。 如需詳細資訊，請參閱 [PostgresSQL 文件](https://www.postgresql.org/docs/)。  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL 是一個圖形化 SQL 用戶端，可連線至多種不同的資料庫 (包括 SQL Server、PostgresSQL 及 MySQL)，並執行 SQL 查詢。 您可以使用桌面圖示，從圖形化桌面工作階段 (例如，透過 X2Go 用戶端) 執行 SQuirreL SQL。 或者，您可以在殼層中使用下列命令來執行用戶端：

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh    /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

第一次使用之前，請設定驅動程式和資料庫別名。 JDBC 驅動程式位於 /usr/share/java/jdbcdrivers 中。

如需詳細資訊，請參閱 [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)。

### <a name="command-line-tools-for-accessing-sql-server"></a>用來存取 SQL Server 的命令列工具

SQL Server 的 ODBC 驅動程式封裝也隨附兩個命令列工具：

* **bcp**: bcp 工具在 SQL Server 實體和使用者指定格式的資料檔之間批次複製資料。 您可以使用 bcp 工具將大量新資料列匯入 SQL Server 資料表中，或是將資料表的資料匯出成資料檔案。 若要將資料匯入資料表中，您必須使用為該資料表建立的格式檔案。 或者，您必須了解資料表的結構，以及適用於其資料行的資料類型。

  如需詳細資訊，請參閱[使用 bcp 進行連線](https://msdn.microsoft.com/library/hh568446.aspx)。

* **sqlcmd**:您可以使用 sqlcmd 實用程式在指令提示符下輸入 Transact-SQL 語句、系統過程和文本檔。 sqlcmd 公用程式會使用 ODBC 執行 Transact-SQL 批次。

  如需詳細資訊，請參閱[使用 sqlcmd 進行連線](https://msdn.microsoft.com/library/hh568447.aspx)。

  > [!NOTE]
  > 此工具在 Linux 和 Windows 平台之間有一些差異。 如需詳細資訊，請參閱上述文件。

### <a name="database-access-libraries"></a>資料庫存取程式庫

用於資料庫存取的程式庫可在 R 和 Python 中使用：

* 在 R 中，您可以使用 RODBC 套件來查詢資料庫伺服器或在其上執行 SQL 陳述式。
* 在 Python 中，pyodbc 程式庫可讓您存取以 ODBC 作為基礎層的資料庫。

## <a name="azure-tools"></a>Azure 工具

DSVM 上安裝了下列 Azure 工具：

* **Azure CLI**:可以使用 Azure 中的命令列介面透過 shell 命令建立和管理 Azure 資源。 若要開啟 Azure Tools，請輸入 **azure help**。 如需詳細資訊，請參閱 [Azure CLI 文件頁面](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。
* **Azure 儲存資源管理員**:Azure 儲存資源管理員是一種圖形工具,可用於瀏覽已儲存在 Azure 儲存帳戶中的物件,以及將數據上載和從 Azure Blob 上載和下載。 您可以從桌面捷徑圖示存取儲存體總管。 您也可以輸入 **StorageExplorer**，從殼層提示字元加以開啟。 您必須從 X2Go 用戶端登入，或是已設定 X11 轉送。
* **Azure 函式庫**:以下函式庫預安裝在 DSVM 上:
  
  * **Python**: Python 中與 Azure 相關的函式庫是*azure、azureml、pydocumentdb*與*pyodbc*。 *azure* *pydocumentdb* 透過前三個程式庫，您可以存取 Azure 儲存體服務、Azure Machine Learning 和 Azure Cosmos DB (Azure 上的 NoSQL 資料庫)。 第四個程式庫 pyodbc (以及 Microsoft ODBC Driver for SQL Server) 可讓您使用 ODBC 介面，從 Python 中存取 SQL Server、Azure SQL Database 和 Azure SQL 資料倉儲。 輸入 **pip list** 以查看所有列出的程式庫。 請務必在 Python 2.7 和 3.5 的環境中執行此命令。
  * **R**: R 中的 Azure 相關函式庫是 AzureML 和 RODBC。
  * **Java**:Azure Java 庫的清單可以在 DSVM 上的目錄 /dsvm/sdk/AzureSDKJAVA 中找到。 關鍵的程式庫為 Azure 儲存體和管理 API、Azure Cosmos DB 和 JDBC Driver for SQL Server。  

可以從預先安裝的 Firefox 瀏覽器存[取 Azure 門戶](https://portal.azure.com)。 在 Azure 入口網站中，您可以建立、管理和監視 Azure 資源。

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning 是完全受控的雲端服務，可用來建置、部署及共用預測性分析解決方案。 從 Azure 機器學習工作室(經典)構建實驗和模型。 要從 DSVM 上的 Web 瀏覽器存取 Azure 機器學習,請造[訪 Microsoft Azure 機器學習](https://studio.azureml.net)。

登錄到 Azure 機器學習工作室(經典版)後,可以使用實驗畫布為機器學習演演演算法構建邏輯流。 您也可以存取 Azure Machine Learning 上裝載的 Jupyter Notebook。 該筆記本可以無縫地與 Azure 機器學習工作室(經典)中的實驗配合使用。 

請將您建置的機器學習模型包裝在 Web 服務介面中，使其能夠運作。 讓機器學習模型能夠運作，使用任何語言撰寫的用戶端就能夠從這些模型叫用預測。 如需詳細資訊，請參閱 [機器學習文件](https://azure.microsoft.com/documentation/services/machine-learning/)。

您可以也在 DSVM 上以 R 或 Python 建置模型，然後將其部署在 Azure Machine Learning 的生產環境中。 Microsoft 已安裝 R (**AzureML**) 和 Python (**azureml**) 的程式庫來支援這項功能。

如需如何將 R 和 Python 的模型部署到 Azure Machine Learning 的相關資訊，請參閱[您可以在資料科學虛擬機器上做的十件事](vm-do-ten-things.md)。

> [!NOTE]
> [您可以在資料科學虛擬機器上做的十件事](vm-do-ten-things.md)中的指示是針對 Windows 版的 DSVM 而撰寫的。 但關於將模型部署到 Azure Machine Learning 的資訊，也適用於 Linux DSVM。

## <a name="machine-learning-tools"></a>機器學習工具

DSVM 隨附了一些已預先編譯且預先安裝在本機上的機器學習工具和演算法。 其中包括：

* **微軟認知工具組**:一個深度學習工具組。
* **Vowpal Wabbit**：快速線上學習演算法。
* **XGBoost**:一種提供優化、提升的樹演演演算法的工具。
* **Python**：Anaconda Python 會與含有像是 Scikit-learn 的程式庫的機器學習演算法進行配套。 您可以使用 `pip install` 程式庫。
* **R**: 機器學習函數的豐富庫可用於 R. 預安裝的庫,包括 lm、glm、隨機林和 rpart。 您可以執行 `install.packages(<lib name>)` 來安裝其他程式庫。

後續章節會更詳細地討論 Microsoft Cognitive Toolkit、Vowpal Wabbit 和 XGBoost。

### <a name="microsoft-cognitive-toolkit"></a>Microsoft 辨識工具組

Microsoft Cognitive Toolkit 是開放原始碼的深度學習工具組。 它是一項命令列工具 (CNTK)，且已在 PATH 中。

若要執行基本範例，請在殼層中執行下列命令：

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

如需詳細資訊，請參閱 [GitHub CNTK 存放庫](https://github.com/Microsoft/CNTK)和 [CNTK Wiki](https://github.com/Microsoft/CNTK/wiki)。

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit 是一個機器學習系統，會使用線上、雜湊，allreduce、簡化、learning2search、主動和互動式學習等技術。

若要在基本範例上執行此工具，請執行下列命令：

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vowpal Wabbit 示範目錄包含其他更大的示範。 如需 Vowpal Wabbit 的詳細資訊，請參閱 [GitHub Vowpal Wabbit 存放庫](https://github.com/JohnLangford/vowpal_wabbit)和 [Vowpal Wabbit Wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)。

### <a name="xgboost"></a>XGBoost

XGBoost 是針對促進式決策 (樹) 演算法而設計和最佳化的程式庫。 XGBoost 程式庫的目標是要將機器的運算限制推到所需的極致，以提供可調整、可攜且精確的大規模樹狀促進式決策。

XGBoost 以命令列和 R 程式庫的形式提供。

若要在 R 中使用 XGBoost 程式庫，請啟動互動式 R 工作階段 (在殼層中輸入 **R**)，然後載入程式庫。

以下是您可在 R 提示字元中執行的簡單範例︰

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

若要執行 XGBoost 命令列，請在殼層中執行下列命令︰

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.model 檔案會寫入至指定的目錄。 如需與 GitHub 上的這個示範範例有關的資訊，請參閱[二元分類](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)。

如需 XGBoost 的詳細資訊，請參閱 [XGBoost 文件](https://xgboost.readthedocs.org/en/latest/)和 [XGBoost GitHub 存放庫](https://github.com/dmlc/xgboost)。

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) 會使用 GUI 型資料探索和模型化。 Rattle：
- 顯示資料的統計和視覺化摘要。
- 轉換已可建立模型的資料。
- 從資料建置不受監督和受監督的模型。
- 以圖形方式顯示模型的效能。
- 為新的資料集評分。
- 產生 R 程式碼。
- 複寫 UI 中可直接在 R 中執行的作業，或作為進一步分析的起點。

若要執行 Rattle，您必須登入圖形化桌面工作階段。 在終端機中輸入 **R**，以開啟 R 環境。 在 R 提示字元中輸入下列命令：

```R
library(rattle)
rattle()
```

此時會開啟具有一組索引標籤的圖形化介面。 請使用下列快速入門步驟，在 Rattle 中使用範例天氣資料集並建置模型。 在部分步驟中，系統會提示您自動安裝並載入一些系統上還沒有的必要 R 套件。

> [!NOTE]
> 如果您無權存取系統目錄 (預設) 中的套件，您可能會在 R 主控台視窗上看到提示，詢問是否要將套件安裝至您的個人程式庫。 如果您看到這些提示，請輸入 **y**。

1. 選擇 **"執行**"
1. 此時會出現一個對話方塊，提示您載入範例天氣資料集。 請選取 [是]**** 以載入範例。
1. 選取 [模型]**** 索引標籤。
1. 選取 [執行]**** 以建置決策樹。
1. 選取 [繪圖]**** 以顯示決策樹。
1. 選取 [樹系]**** 選項，然後選取 [執行]**** 以建置隨機樹系。
1. 選取 [評估]**** 索引標籤。
1. 選取 [風險]**** 選項，然後選取 [執行]**** 以顯示兩個 [風險 (累積)]**** 效能繪圖。
1. 選取 [記錄]**** 索引標籤，以顯示針對先前的作業產生的 R 程式碼。 (由於 Rattle 當前版本中存在 Bug,因此必須在日**#** 誌文本中**匯出此日誌**前面插入一個字元。
1. 選取 [匯出]**** 按鈕，將名為 weather_script.R** 的 R 指令檔儲存到主資料夾。

您可以結束 Rattle 和 R。現在，您可以修改產生的 R 指令碼。 或者，您可以照原樣使用，並且隨時執行，以便重複執行在 Rattle UI 中完成的所有動作。 這是特別適合 R 初學者的方法，他們將可在簡單的圖形化介面中快速進行分析和機器學習，同時自動產生 R 程式碼來修改或學習。

## <a name="next-steps"></a>後續步驟

仍有其他問題嗎？ 請考慮建立[支援票證](https://azure.microsoft.com/support/create-ticket/)。