---
title: 探索 Windows 上的資料和模型
titleSuffix: Azure Data Science Virtual Machine
description: 在 Windows 資料科學虛擬機器上執行資料探索和模型化工作。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: tracking-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 7d9aced42efefc8651605be44f0091b2f4f2815e
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959274"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>您可以在 Windows Data Science Virtual Machine 上做的十件事

Windows 資料科學虛擬機器（DSVM）是一個功能強大的資料科學開發環境，您可以在其中執行資料探索和模型化工作。 環境已建立並與數個熱門的資料分析工具配套，可讓您輕鬆地開始進行內部部署、雲端或混合式部署的分析。 

DSVM 與 Azure 服務密切合作。 它可以讀取及處理已儲存在 Azure 上的資料，Azure SQL 資料倉儲、Azure Data Lake、Azure 儲存體或 Azure Cosmos DB。 它也可以利用其他分析工具，例如 Azure Machine Learning 和 Azure Data Factory。

在本文中，您將瞭解如何使用您的 DSVM 來執行資料科學工作，並與其他 Azure 服務互動。 以下是您可以在 DSVM 上做的一些事：

- 使用 Microsoft Machine Learning Server 和 Python，探索資料並在 DSVM 本機開發模型。
- 使用 Jupyter 筆記本，在瀏覽器中使用 Python 2、Python 3 和 Microsoft R 來試驗您的資料（Microsoft R 是專為效能而設計的適用于企業的 R 版本）。
- 在 Azure Machine Learning 上部署透過 R 和 Python 建立的模型，讓用戶端應用程式可以使用簡單的 web 服務介面來存取您的模型。
- 使用 Azure 入口網站或 PowerShell 來管理您的 Azure 資源。
- 藉由在 DSVM 上建立 Azure 檔案儲存體共用作為可掛接的磁片磁碟機，以擴充您的儲存空間，並在整個小組共用大規模的資料集/程式碼。
- 使用 GitHub 與您的小組共用程式碼。 使用預先安裝的 Git 用戶端（Git Bash 和 Git GUI）來存取您的存放庫。
- 存取 Azure 資料和分析服務，例如 Azure Blob 儲存體、Azure Data Lake、Azure Cosmos DB、Azure SQL 資料倉儲和 Azure SQL Database。
- 使用預先安裝在 DSVM 上的 Power BI Desktop 實例來建立報表和儀表板，並將其部署在雲端中。
- 以動態方式調整您的 DSVM，以符合您的專案需求。
- 在您的虛擬機器上安裝其他工具。   

> [!NOTE]
> 這篇文章中所列的許多資料儲存體和分析服務都適用額外的使用費用。 如需詳細資訊，請參閱[Azure 定價](https://azure.microsoft.com/pricing/)頁面。
> 
> 

## <a name="prerequisites"></a>必要條件

* 您需要 Azure 訂用帳戶。 您可以 [註冊免費試用](https://azure.microsoft.com/free/)。
* 您可以在[建立虛擬機器時](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)取得在 Azure 入口網站上布建資料科學虛擬機器的指示。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>使用 Microsoft Machine Learning Server 探索資料和開發模型
您可以使用 R 和 Python 等語言在 DSVM 上進行資料分析。

針對 R，您可以使用可在 [開始] 功能表或桌面上找到的 IDE，例如 RStudio。 或者，您可以使用 Visual Studio R 工具。 Microsoft 在開放原始碼 CRAN R 上提供了額外的程式庫，以啟用可調整的分析，並能夠分析大於平行區塊分析所允許之記憶體大小的資料。 

針對 Python，您可以使用 Visual Studio Community Edition 這類已預先安裝「適用於 Visual Studio 的 Python 工具」(PTVS) 延伸模組的 IDE。 根據預設，只有 Python 3.6 （根 Conda 環境）會在 PTVS 上設定。 若要啟用 Anaconda Python 2.7，請採取下列步驟：

1. 前往 [**工具**] [  >  **python 工具**] [  >  **python 環境**]，然後選取 Visual Studio Community 版本中的 [ **+ 自訂**]，為每個版本建立自訂環境。
1. 提供描述，並將環境前置詞路徑設定為 Anaconda Python 2.7 的**c:\anaconda\envs\python2** 。
1. 選取 [自動偵測] [**套用**]  >  **Apply**以儲存環境。

如需有關如何建立 Python 環境的詳細資訊，請參閱[PTVS 檔](https://aka.ms/ptvsdocs)。

現在您已設定建立新的 Python 專案。 移至**File**  >  [檔案] [**新增**]  >  [**專案**]  >  [**python** ]，然後選取您要建立的 Python 應用程式類型。 您可以在 [ **python 環境**] 上按一下滑鼠右鍵，然後選取 [**新增/移除 python 環境**]，將目前專案的 Python 環境設定為所需的版本（python 2.7 或3.6）。 您可以在[產品檔](https://aka.ms/ptvsdocs)中找到有關使用 PTVS 的詳細資訊。

## <a name="use-jupyter-notebooks"></a>使用 Jupyter Notebook
Jupyter Notebook 提供以瀏覽器為基礎的 IDE，以進行資料探索和模型化。 您可以在 Jupyter 筆記本中使用 Python 2、Python 3 或 R （開放原始碼和 Microsoft R Server）。

若要啟動 Jupyter Notebook，請選取 [**開始**] 功能表或桌面上的 [ **Jupyter Notebook** ] 圖示。 在 DSVM 命令提示字元中，您也可以 ```jupyter notebook``` 從現有的筆記本所在的目錄或您想要建立新筆記本的位置執行命令。  

在您開始 Jupyter 之後，請流覽至目錄，以 `/notebooks` 取得預先封裝到 DSVM 中的範例筆記本。 現在您可以：

* 選取筆記本以查看程式碼。
* 選取 Shift + Enter 來執行每個儲存格。
* 選取 [**儲存格**] [執行] 以執行整個筆記本  >  ** **。
* 選取 Jupyter 圖示（左上角），選取右側的 [**新增**] 按鈕，然後選擇筆記本語言（也稱為核心），以建立新的筆記本。   

> [!NOTE]
> 目前支援 Jupyter 中的 Python 2.7、Python 3.6、R、Julia 和 PySpark 核心。 R 核心支援在開放原始碼 R 和 Microsoft R 中進行程式設計。   
> 
> 

當您在筆記本中時，您可以流覽資料、建立模型，以及使用您選擇的程式庫來測試模型。

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>使用 Azure Machine Learning 來定型和部署模型
在您建立並驗證模型之後，下一個步驟通常是將它部署到生產環境。 此步驟可讓您的用戶端應用程式以即時或批次模式為基礎，叫用模型預測。 Azure Machine Learning 提供一個機制來實作以 R 或 Python 建置的模型。

當您在 Azure Machine Learning 中讓模型時，會公開 web 服務。 它允許用戶端進行 REST 呼叫，傳入輸入參數，並從模型接收預測做為輸出。

### <a name="build-and-operationalize-python-models"></a>建立和讓 Python 模型
以下是在 Python Jupyter 筆記本中開發的程式碼片段，它會使用 Scikit-learn-瞭解程式庫來建立簡單的模型：

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

用來將您的 Python 模型部署至 Azure Machine Learning 的方法會將模型的預測包裝到函式中，並使用預先安裝 Azure Machine Learning Python 程式庫所提供的屬性來裝飾它。 屬性代表您的 Azure Machine Learning 工作區識別碼、API 金鑰，以及輸入和傳回參數。  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

用戶端現在可以呼叫 Web 服務。 便利包裝函式會建立 REST API 的要求。 以下是使用 web 服務的範例程式碼：

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

### <a name="build-and-operationalize-r-models"></a>建立和讓 R 模型
您可以將資料科學虛擬機器或其他位置上建立的 R 模型部署到 Azure Machine Learning，其方式類似于 Python 的執行方式。 以下是步驟：

1. 在檔案上建立 settings.js，以提供您的工作區識別碼和驗證權杖。 
2. 撰寫模型之 predict 函數的包裝函式。
3. ```publishWebService```在 Azure Machine Learning 程式庫中呼叫，以傳入函式包裝函式。  

使用下列程式和程式碼片段，在 Azure Machine Learning 中設定、建立、發行和取用模型做為 web 服務。

#### <a name="set-up"></a>設定

在 ```.azureml``` 您的主目錄下名為的目錄下，建立檔案的 settings.js。 輸入 Azure Machine Learning 工作區中的參數。

以下是檔案結構上的 settings.js：

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
若要從用戶端應用程式取用模型，請使用 Azure Machine Learning 程式庫依名稱查閱已發佈的 web 服務。 使用 `services` API 呼叫來判斷端點。 然後您只要呼叫 `consume` 函式並傳入要預測的資料框架。

使用下列程式碼來取用發佈為 Azure Machine Learning web 服務的模型：

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

如需詳細資訊，請參閱[Machine Learning Studio 中的 R 套件](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)。

## <a name="manage-azure-resources"></a>管理 Azure 資源
DSVM 不只是讓您在虛擬機器上的本機建立分析解決方案。 它也可讓您存取 Azure 雲端平臺上的服務。 Azure 提供數個計算、儲存體、資料分析，以及您可以從 DSVM 管理和存取的其他服務。

若要管理您的 Azure 訂用帳戶和雲端資源，您有兩個選項：
+ 使用您的瀏覽器並移至[Azure 入口網站](https://portal.azure.com)。

+ 使用 PowerShell 腳本。 從桌面上的快捷方式或從 [**開始**] 功能表執行 Azure PowerShell。 如需完整詳細資料，請參閱[Microsoft Azure PowerShell 檔](../../powershell-azure-resource-manager.md)。 

## <a name="extend-storage-by-using-shared-file-systems"></a>使用共用檔案系統擴充儲存體
資料科學家可以在小組內共用大型資料集、程式碼或其他資源。 DSVM 有大約 45 GB 的可用空間。 若要擴充您的儲存體，您可以使用 Azure 檔案儲存體，並將它掛接在一或多個 DSVM 實例上，或透過 REST API 存取。 您也可以使用[Azure 入口網站](../../virtual-machines/windows/attach-managed-disk-portal.md)或使用[Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md)新增額外的專用資料磁片。 

> [!NOTE]
> Azure 檔案儲存體共用上的最大空間為 5 TB。 每個檔案的大小限制為 1 TB。 

您可以在 Azure PowerShell 中使用此腳本來建立 Azure 檔案儲存體共用：

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

既然您已建立 Azure 檔案儲存體共用，您可以將它裝載于 Azure 中的任何虛擬機器。 我們建議您將 VM 放在與儲存體帳戶相同的 Azure 資料中心，以避免延遲和資料傳輸費用。 以下是在 DSVM 上掛接磁片磁碟機的 Azure PowerShell 命令：

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

您現在可以將此磁碟機當作 VM 上的任何一般磁碟機存取。

## <a name="share-code-in-github"></a>在 GitHub 中共用程式碼
GitHub 是一個程式碼存放庫，您可以使用開發人員小組所共用的技術，尋找各種工具的程式碼範例和來源。 它會使用 Git 技術來追蹤和儲存各版本的程式碼檔案。 GitHub 也是一個平臺，您可以在其中建立自己的存放庫，以儲存您小組的共用程式碼和檔、執行版本控制，以及控制有權查看和貢獻程式碼的人員。 

如需有關使用 Git 的詳細資訊，請瀏覽 [GitHub 說明頁面](https://help.github.com/) \(英文\)。 您可以使用 GitHub 做為與小組共同作業的其中一種方式，使用由社區開發的程式碼，並讓程式碼回到該社區。

DSVM 會在命令列上與用戶端工具一起載入，並在 GUI 上用來存取 GitHub 存放庫。 適用于 Git 和 GitHub 的命令列工具稱為 Git Bash。 Visual Studio 安裝在 DSVM 上，且具有 Git 延伸模組。 您可以在 [**開始**] 功能表和桌面上找到這些工具的圖示。

若要從 GitHub 存放庫下載程式碼，您可以使用 ```git clone``` 命令。 例如，若要將 Microsoft 所發佈的資料科學儲存機制下載到目前的目錄中，您可以在 Git Bash 中執行下列命令：

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

您可以在 Visual Studio 中執行相同的複製作業。 下列螢幕擷取畫面顯示如何在 Visual Studio 中存取 Git 和 GitHub 工具：

![顯示 GitHub 連線的 Visual Studio 螢幕擷取畫面](./media/vm-do-ten-things/VSGit.PNG)

您可以從 github.com 上的可用資源，找到有關使用 Git 來處理 GitHub 存放庫的詳細資訊。 [功能提要](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) 是有用的參考資料。

## <a name="access-azure-data-and-analytics-services"></a>存取 Azure 資料和分析服務
### <a name="azure-blob-storage"></a>Azure Blob 儲存體
Azure Blob 儲存體是可靠、經濟實惠的雲端儲存體服務，適用于資料的規模和小型。 本節說明如何將資料移至 Blob 儲存體，並存取儲存在 Azure blob 中的資料。

#### <a name="prerequisites"></a>必要條件

* 從[Azure 入口網站](https://portal.azure.com)建立您的 Azure Blob 儲存體帳戶。

   ![Azure 入口網站中的儲存體帳戶建立程式的螢幕擷取畫面](./media/vm-do-ten-things/create-azure-blob.png)

* 確認已預先安裝命令列 AzCopy 工具： ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` 。 包含 azcopy.exe 的目錄已在您的 PATH 環境變數上，因此您可以在執行此工具時避免輸入完整的命令路徑。 如需 AzCopy 工具的詳細資訊，請參閱[AzCopy 檔](../../storage/common/storage-use-azcopy.md)。
* 啟動 Azure 儲存體總管工具。 您可以從[儲存體總管網頁](https://storageexplorer.com/)下載它。 

   ![Azure 儲存體總管存取儲存體帳戶的螢幕擷取畫面](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>將資料從 VM 移至 Azure blob： AzCopy

若要在本機檔案與 Blob 儲存體之間移動資料，您可以在命令列或 PowerShell 中使用 AzCopy：

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

將**C:\myfolder**取代為您的檔案儲存所在的路徑，並以您的 blob 儲存體帳戶名稱**mystorageaccount** 、使用容器名稱**mycontainer** ，以及使用您的 blob 儲存體存取金鑰來**儲存帳戶金鑰**。 您可以在[Azure 入口網站](https://portal.azure.com)中找到您的儲存體帳號憑證。

在 PowerShell 中或從命令提示字元執行 AzCopy 命令。 以下是 AzCopy 命令的一些使用範例：

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

執行 AzCopy 命令以複製到 Azure blob 之後，您的檔案會顯示在 Azure 儲存體總管中。

![儲存體帳戶的螢幕擷取畫面，顯示已上傳的 CSV 檔案](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>將資料從 VM 移至 Azure blob： Azure 儲存體總管

您也可以使用 Azure 儲存體總管，從 VM 中的本機檔案上傳資料：

* 若要將資料上傳至容器，請選取目標容器，然後選取 [**上傳**] 按鈕。 ![Azure 儲存體總管中 [上傳] 按鈕的螢幕擷取畫面](./media/vm-do-ten-things/storage-accounts.png)
* 選取 **[檔案**] 方塊右邊的省略號（**...**），從檔案系統中選取一或多個要上傳的檔案，然後選取 [**上傳**] 以開始上傳檔案。 ![[上傳檔案] 對話方塊的螢幕擷取畫面](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>從 Azure blob 讀取資料： Machine Learning 讀取器模組

在 Azure Machine Learning Studio 中，您可以使用 [匯入資料] 模組從 blob 讀取資料。

![Machine Learning Studio 中的匯入資料模組的螢幕擷取畫面](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>從 Azure blob 讀取資料： Python ODBC

您可以使用 BlobService 程式庫，直接從 Jupyter 筆記本中的 blob 或在 Python 程式中讀取資料。

首先，匯入必要的套件：

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

然後，插入您的 Blob 儲存體帳號憑證，並從 Blob 讀取資料：

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

資料會讀取為數據框架：

![前 10 個資料列的螢幕擷取畫面](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage 是海量資料分析工作負載的超大規模資料庫存放庫，而且與 Hadoop 分散式檔案系統（HDFS）相容。 它可以與 Hadoop、Spark 及 Azure Data Lake Analytics 搭配運作。 在本節中，您將瞭解如何使用 Azure Data Lake Analytics 將資料移至 Azure Data Lake Storage 並執行分析。

#### <a name="prerequisites"></a>必要條件

* 在[Azure 入口網站](https://portal.azure.com)中建立 Azure Data Lake Analytics 實例。

   ![從 Azure 入口網站建立 Data Lake Analytics 實例的螢幕擷取畫面](./media/vm-do-ten-things/azure-data-lake-create-v3.png)

* [Visual Studio 外掛程式的 Azure Data Lake 和串流分析工具](https://www.microsoft.com/download/details.aspx?id=49504)已安裝在虛擬機器的 Visual Studio Community 版本中。 開始 Visual Studio 並登入您的 Azure 訂用帳戶之後，您應該會在 Visual Studio 的左面板中看到您的 Azure 資料分析帳戶和儲存體。

   ![Visual Studio 中 Data Lake 工具外掛程式的螢幕擷取畫面](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>將資料從 VM 移至 Data Lake： Azure Data Lake Explorer

您可以使用 Azure Data Lake Explorer，將[資料從虛擬機器中的本機檔案上傳至 Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。

您也可以使用[Azure Data Factory](https://azure.microsoft.com/services/data-factory/)來建立資料管線，以讓 Azure Data Lake 的資料移動。 [這篇文章](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)會引導您完成建立資料管線的步驟。

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>將資料從 Azure blob 讀取至 Data Lake： U-SQL

如果您的資料位於 Azure Blob 儲存體中，您可以直接從「U-SQL」查詢中的 Azure blob 讀取資料。 在撰寫您的 U-SQL 查詢之前，請確定您的 Blob 儲存體帳戶已連結至您的 Azure Data Lake 實例。 移至 [Azure 入口網站]，尋找您的 Azure Data Lake Analytics 儀表板，選取 [**新增資料來源**]，選取**Azure 儲存體**的儲存體類型，然後插入您的 Azure 儲存體帳戶名稱和金鑰。 然後您可以參考儲存體帳戶中儲存的資料。

![[新增資料來源] 對話方塊的螢幕擷取畫面](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

在 Visual Studio 中，您可以從 Blob 儲存體讀取資料、運算元據、工程師功能，並將產生的資料傳送至 Azure Data Lake 或 Azure Blob 儲存體。 當您參考 Blob 儲存體中的資料時，請使用**wasb://**。 當您參考 Azure Data Lake 中的資料時，請使用**swbhdfs://**。

您可以在 Visual Studio 中使用下列的 U-SQL 查詢：

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

將查詢提交至伺服器之後，圖表會顯示作業的狀態。

![作業狀態圖表的螢幕擷取畫面](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>查詢資料湖中的資料：U-SQL

在 Azure Data Lake 中內嵌資料集之後，您可以使用 [ [U-SQL 語言](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)] 來查詢和流覽資料。 U-SQL 語言類似于 T-sql，但結合了 c # 的一些功能，讓使用者可以撰寫自訂的模組和使用者定義的函數。 您可以在上一個步驟中使用指令碼。

將查詢提交至伺服器之後，tripdata_summary.CSV 會出現在 Azure Data Lake Explorer 中。 您可以用滑鼠右鍵按一下檔案來預覽資料。

![Data Lake Explorer 中 CSV 檔案的螢幕擷取畫面](./media/vm-do-ten-things/USQL_create_summary.png)

檔案資訊隨即出現：

![檔案摘要資訊的螢幕擷取畫面](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL 資料倉儲和資料庫
Azure SQL 資料倉儲是彈性的資料倉儲即服務，具有企業級 SQL Server 體驗。

您可以遵循[這篇文章](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)中的指示來布建您的 Azure SQL 資料倉儲。 布建您的 SQL 資料倉儲之後，您可以使用[此逐步](../team-data-science-process/sqldw-walkthrough.md)解說，利用 SQL 資料倉儲中的資料來進行資料上傳、探索和模型化。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB 是雲端中的一種 NoSQL 資料庫。 您可以使用它來處理 JSON 之類的檔，並儲存和查詢檔。

使用下列必要步驟來存取 DSVM 中的 Azure Cosmos DB：

1. Azure Cosmos DB Python SDK 已安裝在 DSVM 上。 若要更新它，請 ```pip install pydocumentdb --upgrade``` 從命令提示字元執行。
2. 從[Azure 入口網站](https://portal.azure.com)建立 Azure Cosmos DB 帳戶和資料庫。
3. 從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=53595)下載 Azure Cosmos DB 資料移轉工具，並將其解壓縮至您選擇的目錄。
4. 使用下列命令參數將儲存在[公用 blob](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json)中的 JSON 資料（火山資料）匯入到 Azure Cosmos DB 遷移工具。 （使用 dtui.exe，從您安裝 Azure Cosmos DB 資料移轉工具的目錄）。輸入具有下列參數的來源和目標位置：
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

匯入資料之後，您可以移至 Jupyter 並開啟標題為*documentdbsample 且*的筆記本。 其中包含 Python 程式碼，用來存取 Azure Cosmos DB 並執行一些基本查詢。 若要深入瞭解 Azure Cosmos DB，請造訪服務的[檔頁面](https://docs.microsoft.com/azure/cosmos-db/)。

## <a name="use-power-bi-reports-and-dashboards"></a>使用 Power BI 報表和儀表板 
您可以將上述 Azure Cosmos DB 範例中的火山 JSON 檔案視覺化，Power BI Desktop 以取得資料的視覺化深入解析。 在 [Power BI 文章](../../cosmos-db/powerbi-visualize.md)中可找到詳細的步驟。 高階步驟如下：

1. 開啟 Power BI Desktop 並選取 [取得資料]****。 將 URL 指定為： `https://cahandson.blob.core.windows.net/samples/volcano.json` 。
2. 您應該會看到 JSON 記錄已匯入為清單。 將清單轉換成資料表，讓 Power BI 可以使用它。
4. 選取 [展開] （箭號）圖示來展開資料行。
5. 請注意，位置是 [**記錄**] 欄位。 展開記錄，然後只選取座標。 **座標**是清單資料行。
6. 加入新的資料行，以將清單座標資料行轉換成逗號分隔的**LatLong**資料行。 使用公式來串連座標清單欄位中的兩個元素 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` 。
7. 將 [提高**許可權**] 資料行轉換為 [十進位 **]，然後**選取 [**關閉**並套用] 按鈕。

除了上述步驟以外，您還可以貼上下列程式碼。 它會將 Power BI 中的進階編輯器中使用的步驟編寫為腳本，以查詢語言撰寫資料轉換。

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

您的 Power BI 資料模型中現在會有資料。 您的 Power BI Desktop 實例應該會如下所示：

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

您可以使用資料模型來開始建立報表和視覺效果。 您可以遵循[這篇 Power BI 文章](../../cosmos-db/powerbi-visualize.md#build-the-reports)中的步驟來建立報告。

## <a name="scale-the-dsvm-dynamically"></a>動態調整 DSVM 
您可以相應增加和減少 DSVM，以符合您的專案需求。 如果您在晚上或週末不需要使用 VM，您可以從[Azure 入口網站](https://portal.azure.com)關閉 vm。

> [!NOTE]
> 如果您只針對 VM 上的作業系統使用 [關機] 按鈕，則會產生計算費用。  
> 
> 

您可能需要處理一些大規模的分析，而且需要更多的 CPU、記憶體或磁片容量。 若是如此，您可以根據 CPU 核心、深度學習的 GPU 型實例、記憶體容量和磁片類型（包括固態硬碟機），找到符合您計算和預算需求的 VM 大小選擇。 您可以在[Azure 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)頁面上取得 vm 的完整清單及其每小時計算定價。

同樣地，您的 VM 處理容量需求可能會降低。 （例如：您已將主要工作負載移至 Hadoop 或 Spark 叢集）。然後，您可以從[Azure 入口網站](https://portal.azure.com)相應減少叢集，並移至 VM 實例的設定。 

## <a name="add-more-tools"></a>新增更多工具
預先建立至 DSVM 的工具可解決許多常見的資料分析需求。 這可節省您的時間，因為您不需要逐一安裝和設定您的環境。 它也會為您節省成本，因為您只需支付所使用的資源。

您可以使用本文中所分析的其他 Azure 資料和分析服務，以增強您的分析環境。 在某些情況下，您可能需要額外的工具，包括一些專屬的合作夥伴工具。 您在虛擬機器上擁有完整的系統管理存取權，可安裝您所需的新工具。 您也可以在 Python 和 R 中安裝其他未預先安裝的封裝。 針對 Python，您可以使用 ```conda``` 或 ```pip``` 。 針對 r，您可以 ```install.packages()``` 在 r 主控台中使用，或使用 IDE 並選取 [**套件**] [  >  **安裝套件**]。

## <a name="deep-learning"></a>深入學習

除了以架構為基礎的範例以外，您還可以取得一組已在 DSVM 上驗證的完整逐步解說。 這些逐步解說可協助您快速開始開發各種領域的深度學習應用程式，例如影像和文字/語言理解。   


- [跨不同](https://github.com/ilkarman/DeepLearningFrameworks)的架構執行類神經網路：本逐步解說示範如何將程式碼從一個架構遷移至另一個 framework。 它也會示範如何比較模型和跨架構的執行時間效能。 

- [建置端對端解決方案在影像內偵測產品的操作說明指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：影像偵測是一種可在影像內找出並分類物體的技術。 這項技術可能會在許多真實生活的商業領域中帶來巨大的報酬。 例如，零售商可以利用這項技術來判斷客戶從貨架上挑選什麼產品。 這項資訊進而有助於商店管理產品庫存。 

- [適用于音訊的深度學習](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)：本教學課程示範如何在「市內音效」[資料集](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)上訓練適用于音訊事件偵測的深度學習模型。 它也提供如何使用音訊資料的總覽。

- [文字檔分類](https://github.com/anargyri/lstm_han)：本逐步解說示範如何建立和定型兩個類神經網路架構：階層式注意網路和長短期記憶體（LSTM）網路。 這些類神經網路會使用深入學習的 Keras API 將文字文件分類。 Keras 是三個最受歡迎的深度學習架構的前端： Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="summary"></a>摘要
本文說明您可以在 Microsoft 資料科學虛擬機器上執行的一些事項。 您還可以做更多工作，讓 DSVM 成為有效的分析環境。

