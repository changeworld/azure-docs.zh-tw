---
title: 使用 Azure Databricks 進行轉換
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
ms.openlocfilehash: 65b89a13637f5a4e1712995a6ac58d88b4421806
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414856"
---
# <a name="transformation-with-azure-databricks"></a>使用 Azure Databricks 進行轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在本教學中,您將創建一個端到端管道,其中包含 Azure 資料工廠中的**驗證**、**複製數據和****筆記**型活動。

- **在**觸發複製和分析作業之前,驗證可確保源數據集已準備好用於下游使用。

- **複製資料**將源資料集複製到接收體記憶體儲存,該儲存作為 DBFS 載入在 Azure 資料塊筆記本中。 這樣,Spark可以直接使用數據集。

- **筆記本**將觸發轉換數據集的數據磚筆記本。 它還將數據集添加到已處理的資料夾或 Azure SQL 資料倉庫。

為簡單起見,本教程中的範本不會創建計劃觸發器。 如有必要,可以添加一個。

![管線圖](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Prerequisites

- 具有用作`sinkdata`接收器的容器的 Azure Blob 儲存帳戶。

  儲存儲存帳戶名稱、容器名稱和存取金鑰。 稍後將需要在範本中使用這些值。

- Azure 資料塊工作區。

## <a name="import-a-notebook-for-transformation"></a>匯入用於轉換的筆記本

要將**轉換**筆記本匯入資料磚塊工作區,請:

1. 登錄到 Azure 資料塊工作區,然後選擇 **「導入**」。
       ![用於導入工作區](media/solution-template-Databricks-notebook/import-notebook.png)的功能表命令您的工作區路徑可能不同於顯示的路徑,但請稍後記住它。
1. 選擇**從:URL**匯入。 在文字框中,`https://adflabstaging1.blob.core.windows.net/share/Transformations.html`輸入 。

   ![匯入筆記本的選擇](media/solution-template-Databricks-notebook/import-from-url.png)

1. 現在,讓我們使用存儲連接資訊更新**轉換**筆記本。

   在導入的筆記本中,轉到**命令5,** 如以下代碼段所示。

   - 替換`<storage name>`並`<access key>`使用您自己的儲存連接資訊。
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
   1. 在 DataBRICKS 工作區中,選擇右上角的使用者配置檔圖示。
   1. 選擇**使用者設定**。
    ![使用者設定的選單指令](media/solution-template-Databricks-notebook/user-setting.png)
   1. 在 **「訪問權杖」** 選項卡下選擇 **「生成新權杖**」。
   1. 選取 [產生]****。

    !['產生"按鈕](media/solution-template-Databricks-notebook/generate-new-token.png)

   *保存存取權杖*以供以後用於創建資料磚塊連結服務。 存取權杖`dapi32db32cbb4w6eee18b7d87e45exxxxxx`。

## <a name="how-to-use-this-template"></a>如何使用此樣本

1. 跳到使用**Azure 資料磚塊範本的轉換**,並創建用於以下連接的新連結服務。

   ![連線設定](media/solution-template-Databricks-notebook/connections-preview.png)

    - **源 Blob 連接**-存取源資料。

       在本練習中,可以使用包含源檔的公共 Blob 存儲。 參考以下配置螢幕截圖。 使用以下**SAS 網址**連線到來源儲存(唯讀存取):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![認證與 SAS 網址的選擇](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **目標 Blob 連線**-儲存複製的資料。

       在 **「新建連結服務」** 視窗中,選擇接收器儲存 Blob。

       ![將儲存 Blob 作為新的連結服務](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure 資料塊**- 連接到資料磚群集。

        使用以前生成的存取金鑰創建與Databricks連結的服務。 如果您有*互動式群集*,可以選擇選擇互動式群集。 此示例使用 **「新建作業群集」** 選項。

        ![連接到叢集的選擇](media/solution-template-Databricks-notebook/databricks-connection.png)

1. 選擇**此樣本**。 您將看到已建立的管道。

    ![建立管線](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>管管介紹和設定

在新管道中,大多數設置都自動配置預設值。 查看管道的配置並進行任何必要的更改。

1. 在**驗證**活動**可用性標誌**中,驗證來源**資料集**值`SourceAvailabilityDataset`是否設定為 您之前創建的。

   ![來源資料集值](media/solution-template-Databricks-notebook/validation-settings.png)

1. 在 **「將資料**活動**檔案複製到 blob」** 中,檢查 **「源**」和「**接收器」** 選項卡。 如有必要,更改設置。

   - **來源**選項![卡 「源」選項卡](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **接收器**選項![卡「接收器」選項卡](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. 在**筆記本**活動**轉換中**,根據需要查看和更新路徑和設置。

   **數據磚連結服務**應預填充上一步的值,如圖所示![: 數據磚塊鏈接服務的填充值](media/solution-template-Databricks-notebook/notebook-activity.png)

   要檢查**筆記本**設定:
  
    1. 選擇**設定設定選項**卡。對於**筆記本路徑**,驗證默認路徑是否正確。 您可能需要瀏覽並選擇正確的筆記本路徑。

       ![筆記本路徑](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. 展開**基本參數**選擇器並驗證參數是否與以下螢幕截圖中顯示的參數匹配。 這些參數從數據工廠傳遞到數據磚塊筆記本。

       ![基本參數](media/solution-template-Databricks-notebook/base-parameters.png)

1. 驗證**導管參數**與以下螢幕截圖中顯示![的內容符合: 導管參數](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 連接到數據集。

   - **來源可用的資料集**- 檢查來源資料是否可用。

     ![來源可用的資料集的連結服務與檔案路徑的選擇](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **源檔案資料集**- 存取來源資料。

       ![來源資料集連結服務與檔案路徑的選擇](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **目標檔案資料集**-將資料複製到接收器目標位置。 輸入下列值：

     - **連結服務** - `sinkBlob_LS`,在上一步中創建。

     - **檔案路徑** - `sinkdata/staged_sink`。

       ![目的檔案資料集的連結服務和檔案路徑的選擇](media/solution-template-Databricks-notebook/destination-dataset.png)

1. 選擇**調試**以運行管道。 您可以找到指向 Databricks 日誌的連結,以獲取更詳細的 Spark 日誌。

    ![從輸出連結到資料磚區誌](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    您還可以使用 Azure 儲存資源管理員驗證資料檔。

    > [!NOTE]
    > 對於與資料工廠管道運行相關的操作,此示例將管道運行 ID 從資料工廠追加到輸出資料夾。 這有助於跟蹤每次運行生成的檔。
    > ![附加導管執行識別碼](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
