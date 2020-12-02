---
title: 教學課程 - 透過 Data Factory 執行 Python 指令碼
description: 了解如何使用 Azure Batch 透過 Azure Data Factory，將 Python 指令碼當作管線的一部分執行。
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7752bc3f768aec7a3e98fb1813c4194f81fb9dfb
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917625"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>教學課程：使用 Azure Batch 透過 Azure Data Factory 執行 Python 指令碼

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 驗證 Batch 和儲存體帳戶
> * 在 Python 中開發和執行指令碼
> * 建立計算節點的集區來執行應用程式
> * 排程您的 Python 工作負載
> * 監視您的分析管線
> * 存取您的記錄檔

以下範例會執行 Python 指令碼，以從 Blob 儲存體容器接收 CSV 輸入、執行資料操作程序，並將輸出寫入至個別的 Blob 儲存體容器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>必要條件

* 已安裝的 [Python](https://www.python.org/downloads/) 散發套件 (用於本機測試)。
* [azure-storage-blob](https://pypi.org/project/azure-storage-blob/) `pip` 套件。
* [iris.csv 資料集](https://www.kaggle.com/uciml/iris/version/2#Iris.csv)
* Azure Batch 帳戶和連結的 Azure 儲存體帳戶。 請參閱[建立 Batch 帳戶](quick-create-portal.md#create-a-batch-account)，深入了解如何建立 Batch 帳戶並將其連結至儲存體帳戶。
* Azure Data Factory 帳戶。 請參閱[建立資料處理站](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)，深入了解如何透過 Azure 入口網站建立資料處理站。
* [Batch Explorer](https://azure.github.io/BatchExplorer/)。
* [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>使用 Batch Explorer 建立 Batch 集區

在本節中，您將使用 Batch Explorer 來建立 Azure Data factory 管線將會使用的 Batch 集區。 

1. 使用您的 Azure 認證登入 Batch Explorer。
1. 選取您的 Batch 帳戶
1. 選取左側邊列上的 [集區]，然後選取搜尋表單上方的 [新增] 按鈕，以建立集區。 
    1. 選擇識別碼和顯示名稱。 此範例中，我們將使用 `custom-activity-pool`。
    1. 將縮放類型設定為 [固定大小]，並將專用節點計數設定為 2。
    1. 在 [資料科學] 之下，選取 [Dsvm Windows] 作為作業系統。
    1. 選擇 `Standard_f2s_v2` 為虛擬機器大小。
    1. 啟用啟動工作，並新增命令 `cmd /c "pip install azure-storage-blob pandas"`。 使用者身分識別可保留為預設 [集區使用者]。
    1. 選取 [確定]。

## <a name="create-blob-containers"></a>建立 Blob 容器

這裡您將建立 Blob 容器，可儲存 OCR Batch 作業的輸入和輸出檔案。

1. 使用您的 Azure 認證登入儲存體總管。
1. 遵循[建立 Blob 容器](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)中的步驟，使用連結至 Batch 帳戶的儲存體帳戶，建立兩個 Blob 容器 (一個用於輸入檔，一個用於輸出檔案)。
    * 在此範例中，我們將輸入容器稱為 `input`，輸出容器稱為 `output`。
1. 遵循[管理 Blob 容器中的 Blob](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) 中的步驟，使用儲存體總管將 [`iris.csv`](https://www.kaggle.com/uciml/iris/version/2#Iris.csv) 上傳至輸入容器 `input`

## <a name="develop-a-script-in-python"></a>在 Python 中開發指令碼

下列 Python 指令碼會從您的 `input` 容器載入 `iris.csv` 資料集、執行資料操作程序，然後將結果存回 `output` 容器。

``` python
# Load libraries
from azure.storage.blob import BlobServiceClient
import pandas as pd

# Define parameters
storageAccountURL = "<storage-account-url>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blob_service_client = BlockBlobService(account_url=storageAccountURL,
                               credential=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
container_client = blob_service_client.get_container_client(containerName)
with open("iris_setosa.csv", "rb") as data:
    blob_client = container_client.upload_blob(name="iris_setosa.csv", data=data)
```

將指令碼儲存為 `main.py`，並將其上傳至 **Azure 儲存體** `input` 容器。 請務必先在本機測試並驗證其功能，再將其上傳至您的 Blob 容器：

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>設定 Azure Data Factory 管線

在本節中，您將使用 Python 指令碼來建立及驗證管線。

1. 依照[本文](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)的「建立資料處理站」一節中的步驟來建立資料處理站。
1. 在 [處理站資源] 方塊中，選取 + (加號) 按鈕，然後選取 [管線]。
1. 在 [一般] 索引標籤中，將管線的名稱設定為 [執行 Python]。

    ![在 [一般] 索引標籤中，將管線的名稱設定為「執行 Python」](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. 在 [活動] 方塊中，展開 [Batch 服務]。 將 [活動] 工具箱中的自訂活動拖到管線設計工具介面。
1. 在 [一般] 索引標籤中，指定 **testPipeline** 作為 [名稱]。

    ![在 [一般] 索引標籤中，指定 testPipeline 作為 [名稱]](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. 在 [Azure Batch] 索引標籤中，新增在先前步驟中建立的 [Batch 帳戶]，然後 **測試連線** 以確保連線成功。

    ![在 [Azure Batch] 索引標籤中，新增在先前步驟中建立的 [Batch 帳戶]，然後測試連線](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. 在 [設定] 索引標籤中，輸入 `python main.py` 命令。
1. 針對 [資源連結服務]，新增在先前步驟中建立的儲存體帳戶。 測試連線以確保連線成功。
1. 在 [資料夾路徑] 中，選取 [Azure Blob 儲存體] 容器的名稱，其中包含 Python 指令碼及相關聯的輸入。 在 Python 腳本執行之前，這會將選取的檔案從容器下載至集區節點執行個體。

    ![在 [資料夾路徑] 中，選取 Azure Blob 儲存體容器的名稱](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. 在畫布上方的管線設定上按一下 [驗證]，以驗證管線設定。 確認管線已驗證成功。 若要關閉驗證輸出，請選取 &gt;&gt; (向右箭號) 按鈕。
1. 按一下 [偵錯] 以測試管線，並確保其運作正常。
1. 按一下 [發佈] 以發佈管線。
1. 按一下 [觸發]，以在批次處理過程中執行 Python 指令碼。

    ![按一下 [觸發]，以在批次處理過程中執行 Python 指令碼](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>監視記錄檔

如果指令碼的執行產生警告或錯誤，您可以查看 `stdout.txt` 或 `stderr.txt`，以取得所記錄輸出的詳細資訊。

1. 從 Batch Explorer 的左側選取 [作業]。
1. 選擇您的資料處理站所建立的作業。 假設您將集區命名為 `custom-activity-pool`，請選取 `adfv2-custom-activity-pool`。
1. 按一下有失敗結束代碼的工作。
1. 檢視 `stdout.txt` 和 `stderr.txt` 來調查及診斷您的問題。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已探索一個範例並了解如何使用 Azure Batch 透過 Azure Data Factory，將 Python 指令碼當作管線的一部分執行。

若要深入了解 Azure Data Factory，請參閱：

> [!div class="nextstepaction"]
> [Azure Data Factory 概觀](../data-factory/introduction.md)

