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
ms.date: 04/27/2020
ms.openlocfilehash: f9dc11bd046bdc3a8913b4b05f1b68b84c9736c4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438444"
---
# <a name="transformation-with-azure-databricks"></a>使用 Azure Databricks 進行轉換

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教學課程中，您會在 Azure Data Factory 中建立包含 **驗證**、 **複製資料**和 **筆記本** 活動的端對端管線。

- **驗證** 可確保您的源資料集已準備好供下游取用，然後您才會觸發複製和分析作業。

- **複製資料** 會將源資料集複製到接收儲存體，並在 Azure Databricks 筆記本中掛接為 DBFS。 如此一來，Spark 就可以直接使用資料集。

- **筆記本** 會觸發可轉換資料集的 Databricks 筆記本。 它也會將資料集新增至已處理的資料夾或 Azure Azure Synapse Analytics (先前的 SQL 資料倉儲) 。

為了簡單起見，本教學課程中的範本不會建立排程的觸發程式。 如有必要，您可以新增一個。

![管線的圖表](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>必要條件

- 具有名為的容器的 Azure Blob 儲存體帳戶， `sinkdata` 用來作為接收。

  記下儲存體帳戶名稱、容器名稱和存取金鑰。 您稍後會在範本中需要這些值。

- Azure Databricks 工作區。

## <a name="import-a-notebook-for-transformation"></a>匯入筆記本以進行轉換

若要將 **轉換** 筆記本匯入至 Databricks 工作區：

1. 登入您的 Azure Databricks 工作區，然後選取 [匯 **入**]。
       ![用來匯入工作區的功能表命令 ](media/solution-template-Databricks-notebook/import-notebook.png) ，您的工作區路徑可以與顯示的不同，但請記住它以供稍後使用。
1. 選取 [匯 **入來源： URL**]。 在文字方塊中，輸入 `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` 。

   ![匯入筆記本的選項](media/solution-template-Databricks-notebook/import-from-url.png)

1. 現在讓我們使用您的儲存體連接資訊來更新 **轉換** 筆記本。

   在匯入的筆記本中，移至 **命令 5** ，如下列程式碼片段所示。

   - `<storage name>`將和取代 `<access key>` 為您自己的儲存體連接資訊。
   - 將儲存體帳戶與容器搭配使用 `sinkdata` 。

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
   1. 在 Databricks 工作區中，選取右上方的使用者設定檔圖示。
   1. 選取 [ **使用者設定**]。
    ![使用者設定的功能表命令](media/solution-template-Databricks-notebook/user-setting.png)
   1. 選取 [**存取權杖**] 索引標籤底下的 [**產生新的權杖**]。
   1. 選取 [產生]。

    ![[產生] 按鈕](media/solution-template-Databricks-notebook/generate-new-token.png)

   *儲存存取權杖* ，以供稍後用來建立 Databricks 連結服務。 存取權杖看起來會像這樣 `dapi32db32cbb4w6eee18b7d87e45exxxxxx` 。

## <a name="how-to-use-this-template"></a>如何使用此範本

1. 移至 Azure Databricks 範本的 **轉換** ，並針對下列連接建立新的連結服務。

   ![連接設定](media/solution-template-Databricks-notebook/connections-preview.png)

    - **來源 Blob 連接** -用來存取來源資料。

       針對此練習，您可以使用包含來源檔案的公用 blob 儲存體。 請參考下列設定的螢幕擷取畫面。 使用下列 **SAS URL** 連接到來源儲存體 (唯讀存取) ：

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![驗證方法和 SAS URL 的選取專案](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **目的地 Blob 連接** -用以儲存複製的資料。

       在 [ **新增連結服務** ] 視窗中，選取您的接收儲存體 blob。

       ![接收儲存體 blob 做為新的連結服務](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** -連接到 Databricks 叢集。

        使用您先前產生的存取金鑰來建立 Databricks 連結的服務。 您可以選擇選取 *互動式* 叢集（如果有的話）。 此範例會使用 [ **新增作業** 叢集] 選項。

        ![用於連接到叢集的選項](media/solution-template-Databricks-notebook/databricks-connection.png)

1. 選取 [使用此範本]。 您會看到建立的管線。

    ![建立管線](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>管線簡介和設定

在新的管線中，大部分設定都會自動以預設值設定。 檢查管線的設定，並進行任何必要的變更。

1. 在 [ **驗證** 活動 **可用性] 旗**標中，確認源 **資料集** 值設定為 `SourceAvailabilityDataset` 您稍早建立的值。

   ![源資料集值](media/solution-template-Databricks-notebook/validation-settings.png)

1. 在 [ **複製資料** 活動 **檔到 blob**] 中，檢查 [ **來源** ] 和 [ **接收** ] 索引標籤。 視需要變更設定。

   - **來源**索引標籤來源索引標籤 ![](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **接收**索引標籤接收索引標籤 ![](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. 在 [ **筆記本** 活動] **轉換**中，檢查並視需要更新路徑和設定。

   **Databricks 連結服務** 應預先填入上一個步驟中的值，如下所示：已 ![ 填入 Databricks 連結服務的值](media/solution-template-Databricks-notebook/notebook-activity.png)

   若要檢查 **筆記本** 設定：
  
    1. 選取 [ **設定** ] 索引標籤。針對 [ **筆記本路徑**]，確認預設路徑是正確的。 您可能需要流覽並選擇正確的筆記本路徑。

       ![筆記本路徑](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. 展開 **基底參數** 選取器，並確認參數符合下列螢幕擷取畫面中顯示的內容。 這些參數會從 Data Factory 傳遞到 Databricks 筆記本。

       ![基底參數](media/solution-template-Databricks-notebook/base-parameters.png)

1. 確認 **管線參數** 符合下列螢幕擷取畫面中顯示的內容： ![ 管線參數](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 連接到您的資料集。

    >[!NOTE]
    >在下列資料集中，已自動在範本中指定檔案路徑。 如果需要任何變更，請確定您已指定 **容器** 和 **目錄** 的路徑，以防發生任何連接錯誤。

   - **SourceAvailabilityDataset** -檢查來源資料是否可用。

     ![SourceAvailabilityDataset 連結服務和檔案路徑的選取專案](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** -存取來源資料。

       ![SourceFilesDataset 連結服務和檔案路徑的選取專案](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** -將資料複製到接收目的地位置。 輸入下列值：

     - **連結的服務**  -  `sinkBlob_LS` ，在上一個步驟中建立。

     - 檔案**路徑**  -  `sinkdata/staged_sink` 。

       ![DestinationFilesDataset 連結服務和檔案路徑的選取專案](media/solution-template-Databricks-notebook/destination-dataset.png)

1. 選取 [ **Debug** ] 以執行管線。 您可以找到 Databricks 記錄的連結，以取得更詳細的 Spark 記錄。

    ![從輸出 Databricks 記錄的連結](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    您也可以使用 Azure 儲存體總管來驗證資料檔案。

    > [!NOTE]
    > 為了與 Data Factory 管線執行相互關聯，此範例會將管線執行識別碼從 Data Factory 附加至輸出檔案夾。 這有助於追蹤每次執行所產生的檔案。
    > ![附加的管線執行識別碼](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
