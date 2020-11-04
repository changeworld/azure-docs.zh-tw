---
title: 探索 Windows 上的資料和模型
titleSuffix: Azure Data Science Virtual Machine
description: 在 Windows 資料科學虛擬機器上執行資料探索和模型化工作。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 766da55f2589e2a8eb09e0f1b1c9a0a5027c8c3c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320952"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>使用 Windows 資料科學虛擬機器的資料科學

Windows 資料科學虛擬機器 (DSVM) 是功能強大的資料科學開發環境，可讓您執行資料探索和模型化工作。 此環境已建立並與數個熱門的資料分析工具配套，可讓您輕鬆地開始進行內部部署、雲端或混合式部署的分析。 

DSVM 可與 Azure 服務緊密搭配運作。 它可以讀取和處理已儲存在 Azure Synapse 中的資料，這些資料已儲存在 azure 中， (先前為 SQL DW) 、Azure Data Lake、Azure 儲存體或 Azure Cosmos DB。 它也可以利用其他分析工具，例如 Azure Machine Learning。

在本文中，您將瞭解如何使用 DSVM 來執行資料科學工作，並與其他 Azure 服務互動。 以下是您可以在 DSVM 上做的一些事：

- 使用 Jupyter 筆記本，在瀏覽器中使用 Python 2、Python 3 和 Microsoft R 來試驗您的資料。 (的 Microsoft R 是針對效能設計的 R 企業就緒版。 ) 
- 使用 Microsoft Machine Learning Server 和 Python，探索資料並在 DSVM 本機開發模型。
- 使用 Azure 入口網站或 PowerShell 管理您的 Azure 資源。
- 藉由在 DSVM 上建立 Azure 檔案儲存體共用作為可掛接的磁片磁碟機，來擴充您的儲存空間，並在整個小組之間共用大規模的資料集/程式碼。
- 使用 GitHub 與您的小組共用程式碼。 使用預先安裝的 Git 用戶端存取您的存放庫： Git Bash 和 Git GUI。
- 存取 azure 資料和分析服務，例如 Azure Blob 儲存體、Azure Cosmos DB、Azure Synapse (先前為 SQL DW) 和 Azure SQL Database。
- 使用預先安裝在 DSVM 上的 Power BI Desktop 實例來建立報表和儀表板，並將其部署至雲端。

- 在您的虛擬機器上安裝其他工具。   

> [!NOTE]
> 本文所列的許多資料儲存體和分析服務都適用額外的使用費用。 如需詳細資訊，請參閱 [Azure 定價](https://azure.microsoft.com/pricing/) 頁面。
> 
> 

## <a name="prerequisites"></a>必要條件

* 您需要 Azure 訂用帳戶。 您可以 [註冊免費試用](https://azure.microsoft.com/free/)。
* 在 Azure 入口網站上布建資料科學虛擬機器的指示可用於 [建立虛擬機器](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>使用 Jupyter Notebook
Jupyter Notebook 針對資料探索和模型化提供以瀏覽器為基礎的 IDE。 您可以在 Jupyter 筆記本中使用 Python 2、Python 3 或 R (開放原始碼和 Microsoft R Server) 。

若要啟動 Jupyter Notebook，請選取 [ **開始** ] 功能表或桌面上的 [ **Jupyter Notebook** ] 圖示。 在 DSVM 命令提示字元中，您也可以 ```jupyter notebook``` 從已有筆記本的目錄或您要在其中建立新筆記本的目錄執行命令。  

開始 Jupyter 之後，請流覽至 `/notebooks` 已預先封裝至 DSVM 的範例筆記本目錄。 現在您可以：

* 選取筆記本以查看程式碼。
* 選取 Shift + Enter 來執行每個儲存格。
* 選取 [資料 **格** 執行] 來執行整個筆記本  >  **** 。
* 若要建立新的筆記本，請選取 Jupyter 圖示 (左上角) ，選取右邊的 [ **新增** ] 按鈕，然後選擇 [筆記本語言] (也稱為 [核心) ]。   

> [!NOTE]
> 目前支援 Jupyter 中的 Python 2.7、Python 3.6、R、Julia 和 PySpark 核心。 R 核心支援以開放原始碼 R 和 Microsoft R 進行程式設計。   
> 
> 

當您在筆記本中時，您可以流覽資料、建立模型，並使用您選擇的程式庫來測試模型。

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>使用 Microsoft Machine Learning Server 探索資料及開發模型
您可以使用 R 和 Python 等語言在 DSVM 上進行資料分析。

針對 R，您可以使用可在 [開始] 功能表或桌面上找到的 IDE （例如 RStudio）。 或者，您可以使用 Visual Studio R 工具。 Microsoft 已在開放原始碼 CRAN R 之上提供額外的程式庫，以啟用可調整的分析，並能夠分析大於平行區塊分析中所允許之記憶體大小的資料。 

針對 Python，您可以使用 Visual Studio Community Edition 這類已預先安裝「適用於 Visual Studio 的 Python 工具」(PTVS) 延伸模組的 IDE。 依預設，只會在 PTVS 上設定 Python 3.6 （根 Conda 環境）。 若要啟用 Anaconda Python 2.7，請執行下列步驟：

1. 若要為每個版本建立自訂環境，請前往 [ **工具**  >  **python 工具**  >  **python 環境** ]，然後在 Visual Studio Community 版本中選取 [ **+ 自訂** ]。
1. 提供描述，並將環境首碼路徑設定為 Anaconda Python 2.7 的 **c:\anaconda\envs\python2** 。
1. 選取 [ **自動偵測套用** ]  >  **Apply** 以儲存環境。

如需如何建立 Python 環境的詳細資訊，請參閱 [PTVS 檔](/visualstudio/python/) 。

現在您已設定建立新的 Python 專案。 移至 **File**  >  [ **新增**  >  **專案**  >  **python** ]，然後選取您要建立的 python 應用程式類型。 您可以用滑鼠右鍵按一下 [ **python 環境** ]，然後選取 [ **新增/移除 python 環境** ]，將目前專案的 python 環境設定為所需的版本 (python 2.7 或 3.6) 。 您可以在 [產品檔](/visualstudio/python/)中找到有關使用 PTVS 的詳細資訊。



## <a name="manage-azure-resources"></a>管理 Azure 資源
DSVM 不只是讓您在虛擬機器上的本機建立分析解決方案。 它也可讓您存取 Azure 雲端平臺上的服務。 Azure 提供數個計算、儲存體、資料分析，以及您可以從 DSVM 管理和存取的其他服務。

若要管理您的 Azure 訂用帳戶和雲端資源，您有兩個選項：
+ 使用您的瀏覽器並移至 [Azure 入口網站](https://portal.azure.com)。

+ 使用 PowerShell 腳本。 從桌面上的快捷方式或從 [ **開始** ] 功能表執行 Azure PowerShell。 如需完整的詳細資料，請參閱 [Microsoft Azure PowerShell 檔](../../azure-resource-manager/management/manage-resources-powershell.md) 。 

## <a name="extend-storage-by-using-shared-file-systems"></a>使用共用檔案系統來擴充儲存體
資料科學家可以在小組內共用大型資料集、程式碼或其他資源。 DSVM 大約有 45 GB 的可用空間。 若要擴充您的儲存體，您可以使用 Azure 檔案儲存體，並將其裝載在一或多個 DSVM 實例上，或透過 REST API 來存取。 您也可以使用 [Azure 入口網站](../../virtual-machines/windows/attach-managed-disk-portal.md) ，或使用 [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) 來新增額外的專用資料磁片。 

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

現在您已建立 Azure 檔案儲存體共用，您可以將它裝載在 Azure 中的任何虛擬機器。 建議您將 VM 放在與儲存體帳戶相同的 Azure 資料中心，以避免延遲和資料傳輸費用。 以下是在 DSVM 上掛接磁片磁碟機的 Azure PowerShell 命令：

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

您現在可以將此磁碟機當作 VM 上的任何一般磁碟機存取。

## <a name="share-code-in-github"></a>在 GitHub 中共用程式碼
GitHub 是一個程式碼存放庫，您可以使用開發人員群體所共用的技術來尋找各種工具的程式碼範例和來源。 它會使用 Git 技術來追蹤和儲存各版本的程式碼檔案。 GitHub 也是一個平臺，您可以在其中建立自己的存放庫來儲存小組的共用程式碼和檔、執行版本控制，以及控制誰有權查看和參與程式碼。 

如需有關使用 Git 的詳細資訊，請瀏覽 [GitHub 說明頁面](https://help.github.com/) \(英文\)。 您可以使用 GitHub 作為與小組共同作業的其中一種方式，使用由社區開發的程式碼，並將程式碼貢獻回給該社區。

DSVM 會在命令列上和在 GUI 上載入用戶端工具，以存取 GitHub 存放庫。 適用于 Git 和 GitHub 的命令列工具稱為 Git Bash。 Visual Studio 安裝在 DSVM 上，且具有 Git 擴充功能。 您可以在 [ **開始** ] 功能表和桌面上找到這些工具的圖示。

若要從 GitHub 存放庫下載程式碼，您可以使用 ```git clone``` 命令。 例如，若要將 Microsoft 所發佈的資料科學存放庫下載到目前的目錄中，您可以在 Git Bash 中執行下列命令：

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

您可以在 Visual Studio 中執行相同的複製作業。 下列螢幕擷取畫面顯示如何存取 Visual Studio 中的 Git 和 GitHub 工具：

![顯示 GitHub 連線的 Visual Studio 螢幕擷取畫面](./media/vm-do-ten-things/VSGit.png)

您可以從 github.com 上提供的資源，找到有關使用 Git 來與您的 GitHub 存放庫搭配運作的詳細資訊。 [功能提要](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) 是有用的參考資料。

## <a name="access-azure-data-and-analytics-services"></a>存取 Azure 資料和分析服務
### <a name="azure-blob-storage"></a>Azure Blob 儲存體
Azure Blob 儲存體是可靠、經濟實惠的雲端儲存體服務，適用于資料的規模和小型儲存體服務。 本節說明如何將資料移至 Blob 儲存體，以及存取儲存在 Azure blob 中的資料。

#### <a name="prerequisites"></a>必要條件

* 從 [Azure 入口網站](https://portal.azure.com)建立您的 Azure Blob 儲存體帳戶。

   ![Azure 入口網站中儲存體帳戶建立程式的螢幕擷取畫面](./media/vm-do-ten-things/create-azure-blob.png)

* 確認已預先安裝命令列 AzCopy 工具： ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` 。 包含 azcopy.exe 的目錄已經在您的 PATH 環境變數中，因此您可以避免在執行此工具時輸入完整的命令路徑。 如需 AzCopy 工具的詳細資訊，請參閱 [AzCopy 檔](../../storage/common/storage-use-azcopy-v10.md)。
* 啟動 Azure 儲存體總管工具。 您可以從  [儲存體總管網頁](https://storageexplorer.com/)下載它。 

   ![存取儲存體帳戶 Azure 儲存體總管的螢幕擷取畫面](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>將資料從 VM 移至 Azure blob： AzCopy

若要在本機檔案與 Blob 儲存體之間移動資料，您可以在命令列或 PowerShell 中使用 AzCopy：

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

將 **C:\myfolder** 取代為您的檔案儲存路徑、以 blob 儲存體帳戶名稱 **mystorageaccount** 、以容器名稱 **mycontainer** ，並以 blob 儲存體存取金鑰取代 **儲存體帳戶金鑰** 。 您可以在 [Azure 入口網站](https://portal.azure.com)中找到您的儲存體帳號憑證。

在 PowerShell 或命令提示字元中執行 AzCopy 命令。 以下是 AzCopy 命令的一些使用範例：

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

當您執行 AzCopy 命令來複製到 Azure blob 之後，您的檔案就會出現在 Azure 儲存體總管中。

![儲存體帳戶的螢幕擷取畫面，其中顯示已上傳的 CSV 檔案](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>將資料從 VM 移至 Azure blob： Azure 儲存體總管

您也可以使用 Azure 儲存體總管，從 VM 中的本機檔案上傳資料：

* 若要將資料上傳至容器，請選取目標容器，然後選取 [ **上傳** ] 按鈕。 ![Azure 儲存體總管中 [上傳] 按鈕的螢幕擷取畫面](./media/vm-do-ten-things/storage-accounts.png)
* 選取 **[檔案** ] 方塊右邊的省略號 ( **...** ) ，從檔案系統中選取一或多個要上傳的檔案，然後選取 **[上傳** ] 以開始上傳檔案。 ![[上傳檔案] 對話方塊的螢幕擷取畫面](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>從 Azure blob 讀取資料： Python ODBC

您可以使用 BlobService 程式庫，直接從 Jupyter 筆記本中的 blob 或 Python 程式讀取資料。

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

然後，插入您的 Blob 儲存體帳號憑證，並從 blob 讀取資料：

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

![前 10 個資料列的螢幕擷取畫面](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-formerly-sql-dw-and-databases"></a>Azure Synapse Analytics (先前的 SQL DW) 和資料庫
Azure Synapse Analytics (先前的 SQL DW) 是彈性的資料倉儲即服務，具備企業級的 SQL Server 體驗。

您可以 [遵循本文中](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)的指示來布建 Azure Synapse Analytics。 布建 Azure Synapse Analytics 之後，您可以使用 [此逐步](../team-data-science-process/sqldw-walkthrough.md) 解說，利用 Azure Synapse Analytics 內的資料來進行資料上傳、探索及模型化。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB 是雲端中的一種 NoSQL 資料庫。 您可以使用它來處理 JSON 等檔，以及儲存和查詢檔。

使用下列必要條件步驟從 DSVM 存取 Azure Cosmos DB：

1. DSVM 上已安裝 Azure Cosmos DB Python SDK。 若要更新，請 ```pip install pydocumentdb --upgrade``` 從命令提示字元執行。
2. 從 [Azure 入口網站](https://portal.azure.com)建立 Azure Cosmos DB 帳戶和資料庫。
3. 請從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=53595) 下載 Azure Cosmos DB 資料移轉工具，並將其解壓縮至您選擇的目錄。
4. 使用遷移工具的下列命令參數，將 JSON 資料 (火山儲存在 [公用 blob](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) 中的資料) 匯入 Azure Cosmos DB。  (從安裝 Azure Cosmos DB 資料移轉工具的目錄中使用 dtui.exe。 ) 以這些參數輸入來源和目標位置：
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

匯入資料之後，您可以移至 Jupyter，並開啟標題為 *>documentdbsample* 的筆記本。 它包含 Python 程式碼，可存取 Azure Cosmos DB 並進行一些基本查詢。 您可以造訪服務的 [檔頁面](../../cosmos-db/index.yml)，以深入瞭解 Azure Cosmos DB。

## <a name="use-power-bi-reports-and-dashboards"></a>使用 Power BI 報表和儀表板 
您可以從 Power BI Desktop 中先前的 Azure Cosmos DB 範例將火山 JSON 檔案視覺化，以深入瞭解資料。 在 [Power BI 文章](../../cosmos-db/powerbi-visualize.md)中可找到詳細的步驟。 高階步驟如下：

1. 開啟 Power BI Desktop 並選取 [取得資料]。 將 URL 指定為： `https://cahandson.blob.core.windows.net/samples/volcano.json` 。
2. 您應該會看到匯入為清單的 JSON 記錄。 將清單轉換成資料表，讓 Power BI 可以使用它。
4. 選取展開 (箭號) 圖示來展開資料行。
5. 請注意，此位置是 **記錄** 欄位。 展開記錄，然後只選取座標。 **座標** 是清單資料行。
6. 加入新的資料行，將清單座標資料行轉換成以逗號分隔的 **LatLong** 資料行。 使用公式來串連座標清單欄位中的兩個元素 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` 。
7. 將 [提高 **許可權** ] 資料行轉換成小數， **然後選取** [ **關閉** ] 和 [套用] 按鈕。

您可以貼上下列程式碼，而不是先前的步驟。 它會編寫進階編輯器中所使用的步驟腳本，Power BI 以查詢語言撰寫資料轉換。

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

您的 Power BI 資料模型中現在會有資料。 您的 Power BI Desktop 實例應該會顯示如下：

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

您可以使用資料模型來開始建立報表和視覺效果。 您可以遵循 [Power BI 本文](../../cosmos-db/powerbi-visualize.md#build-the-reports) 中的步驟來建立報表。

## <a name="scale-the-dsvm-dynamically"></a>以動態方式調整 DSVM 
您可以擴大和縮小 DSVM，以符合您的專案需求。 如果您不需要在夜間或週末使用 VM，您可以從 [Azure 入口網站](https://portal.azure.com)關閉 vm。

> [!NOTE]
> 如果您只使用 VM 上的作業系統的 [關閉] 按鈕，則會產生計算費用。 相反地，您應該使用 Azure 入口網站或 Cloud Shell 來解除配置您的 DSVM。
> 
> 

您可能需要處理一些大規模分析，而且需要更多的 CPU、記憶體或磁片容量。 若是如此，您可以在 CPU 核心、GPU 型實例的深度學習、記憶體容量和磁片類型中找到選擇的 VM 大小 (包括符合您計算和預算需求的固態硬碟) 。 [Azure 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)頁面上有提供 vm 的完整清單，以及其每小時計算定價。


## <a name="add-more-tools"></a>新增其他工具
預建在 DSVM 中的工具可解決許多常見的資料分析需求。 這可節省您的時間，因為您不需要逐一安裝和設定您的環境。 因為您只需支付所使用的資源，所以也能為您節省成本。

您可以使用本文中所述的其他 Azure 資料和分析服務來增強您的分析環境。 在某些情況下，您可能需要額外的工具，包括一些專屬的合作夥伴工具。 您可以在虛擬機器上擁有完整的系統管理存取權，以安裝所需的新工具。 您也可以在 Python 和 R 中安裝其他未預先安裝的封裝。 針對 Python，您可以使用 ```conda``` 或 ```pip``` 。 針對 r，您可以 ```install.packages()``` 在 r 主控台中使用，或使用 IDE 並選取 **封裝**  >  **安裝套件** 。

## <a name="deep-learning"></a>深入學習

除了以架構為基礎的範例之外，您還可以取得一組已在 DSVM 上驗證的完整逐步解說。 這些逐步解說可協助您在影像和文字/語言理解之類的領域中，快速開始開發深度學習應用程式。   


- [跨不同](https://github.com/ilkarman/DeepLearningFrameworks)的架構執行類神經網路：本逐步解說會示範如何將程式碼從一個架構遷移到另一個架構。 它也會示範如何跨架構來比較模型和執行時間效能。 

- [建置端對端解決方案在影像內偵測產品的操作說明指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：影像偵測是一種可在影像內找出並分類物體的技術。 這項技術可能會在許多實際的商務領域中帶來巨大的獎勵。 例如，零售商可以利用這項技術來判斷客戶從貨架上挑選什麼產品。 這項資訊進而有助於商店管理產品庫存。 

- [音訊的深度學習](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure)：本教學課程示範如何在「城市音效」 [資料集](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)上定型深度學習模型以進行音訊事件偵測。 也提供如何使用音訊資料的總覽。

- [文字檔的分類](https://github.com/anargyri/lstm_han)：本逐步解說示範如何建立和定型兩個類神經網路架構：階層式網路和長期記憶體 (LSTM) 網路。 這些類神經網路會使用深入學習的 Keras API 將文字文件分類。 

## <a name="summary"></a>總結
本文說明一些您可以在 Microsoft 資料科學虛擬機器上做的事情。 您還可以做更多工作，讓 DSVM 成為有效的分析環境。