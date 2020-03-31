---
title: 在 Windows 上流覽資料和模型
titleSuffix: Azure Data Science Virtual Machine
description: 在 Windows 資料科學虛擬機器上執行資料探索和建模任務。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 147f43148c0f804adf70f1a792ba1b8a772fdae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294482"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>您可以在 Windows Data Science Virtual Machine 上做的十件事

Windows 資料科學虛擬機器 （DSVM） 是一個強大的資料科學開發環境，您可以在其中執行資料探索和建模任務。 環境已經構建並捆綁了幾個流行的資料分析工具，這些工具便於開始對本地、雲或混合部署的分析。 

DSVM 與 Azure 服務密切合作。 它可以讀取和處理已存儲在 Azure 上的資料，位於 Azure SQL 資料倉儲、Azure 資料湖、Azure 存儲或 Azure 宇宙 DB 中。 它還可以利用其他分析工具，如 Azure 機器學習和 Azure 資料工廠。

在本文中，您將學習如何使用 DSVM 執行資料科學任務並與其他 Azure 服務進行交互。 以下是您可以在 DSVM 上做的一些事：

- 使用 Microsoft 機器學習伺服器和 Python 在 DSVM 上探索資料並開發模型。
- 使用 Jupyter 筆記本使用 Python 2、Python 3 和 Microsoft R 在瀏覽器中試驗資料（Microsoft R 是專為性能設計的企業版 R）。
- 在 Azure 機器學習上部署通過 R 和 Python 構建的模型，以便用戶端應用程式可以使用簡單的 Web 服務介面訪問模型。
- 使用 Azure 門戶或 PowerShell 管理 Azure 資源。
- 通過在 DSVM 上創建 Azure 檔共用作為可裝載磁碟機，擴展存儲空間並在整個團隊中共用大型資料集/代碼。
- 使用 GitHub 與您的團隊共用代碼。 使用預先安裝的 Git 用戶端（Git Bash 和 Git GUI）訪問存儲庫。
- 訪問 Azure 資料和分析服務，如 Azure Blob 存儲、Azure 資料湖、Azure HDInsight（Hadoop）、Azure 宇宙資料庫、Azure SQL 資料倉儲和 Azure SQL 資料庫。
- 使用在 DSVM 上預先安裝的 Power BI 桌面實例生成報表和儀表板，並將其部署到雲中。
- 動態擴展 DSVM 以滿足專案需求。
- 在虛擬機器上安裝其他工具。   

> [!NOTE]
> 本文中列出的許多資料存儲和分析服務會收取額外的使用費用。 有關詳細資訊，請參閱[Azure 定價](https://azure.microsoft.com/pricing/)頁。
> 
> 

## <a name="prerequisites"></a>Prerequisites

* 您需要 Azure 訂用帳戶。 您可以 [註冊免費試用](https://azure.microsoft.com/free/)。
* 在 Azure 門戶上預配資料科學虛擬機器的說明在["創建虛擬機器](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)"中可用。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>使用 Microsoft 機器學習伺服器探索資料並開發模型
您可以使用 R 和 Python 等語言在 DSVM 上進行資料分析。

對於 R，您可以使用在開始功能表或桌面上可以找到的像 RStudio 這樣的 IDE。 或者，您可以使用 R 工具視覺化工作室。 Microsoft 在開源 CRAN R 之上提供了其他庫，以實現可擴展的分析，並能夠分析大於並行分塊分析中允許的記憶體大小的資料。 

針對 Python，您可以使用 Visual Studio Community Edition 這類已預先安裝「適用於 Visual Studio 的 Python 工具」(PTVS) 延伸模組的 IDE。 預設情況下，在 PTVS 上只配置根 Conda 環境 Python 3.6。 要啟用 Anaconda Python 2.7，請執行以下步驟：

1. 通過訪問**工具** > **Python 工具** > **Python 環境**，然後在視覺化工作室社區版中選擇 **"自訂"，** 為每個版本創建自訂環境。
1. 為 Anaconda Python 2.7 提供說明並將環境首碼路徑設置為**c：\anaconda\envs_python2。**
1. 選擇 **"自動檢測** > **應用"** 以保存環境。

有關如何創建 Python 環境的更多詳細資訊，請參閱[PTVS 文檔](https://aka.ms/ptvsdocs)。

現在，您已設置為創建新的 Python 專案。 轉到 **"檔** > **新專案** > **Project****Python**Python"並選擇要構建的 Python 應用程式的類型。 >  通過按右鍵**Python 環境**，然後選擇 **"添加/刪除 Python 環境**"，可以將當前專案的 Python 環境設置為所需的版本（Python 2.7 或 3.6）。 您可以在[產品文檔中](https://aka.ms/ptvsdocs)找到有關使用 PTVS 的更多資訊。

## <a name="use-jupyter-notebooks"></a>使用 Jupyter Notebook
Jupyter 筆記本提供基於瀏覽器的 IDE，用於資料探索和建模。 您可以在 Jupyter 筆記本中使用 Python 2、Python 3 或 R（開源和微軟 R 伺服器）。

要啟動猶太筆記本，請選擇 **"開始"** 功能表或桌面上的**Jupyter 筆記本**圖示。 在 DSVM 命令提示符中，還可以從具有```jupyter notebook```現有筆記本的目錄中或要創建新筆記本的目錄中運行該命令。  

啟動 Jupyter 後，應看到一個目錄，其中包含一些預打包到 DSVM 中的示例筆記本。 現在您可以：

* 選擇筆記本以查看代碼。
* 通過選擇 Shift_Enter 運行每個儲存格。
* 通過選擇 **"儲存格** > **運行"運行**整個筆記本。
* 通過選擇 Jupyter 圖示（左上角），選擇右側的 **"新建"** 按鈕，然後選擇筆記本語言（也稱為內核），創建新筆記本。   

> [!NOTE]
> 目前，支援聚居器中的 Python 2.7、Python 3.6、R、Julia 和 PySpark 內核。 R 內核支援開源 R 和 Microsoft R 中的程式設計。   
> 
> 

當您在筆記本中時，可以使用您選擇的庫來流覽資料、構建模型和測試模型。

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>使用 Azure 機器學習訓練和部署模型
構建並驗證模型後，下一步通常是將其部署到生產中。 此步驟允許用戶端應用程式即時或批次處理模式調用模型預測。 Azure Machine Learning 提供一個機制來實作以 R 或 Python 建置的模型。

在 Azure 機器學習中操作模型時，將公開 Web 服務。 它允許用戶端進行 REST 調用，這些調用通過輸入參數，並從模型接收預測作為輸出。

### <a name="build-and-operationalize-python-models"></a>構建和操作 Python 模型
下面是在 Python Jupyter 筆記本中開發的代碼片段，該代碼片段使用 Scikit 學習庫構建一個簡單的模型：

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

用於將 Python 模型部署到 Azure 機器學習的方法將模型的預測包裝到函數中，並將其與預先安裝的 Azure 機器學習 Python 庫提供的屬性修飾。 這些屬性工作表示 Azure 機器學習工作區 ID、API 金鑰以及輸入和返回參數。  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

用戶端現在可以呼叫 Web 服務。 方便包裝器構造 REST API 請求。 下面是使用 Web 服務的示例代碼：

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> 目前，Azure 機器學習庫僅在 Python 2.7 上受支援。   
> 
> 

### <a name="build-and-operationalize-r-models"></a>構建和操作 R 模型
您可以將基於資料科學虛擬機器或其他地方的 R 模型部署到 Azure 機器學習中，其方式與對 Python 的完成方式類似。 以下為其步驟：

1. 創建設置.json 檔以提供工作區 ID 和身份驗證權杖。 
2. 為模型的預測函數編寫包裝器。
3. 在```publishWebService```Azure 機器學習庫中調用以傳遞函數包裝器。  

使用以下過程和程式碼片段在 Azure 機器學習中設置、構建、發佈和使用模型作為 Web 服務。

#### <a name="set-up"></a>設定

在主目錄下稱為```.azureml```目錄下創建 settings.json 檔。 輸入 Azure 機器學習工作區中的參數。

下面是設置.json 檔結構：

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>以 R 建置模型並在 Azure Machine Learning 中發佈

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>取用 Azure Machine Learning 中部署的模型
要使用來自用戶端應用程式的模型，請使用 Azure 機器學習庫按名稱查找已發佈的 Web 服務。 使用`services`API 呼叫確定終結點。 然後您只要呼叫 `consume` 函式並傳入要預測的資料框架。

使用以下代碼使用作為 Azure 機器學習 Web 服務發佈的模型：

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

在[機器學習工作室中](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)查看有關 R 包的詳細資訊。

## <a name="manage-azure-resources"></a>管理 Azure 資源
DSVM 不僅允許您在虛擬機器上本地構建分析解決方案。 它還允許您訪問 Azure 雲平臺上的服務。 Azure 提供了多個計算、存儲、資料分析和其他服務，可以從 DSVM 管理和訪問這些服務。

若要管理 Azure 訂閱和雲資源，有兩個選項：
+ 使用瀏覽器並轉到 Azure[門戶](https://portal.azure.com)。

+ 使用 PowerShell 腳本。 從桌面上的快捷方式或 **"開始"** 功能表運行 Azure PowerShell。 有關詳細資訊，請參閱[Microsoft Azure PowerShell 文檔](../../powershell-azure-resource-manager.md)。 

## <a name="extend-storage-by-using-shared-file-systems"></a>使用共用檔案系統擴展存儲
資料科學家可以在小組內共用大型資料集、程式碼或其他資源。 DSVM 的可用空間約為 45 GB。 要擴展存儲，可以使用 Azure 檔並將其裝載到一個或多個 DSVM 實例上，或通過 REST API 訪問它。 您還可以使用[Azure 門戶](../../virtual-machines/windows/attach-managed-disk-portal.md)或使用 Azure [PowerShell](../../virtual-machines/windows/attach-disk-ps.md)添加額外的專用資料磁片。 

> [!NOTE]
> Azure 檔共用上的最大空間為 5 TB。 每個檔的大小限制為 1 TB。 

您可以在 Azure PowerShell 中使用此腳本創建 Azure 檔共用：

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

創建 Azure 檔共用後，可以將其裝載到 Azure 中的任何虛擬機器中。 我們建議您將 VM 與存儲帳戶放在同一 Azure 資料中心中，以避免延遲和資料傳輸費用。 下面是 Azure PowerShell 命令，用於在 DSVM 上安裝磁碟機：

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

您現在可以將此磁碟機當作 VM 上的任何一般磁碟機存取。

## <a name="share-code-in-github"></a>在 GitHub 中共用代碼
GitHub 是一個代碼存儲庫，您可以在其中使用開發人員社區共用的技術查找各種工具的代碼示例和源。 它會使用 Git 技術來追蹤和儲存各版本的程式碼檔案。 GitHub 也是一個平臺，您可以在其中創建自己的存儲庫來存儲團隊的共用代碼和文檔、實施版本控制以及控制誰有權存取碼並貢獻代碼。 

如需有關使用 Git 的詳細資訊，請瀏覽 [GitHub 說明頁面](https://help.github.com/) \(英文\)。 您可以使用 GitHub 作為與團隊協作、使用社區開發的代碼以及將代碼貢獻回社區的方法之一。

DSVM 在命令列和 GUI 上裝載了用戶端工具以訪問 GitHub 存儲庫。 與 Git 和 GitHub 一起工作的命令列工具稱為 Git Bash。 Visual Studio 安裝在 DSVM 上，並且具有 Git 擴展。 您可以在 **"開始"** 功能表和桌面上找到這些工具的圖示。

若要從 GitHub 存放庫下載程式碼，您可以使用 ```git clone``` 命令。 例如，要將 Microsoft 發佈的資料科學存儲庫下載到目前的目錄中，可以在 Git Bash 中運行以下命令：

    git clone https://github.com/Azure/DataScienceVM.git

您可以在 Visual Studio 中執行相同的複製作業。 以下螢幕截圖演示如何在視覺化工作室中訪問 Git 和 GitHub 工具：

![顯示 GitHub 連線的 Visual Studio 螢幕擷取畫面](./media/vm-do-ten-things/VSGit.PNG)

您可以從github.com上可用的資源找到有關使用 Git 處理 GitHub 存儲庫的詳細資訊。 [功能提要](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) 是有用的參考資料。

## <a name="access-azure-data-and-analytics-services"></a>訪問 Azure 資料和分析服務
### <a name="azure-blob-storage"></a>Azure Blob 儲存體
Azure Blob 存儲是一種可靠、經濟的雲存儲服務，適用于大大小小資料。 本節介紹如何將資料移動到 Blob 存儲並訪問存儲在 Azure Blob 中的資料。

#### <a name="prerequisites"></a>Prerequisites

* 從[Azure 門戶](https://portal.azure.com)創建 Azure Blob 存儲帳戶。

   ![Azure 門戶中存儲帳戶創建過程的螢幕截圖](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* 確認預先安裝命令列 AzCopy 工具： ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```。 包含 azcopy.exe 的目錄已位於您的 PATH 環境變數上，因此您可以避免在運行此工具時鍵入完整的命令路徑。 有關 AzCopy 工具的詳細資訊，請參閱[AzCopy 文檔](../../storage/common/storage-use-azcopy.md)。
* 啟動 Azure 儲存體總管工具。 你可以從[存儲資源管理器網頁](https://storageexplorer.com/)下載它。 

   ![訪問存儲帳戶的 Azure 存儲資源管理器的螢幕截圖](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>將資料從 VM 移動到 Azure Blob：AzCopy

要在本地檔和 Blob 存儲之間移動資料，可以在命令列或 PowerShell 中使用 AzCopy：

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

將**C：_myfolder**替換為存儲檔的路徑、使用 Blob**存儲帳戶名稱的存儲帳戶**、帶有容器名稱**的容器**以及使用 Blob 存儲訪問金鑰的**存儲帳戶金鑰**。 您可以在[Azure 門戶](https://portal.azure.com)中找到存儲帳戶憑據。

在 PowerShell 或從命令提示符中運行 AzCopy 命令。 下面是 AzCopy 命令的一些示例用法：

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

運行要複製到 Azure Blob 的 AzCopy 命令後，檔將顯示在 Azure 存儲資源管理器中。

![存儲帳戶的螢幕截圖，顯示上傳的 CSV 檔](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>將資料從 VM 移動到 Azure Blob：Azure 存儲資源管理器

您還可以使用 Azure 存儲資源管理器從 VM 中的本地檔上載資料：

* 要將資料上載到容器，請選擇目標容器並選擇 **"上載**"按鈕。![Azure 存儲資源管理器中上載按鈕的螢幕截圖](./media/vm-do-ten-things/storage-accounts.png)
* 選擇 **"檔"** 框右側的省略號 （**...），** 選擇一個或多個檔從檔案系統上傳，然後選擇 **"上傳**"以開始上傳檔。!["上傳檔"對話方塊的螢幕截圖](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>從 Azure Blob 讀取資料：機器學習讀取器模組

在 Azure 機器學習工作室中，可以使用"導入資料"模組從 Blob 中讀取資料。

![Machine Learning Studio 中的匯入資料模組的螢幕擷取畫面](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>從 Azure Blob 讀取資料：Python ODBC

您可以使用 BlobService 庫直接從 Jupyter 筆記本或 Python 程式中的 Blob 讀取資料。

首先，導入所需的包：

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

然後，插入 Blob 存儲帳戶憑據並從 Blob 讀取資料：

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

資料作為資料框讀取：

![前 10 個資料列的螢幕擷取畫面](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure 資料湖存儲是大資料分析工作負載的超大規模存儲庫，與 Hadoop 分散式檔案系統 （HDFS） 相容。 它可以與 Hadoop、Spark 及 Azure Data Lake Analytics 搭配運作。 在本節中，您將瞭解如何使用 Azure 資料湖分析將資料移動到 Azure 資料湖存儲中並運行分析。

#### <a name="prerequisites"></a>Prerequisites

* 在[Azure 門戶](https://portal.azure.com)中創建 Azure 資料湖分析實例。

   ![從 Azure 門戶創建資料湖分析實例的螢幕截圖](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [用於視覺化工作室外掛程式的 Azure 資料湖和流分析工具](https://www.microsoft.com/download/details.aspx?id=49504)已安裝在虛擬機器上的視覺化工作室社區版中。 啟動 Visual Studio 並登錄到 Azure 訂閱後，應在 Visual Studio 的左側面板中查看 Azure 資料分析帳戶和存儲。

   ![視覺化工作室中資料湖工具外掛程式的螢幕截圖](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>將資料從 VM 移動到資料湖：Azure 資料湖資源管理器

您可以使用 Azure 資料湖資源管理器將資料[從虛擬機器中的本地檔上載到資料湖存儲](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。

還可以構建資料管道，以便使用[Azure 資料工廠](https://azure.microsoft.com/services/data-factory/)運算元據移動到或從 Azure 資料湖移動。 [本文](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)將指導您完成構建資料管道的步驟。

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>將資料從 Azure blob 讀取到資料湖：U-SQL

如果資料駐留在 Azure Blob 存儲中，則可以直接從 U-SQL 查詢中的 Azure Blob 讀取資料。 在撰寫 U-SQL 查詢之前，請確保 Blob 存儲帳戶已連結到 Azure 資料湖實例。 轉到 Azure 門戶，查找 Azure 資料湖分析儀表板，選擇 **"添加資料來源**"，選擇**Azure 存儲**的存儲類型，然後插入 Azure 存儲帳戶名稱和金鑰。 然後，您可以引用存儲在存儲帳戶中的資料。

![添加資料來源對話方塊的螢幕截圖](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

在 Visual Studio 中，可以從 Blob 存儲讀取資料、運算元據、設計功能以及將結果資料發送到 Azure 資料湖或 Azure Blob 存儲。 在 Blob 存儲中參考資料時，請使用**wasb://**。 在 Azure 資料湖中參考資料時，請使用**swbhdfs://。**

您可以在視覺化工作室中使用以下 U-SQL 查詢：

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

將查詢提交到伺服器後，將顯示作業的狀態。

![作業狀態圖的螢幕截圖](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>查詢資料湖中的資料：U-SQL

在 Azure 資料湖中引入資料集後，可以使用[U-SQL 語言](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)來查詢和流覽資料。 U-SQL 語言與 T-SQL 類似，但結合了 C# 中的一些功能，以便使用者可以編寫自訂模組和使用者定義的函數。 您可以在上一個步驟中使用指令碼。

將查詢提交到伺服器後，tripdata_summary。CSV 顯示在 Azure 資料湖資源管理器中。 您可以通過按右鍵檔來預覽資料。

![資料湖資源管理器中 CSV 檔的螢幕截圖](./media/vm-do-ten-things/USQL_create_summary.png)

將顯示檔資訊：

![檔案摘要資訊的螢幕擷取畫面](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop 集群
Azure HDInsight 是雲中的託管 Apache Hadoop、Spark、HBase 和 Storm 服務。 您可以使用資料科學虛擬機器中的 Azure HDInsight 群集輕鬆使用。

#### <a name="prerequisites"></a>Prerequisites

* 從[Azure 門戶](https://portal.azure.com)創建 Azure Blob 存儲帳戶。 此儲存體帳戶用來儲存 HDInsight 叢集的資料。

   ![從 Azure 門戶創建存儲帳戶的螢幕截圖](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* 從[Azure 門戶](../team-data-science-process/customize-hadoop-cluster.md)自訂 Azure HDInsight Hadoop 群集。
  
   在創建 HDInsight 群集時連結其創建的存儲帳戶。 此儲存體帳戶用於存取可以在叢集內處理的資料。

   ![使用 HDInsight 群集連結創建的存儲帳戶的選擇](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* 在創建群集後啟用對群集頭節點的遠端桌面訪問。 請記住您在此處指定的遠端存取憑據，因為在後續過程中需要這些憑據。

   ![遠端桌面按鈕，用於啟用對 HDInsight 群集的遠端存取](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* 建立 Azure Machine Learning 工作區。 您的機器學習實驗存儲在此機器學習工作區中。 選擇門戶中的突出顯示選項，如以下螢幕截圖所示：

   ![建立 Azure Machine Learning 工作區](./media/vm-do-ten-things/Create_ML_Space.PNG)

* 輸入工作區的參數。

   ![輸入 Machine Learning 工作區參數](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* 使用 IPython 筆記本上傳資料。 導入所需的包，插入憑據，在存儲帳戶中創建資料庫，然後將資料載入到 HDI 群集中。

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

或者，您可以按照[本演練](../team-data-science-process/hive-walkthrough.md)將 NYC 計程車資料上載到 HDI 群集。 主要步驟包括：
  
* 使用 AzCopy 將壓縮的 CSV 從公共 blob 下載到本地資料夾。
* 使用 AzCopy 將解壓縮的 CSV 從本地資料夾上載到 HDI 群集。
* 登錄到 Hadoop 群集的頭節點，並為探索性資料分析做好準備。

將資料載入到 HDI 群集後，可以在 Azure 存儲資源管理器中檢查資料。 nytaxidb 資料庫已在 HDI 群集中創建。

#### <a name="data-exploration-hive-queries-in-python"></a>資料探索：Python 中的 Hive 查詢

由於資料位於 Hadoop 群集中，因此可以使用 pyodbc 包連接到 Hadoop 群集，並通過使用 Hive 進行探索和功能工程，從而連接到 Hadoop 群集和查詢資料庫。 您可以查看在先決條件步驟中創建的現有表。

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![檢視現有的表格](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

讓我們看一下每個月的記錄筆數以及車程資料表中已付小費或未付小費的頻率：

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![每個月的記錄數繪圖](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![小費頻率繪圖](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

您還可以計算取件位置和下下車位置之間的距離，然後將其與行程距離進行比較。

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![取件表和下拉表的頂部行](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![行程距離的拾取/下降距離圖](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

現在，讓我們準備一組向下採樣（1%）的資料集進行建模。 您可以在機器學習讀取器模組中使用此資料。

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

現在，將聯接的內容插入到前面的內部表中。

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

一段時間後，您可以看到資料已載入到 Hadoop 群集中：

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![資料表中的頂端資料列](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-azure-machine-learning-studio-classic-reader-module"></a>使用 Azure 機器學習工作室（經典）：讀取資料模組從 HDI 讀取資料

您還可以使用 Azure 機器學習工作室（經典）中的讀取器模組訪問 Hadoop 群集中的資料庫。 插入 HDI 群集和 Azure 存儲帳戶的憑據，以便使用 HDI 群集中的資料庫生成機器學習模型。

![讀取器模組屬性](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

然後，您可以查看評分資料集：

![檢視已評分的資料集](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL 資料倉儲和資料庫
Azure SQL 資料倉儲是一個彈性資料倉儲，作為具有企業級 SQL Server 體驗的服務。

您可以按照[本文](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)中的說明預配 Azure SQL 資料倉儲。 預配 SQL 資料倉儲後，可以使用[本演練](../team-data-science-process/sqldw-walkthrough.md)使用 SQL 資料倉儲中的資料執行資料上載、流覽和建模。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB 是雲端中的一種 NoSQL 資料庫。 您可以使用它處理 JSON 等文檔，以及存儲和查詢文檔。

使用以下先決條件步驟從 DSVM 訪問 Azure 宇宙 DB：

1. Azure 宇宙 DB Python SDK 已安裝在 DSVM 上。 要更新它，請```pip install pydocumentdb --upgrade```從命令提示符運行。
2. 從[Azure 門戶](https://portal.azure.com)創建 Azure Cosmos 資料庫帳戶和資料庫。
3. 從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=53595)下載 Azure 宇宙 DB 資料移轉工具，然後提取到您選擇的目錄。
4. 將存儲在[公共 Blob](https://cahandson.blob.core.windows.net/samples/volcano.json)中的 JSON 資料（火山資料）導入 Azure Cosmos DB，使用以下命令參數到遷移工具。 （使用安裝 Azure Cosmos DB 資料移轉工具的目錄中的 dtui.exe。使用以下參數輸入源和目標位置：
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

導入資料後，您可以轉到 Jupyter 並打開名為*DocumentDBSample*的筆記本。 它包含 Python 代碼來訪問 Azure Cosmos DB 並執行一些基本查詢。 您可以通過訪問服務[的文檔頁](https://docs.microsoft.com/azure/cosmos-db/)瞭解有關 Azure Cosmos DB 的更多詳細資訊。

## <a name="use-power-bi-reports-and-dashboards"></a>使用 Power BI 報表和儀表板 
您可以在 Power BI 桌面中從前面的 Azure Cosmos DB 示例視覺化火山 JSON 檔，以獲得對資料的直觀見解。 在 [Power BI 文章](../../cosmos-db/powerbi-visualize.md)中可找到詳細的步驟。 高階步驟如下：

1. 開啟 Power BI Desktop 並選取 [取得資料]****。 將 URL 指定`https://cahandson.blob.core.windows.net/samples/volcano.json`為： 。
2. 您應該會看到作為清單導入的 JSON 記錄。 將清單轉換為表，以便 Power BI 可以使用它。
4. 通過選擇展開（箭頭）圖示展開列。
5. 請注意，該位置是 **"記錄"** 欄位。 展開記錄，然後只選取座標。 **座標**是清單列。
6. 添加新列以將清單座標列轉換為逗號分隔**的 LatLong**列。 使用 公式```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```將座標清單欄位中的兩個元素串聯。
7. 將 **"高程"** 列轉換為十進位，然後選擇"**關閉****"和"應用**"按鈕。

您可以粘貼以下代碼，而不是上述步驟。 它編寫出 Power BI 中高級編輯器中使用的步驟，以查詢語言編寫資料轉換。

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

您的 Power BI 資料模型中現在會有資料。 Power BI 桌面實例應如下所示：

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

您可以使用資料模型開始構建報表和視覺化效果。 您可以按照此 Power [BI 文章中](../../cosmos-db/powerbi-visualize.md#build-the-reports)的步驟生成報表。

## <a name="scale-the-dsvm-dynamically"></a>動態擴展 DSVM 
您可以向上和向下擴展 DSVM 以滿足專案的需求。 如果晚上或週末不需要使用 VM，則可以從[Azure 門戶](https://portal.azure.com)關閉 VM。

> [!NOTE]
> 如果僅對 VM 上的作業系統使用關機按鈕，則會產生計算費用。  
> 
> 

您可能需要處理一些大規模分析，並需要更多的 CPU、記憶體或磁片容量。 如果是這樣，您可以在 CPU 內核、基於 GPU 的深入學習實例、記憶體容量和磁片類型（包括固態磁碟機）方面找到多種 VM 大小的選擇，以滿足您的計算和預算需求。 VM 的完整清單及其每小時計算定價可在[Azure 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)頁上找到。

同樣，您對 VM 處理容量的需求可能會減少。 （例如：將主要工作負載移動到 Hadoop 或 Spark 群集。然後，可以從[Azure 門戶](https://portal.azure.com)向下縮小群集，然後轉到 VM 實例的設置。 

## <a name="add-more-tools"></a>添加更多工具
DSVM 中預先構建的工具可以解決許多常見的資料分析需求。 這樣可以節省您的時間，因為您不必逐個安裝和配置環境。 它還可以節省您的資金，因為您只支付您使用的資源。

您可以使用本文仲介紹的其他 Azure 資料和分析服務來增強分析環境。 在某些情況下，您可能需要其他工具，包括一些專有合作夥伴工具。 在虛擬機器上具有完全的管理存取權限，可以安裝所需的新工具。 您也可以在 Python 和 R 中安裝其他未預先安裝的封裝。 對於 Python，可以使用 或```conda``````pip```。 對於 R，您可以在```install.packages()```R 主控台中使用，或使用 IDE 並選擇**包** > **安裝包**。

## <a name="deep-learning"></a>深入學習

除了基於框架的示例之外，您還可以獲得一組已在 DSVM 上驗證的綜合演練。 這些演練可説明您在圖像和文本/語言理解等領域快速啟動深度學習應用程式的開發。   


- [跨不同框架運行神經網路](https://github.com/ilkarman/DeepLearningFrameworks)：本演練演示如何將代碼從一個框架遷移到另一個框架。 它還演示如何比較跨框架的模型和運行時性能。 

- [建置端對端解決方案在影像內偵測產品的操作說明指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：影像偵測是一種可在影像內找出並分類物體的技術。 該技術有可能在許多現實生活中的業務領域帶來巨大的回報。 例如，零售商可以利用這項技術來判斷客戶從貨架上挑選什麼產品。 這項資訊進而有助於商店管理產品庫存。 

- [音訊深度學習](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)：本教程演示如何在城市[聲音資料集](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)上訓練音訊事件檢測的深入研究模型。 它還概述了如何使用音訊資料。

- [文字文件分類](https://github.com/anargyri/lstm_han)：本演練演示如何構建和訓練兩個神經網路架構：分層關注網路和長期短期記憶體 （LSTM） 網路。 這些類神經網路會使用深入學習的 Keras API 將文字文件分類。 Keras 是三個最流行的深度學習框架的前端：微軟認知工具組、TensorFlow 和 Theano。

## <a name="summary"></a>總結
本文介紹了在 Microsoft 資料科學虛擬機器上可以執行的一些操作。 您可以執行更多操作，使 DSVM 成為一個有效的分析環境。

