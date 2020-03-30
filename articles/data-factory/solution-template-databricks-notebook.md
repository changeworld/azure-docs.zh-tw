---
title: 使用 Azure 資料塊進行轉換
description: 了解如何使用 Azure Data Factory 中的 Databricks Notebook 以解決方案範本轉換資料。
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 9a05b09f958d741fa56c586fbc7f5c5908dbbce6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384376"
---
# <a name="transformation-with-azure-databricks"></a>使用 Azure 資料塊進行轉換

在本教程中，您將創建一個端到端管道，其中包含 Azure 資料工廠中的**驗證**、**複製資料和****筆記本**活動。

- **在**觸發複製和分析作業之前，驗證可確保源資料集已準備好用於下游使用。

- **複製資料**將源資料集複製到接收器存儲，該存儲作為 DBFS 裝載在 Azure 資料塊筆記本中。 這樣，Spark 可以直接使用資料集。

- **筆記本**將觸發轉換資料集的資料磚筆記本。 它還將資料集添加到已處理的資料夾或 Azure SQL 資料倉儲。

為簡單起見，本教程中的範本不會創建計畫觸發器。 如有必要，可以添加一個。

![管道圖](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Prerequisites

- 具有用作`sinkdata`接收器的容器的 Azure Blob 存儲帳戶。

  記下存儲帳戶名稱、容器名稱和訪問金鑰。 稍後將需要在範本中使用這些值。

- Azure 資料塊工作區。

## <a name="import-a-notebook-for-transformation"></a>導入用於轉換的筆記本

要將**轉換**筆記本導入資料磚塊工作區，請：

1. 登錄到 Azure 資料塊工作區，然後選擇 **"導入**"。
       ![用於導入工作區](media/solution-template-Databricks-notebook/import-notebook.png)的功能表命令您的工作區路徑可能不同于顯示的路徑，但請稍後記住它。
1. 選擇**從：URL**導入。 在文字方塊中，輸入`https://adflabstaging1.blob.core.windows.net/share/Transformations.html`。

   ![導入筆記本的選擇](media/solution-template-Databricks-notebook/import-from-url.png)

1. 現在，讓我們使用存儲連接資訊更新**轉換**筆記本。

   在導入的筆記本中，轉到**命令 5，** 如以下程式碼片段所示。

   - 替換`<storage name>`並`<access key>`使用您自己的存儲連接資訊。
   - 將存儲帳戶與容器一`sinkdata`起使用。

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. 產生供 Data Factory 用來存取 Databricks 的 **Databricks 存取權杖**。
   1. 在 DataBRICKS 工作區中，選擇右上角的使用者設定檔圖示。
   1. 選擇**使用者設置**。
    ![使用者設置的功能表命令](media/solution-template-Databricks-notebook/user-setting.png)
   1. 在 **"訪問權杖"** 選項卡下選擇 **"生成新權杖**"。
   1. 選取 [產生]****。

    !["生成"按鈕](media/solution-template-Databricks-notebook/generate-new-token.png)

   *保存訪問權杖*以供以後用於創建資料磚塊連結服務。 訪問權杖類似于`dapi32db32cbb4w6eee18b7d87e45exxxxxx`。

## <a name="how-to-use-this-template"></a>如何使用此範本

1. 轉到使用**Azure 資料磚塊範本的轉換**，並創建用於以下連接的新連結服務。

   ![連接設置](media/solution-template-Databricks-notebook/connections-preview.png)

    - **源 Blob 連接**- 訪問來源資料。

       在本練習中，可以使用包含原始檔案的公共 Blob 存儲。 參考以下配置螢幕截圖。 使用以下**SAS URL**連接到源存儲（唯讀訪問）：

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![身份驗證方法和 SAS URL 的選擇](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **目標 Blob 連接**- 存儲複製的資料。

       在 **"新建連結服務"** 視窗中，選擇接收器存儲 Blob。

       ![將存儲 Blob 作為新的連結服務](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure 資料塊**- 連接到資料磚群集。

        使用以前生成的訪問金鑰創建與 Databricks 連結的服務。 如果您有*互動式群集*，可以選擇選擇互動式群集。 此示例使用 **"新建作業群集"** 選項。

        ![連接到群集的選擇](media/solution-template-Databricks-notebook/databricks-connection.png)

1. 選擇**使用此範本**。 您將看到已創建的管道。

    ![建立管線](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>管道介紹和配置

在新管道中，大多數設置都自動設定預設值。 查看管道的配置並進行任何必要的更改。

1. 在**驗證**活動**可用性標誌**中，驗證源**資料集**值是否設置為`SourceAvailabilityDataset`您之前創建的。

   ![源資料集值](media/solution-template-Databricks-notebook/validation-settings.png)

1. 在 **"將資料**活動**檔案複製到 blob"** 中，檢查 **"源**"和"**接收器"** 選項卡。 如有必要，更改設置。

   - **源**選項卡!["源"選項卡](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **接收器**選項卡!["接收器"選項卡](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. 在**筆記本**活動**轉換中**，根據需要查看和更新路徑和設置。

   **資料磚連結服務**應預填充上一步的值，如圖所示：![資料磚塊連結服務的填充值](media/solution-template-Databricks-notebook/notebook-activity.png)

   要檢查**筆記本**設置：
  
    1. 選擇 **"設置"** 選項卡。對於**筆記本路徑**，驗證預設路徑是否正確。 您可能需要流覽並選擇正確的筆記本路徑。

       ![筆記本路徑](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. 展開**基本參數**選擇器並驗證參數是否與以下螢幕截圖中顯示的參數匹配。 這些參數從資料工廠傳遞到資料磚塊筆記本。

       ![基本參數](media/solution-template-Databricks-notebook/base-parameters.png)

1. 驗證**管道參數**與以下螢幕截圖中顯示的內容匹配：![管道參數](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 連接到資料集。

   - **源可用性資料集**- 檢查來源資料是否可用。

     ![源可用性資料集的連結服務和檔路徑的選擇](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **原始檔案資料集**- 訪問來源資料。

       ![原始檔案資料集的連結服務和檔路徑的選擇](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **目的檔案資料集**- 將資料複製到接收器目標位置。 輸入下列值：

     - **連結服務** - `sinkBlob_LS`，在上一步中創建。

     - **檔路徑** - `sinkdata/staged_sink`。

       ![目的檔案資料集的連結服務和檔路徑的選擇](media/solution-template-Databricks-notebook/destination-dataset.png)

1. 選擇**調試**以運行管道。 您可以找到指向 Databricks 日誌的連結，以獲取更詳細的 Spark 日誌。

    ![從輸出連結到資料磚塊日誌](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    您還可以使用 Azure 存儲資源管理器驗證資料檔案。

    > [!NOTE]
    > 對於與資料工廠管道運行相關的操作，此示例將管道運行 ID 從資料工廠追加到輸出檔案夾。 這有助於跟蹤每次運行生成的檔。
    > ![附加管道運行 ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
