---
title: 使用 Azure Functions 觸發 Batch 作業
description: 教學課程 - 在文件新增至儲存體 Blob 時，將 OCR 套用至已掃描的文件
author: LauraBrenner
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: a967fdc14b85f294ee11cbcc57a8d2280dba38e8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017185"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>教學課程：使用 Azure Functions 觸發 Batch 作業

在本教學課程中，您將了解如何使用 Azure Functions 觸發 Batch 作業。 我們將逐步解說範例，在範例中，每個新增至 Azure 儲存體 Blob 容器的文件都會透過 Azure Batch 套用光學字元辨識 (OCR)。 為了簡化 OCR 處理流程，我們將設定 Azure 函式，每當檔案新增至 Blob 容器時，函式便會執行 Batch OCR 作業。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* Azure Batch 帳戶和連結的 Azure 儲存體帳戶。 請參閱[建立 Batch 帳戶](quick-create-portal.md#create-a-batch-account)以瞭解有關如何建立及連結帳戶的詳細資訊。
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>使用 Batch Explorer 建立 Batch 集區和 Batch 作業

在本節中，您將使用 Batch Explorer 來建立 Batch 集區和將執行 OCR 工作的 Batch 作業。 

### <a name="create-a-pool"></a>建立集區

1. 使用您的 Azure 認證登入 Batch Explorer。
1. 選取左側邊列上的 [集區]  ，然後選取搜尋表單上方的 [新增]  按鈕，以建立集區。 
    1. 選擇識別碼和顯示名稱。 此範例中，我們將使用 `ocr-pool`。
    1. 將縮放類型設定為 [固定大小]  ，並將專用節點計數設定為 3。
    1. 選取 [Ubuntu 18.04-LTS]  作為作業系統。
    1. 選擇 `Standard_f2s_v2` 為虛擬機器大小。
    1. 啟用啟動工作，並新增命令 `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`。 請務必將使用者身分識別設定為**工作預設使用者 (管理員)** ，這可讓啟動工作包含具 `sudo` 的命令。
    1. 選取 [確定]  。
### <a name="create-a-job"></a>建立作業

1. 選取左側邊列上的 [作業]  ，然後選取搜尋表單上方的 [新增]  按鈕，以在集區上建立作業。 
    1. 選擇識別碼和顯示名稱。 此範例中，我們將使用 `ocr-job`。
    1. 將集區設為 `ocr-pool`，或您為集區選擇的任何名稱。
    1. 選取 [確定]  。


## <a name="create-blob-containers"></a>建立 Blob 容器

這裡您將建立 Blob 容器，可儲存 OCR Batch 作業的輸入和輸出檔案。

1. 使用您的 Azure 認證登入儲存體總管。
1. 遵循[建立 Blob 容器](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container)中的步驟，使用連結至 Batch 帳戶的儲存體帳戶，建立兩個 Blob 容器 (一個用於輸入檔，一個用於輸出檔案)。

在此範例中，輸入容器的名稱為 `input`，所有不具 OCR 的文件，一開始都會先上傳到此處等待處理。 輸出容器的名稱為 `output`，經處理且已具有 OCR 的文件，都會在此處由 Batch 作業進行寫入。  
    * 在此範例中，我們將輸入容器稱為 `input`，輸出容器稱為 `output`。  
    * 輸入容器是所有不具 OCR 的文件最初上傳的位置。  
    * 輸出容器是 Batch 作業為已具有 OCR 的文件進行寫入的位置。  

在儲存體總管中建立輸出容器的共用存取簽章。 要執行這項操作請在輸出容器上按一下滑鼠右鍵，然後選取 [取得共用存取簽章...]  。在 [權限]  下方，勾選 [寫入]  。 不需任何其他權限。  

## <a name="create-an-azure-function"></a>建立 Azure 函式

在本節中，您將建立每當檔案上傳至輸入容器時，就會觸發 OCR Batch 作業的 Azure 函式。

1. 請依照[建立由 Azure Blob 儲存體所觸發的函式](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function)中的步驟建立函式。
    1. 當系統提示您使用儲存體帳戶，請使用您連結至 Batch 帳戶的同一儲存體帳戶。
    1. 針對**執行階段堆疊**，選擇 .NET。 我們將以 C# 撰寫函式以便運用 Batch .NET SDK。
1. 由 Blob 觸發的函式建立之後，使用 [函式] 中 GitHub 的 [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) 和 [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj)。
    * 當新的 Blob 新增至輸入 Blob 容器時，就會執行 `run.csx`。
    * `function.proj` 會列出您函式程式碼中的外部程式庫，例如 Batch .NET SDK。
1. 變更 `run.csx` 檔案中 `Run()` 函式的變數預留位置值，以反映您的 Batch 和儲存體認證。 您可以在 Azure 入口網站中 Batch 帳戶的 [金鑰]  區段中尋找您的 Batch 和儲存體帳戶認證。
    * 在 Azure 入口網站中 Batch 帳戶的 [金鑰]  區段中取出您的 Batch 和儲存體帳戶認證。 

## <a name="trigger-the-function-and-retrieve-results"></a>觸發函式並取出結果

從 GitHub 上的 [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) 目錄將任何或所有已掃描的檔案上傳至輸入容器。 監視 Batch Explorer 以確認工作已為每個檔案新增至 `ocr-pool`。 幾秒之後，套用 OCR 的檔案會新增至輸出容器。 檔案隨即就會顯示在儲存體總管中並可加以取出。

此外，您可以在 Azure Functions Web 編輯器視窗底部觀看記錄檔，您會看到上傳至輸入容器的每個檔案都有如下訊息：

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

若要從儲存體總管將輸出檔案下載到本機電腦中，請先選取您要的檔案，然後再選取頂端功能區的 [下載]  。 

> [!TIP]
> 如果以 PDF 閱讀程式開啟，則下載的檔案為可搜尋。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何： 

> [!div class="checklist"]
> * 使用 Batch Explorer 來建立集區和作業
> * 使用儲存體總管來建立 Blob 容器和共用存取簽章 (SAS)
> * 建立由 Blob 觸發的 Azure 函式
> * 將輸入檔案上傳至儲存體
> * 監視工作執行
> * 擷取輸出檔案

* 如需更多使用 .NET API 來排程和處理 Batch 工作負載的範例，請參閱 [GitHub 上的範例](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)。 

* 若要查看更多您可以用來執行 Batch 工作負載的 Azure Functions 觸發程序，請參閱 [Azure Functions 文件](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)。
