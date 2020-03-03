---
title: Azure Databricks 的轉換
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
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227812"
---
# <a name="transformation-with-azure-databricks"></a>Azure Databricks 的轉換

在本教學課程中，您會建立端對端管線，其中包含 Data Factory 中的**驗證**、**複製**和**筆記本**活動。

-   **驗證**活動是用來確保在觸發複製和分析工作之前，源資料集已準備好進行下游取用。

-   **複製**活動會將來源檔案/資料集複製到接收儲存體。 接收儲存體會在 Databricks Notebook 中掛接為 DBFS，讓 Spark 可以直接取用資料集。

-   **Databricks 筆記本**活動會觸發 Databricks 筆記本，以轉換資料集，並將其新增至已處理的資料夾/SQL DW。

為了簡化此範本，此範本並不會建立排程的觸發程序。 您可以視需要加以新增。

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>必要條件

1. 建立 **Blob 儲存體帳戶**，以及要作為`sinkdata`接收端**的容器** 。 請記下**儲存體帳戶名稱**、**容器名稱**和**存取金鑰**，因為後續在此範本中將會加以參考。

2. 確定您具有 **Azure Databricks 工作區**，或建立新的工作區。

3. 匯**入要轉換的筆記本**。 
    1. 在您的 Azure Databricks 中，參考下列螢幕擷取畫面，以將**轉換**筆記本匯入至 Databricks 工作區。 它不一定要位於與下面相同的位置，但請記住您稍後選擇的路徑。
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. 選取 [**從下列網址匯入]，** 然後在文字方塊中輸入下列 URL：
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. 現在，讓我們使用您的儲存體連接資訊來更新**轉換**筆記本。 移至上述已匯入筆記本中的**命令 5** （如下列程式碼片段所示），並以您自己的儲存體連接資訊取代 `<storage name>`和 `<access key>`。 請確定此帳戶是先前建立的相同儲存體帳戶，且包含 `sinkdata` 容器。

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

5.  產生供 Data Factory 用來存取 Databricks 的 **Databricks 存取權杖**。 **儲存存取權杖**以供稍後用來建立 Databricks 連結服務，其看起來像是 ' dapi32db32cbb4w6eee18b7d87e45exxxxxx '。

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>如何使用此範本

1.  移至 [**使用 Azure Databricks 範本轉換**]。 為下列連接建立新的連結服務。 
    
    ![連接設定](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **來源 Blob 連接**–用於存取來源資料。 
        
        在此範例中，您可以使用包含來源檔案的公用 Blob 儲存體。 請參考下列螢幕擷取畫面進行設定。 使用以下**SAS URL**連線至來源儲存體（唯讀存取）： 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **目的地 Blob 連接**–用於將資料複製到中。 
        
        在 [接收連結服務] 中，選取在**必要條件1中建立的儲存體**。

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** –用來連接到 Databricks 叢集。

        使用在必要條件 2. c 中**產生的存取**金鑰來建立 Databricks 連結服務。 如果您有*互動式叢集*，可加以選取。 (此範例使用 [新增作業叢集] 選項。)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. 選取 [**使用此範本**]，您會看到建立的管線，如下所示：
    
    ![建立管線](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>管線簡介和設定

在建立的新管線中，大部分的設定都是以預設值自動設定。 查看設定，並視需要更新以符合您自己的設定。 如需詳細資訊，請參閱下面的指示和螢幕擷取畫面以取得參考。

1.  針對執行來源可用性檢查，會建立驗證活動**可用性旗**標。 在上一個步驟中建立的*SourceAvailabilityDataset*會選取為 [資料集]。

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  建立複製活動檔案**到 blob** ，以將資料集從來源複製到接收。 請參考下列螢幕擷取畫面，了解複製活動中的來源和接收端組態。

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  隨即會建立「筆記本活動」**轉換**，並選取在上一個步驟中建立的連結服務。
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. 選取 [**設定**] 索引標籤。針對 [*筆記本路徑*]，範本預設會定義路徑。 您可能需要流覽並選取在**先決條件**2 中上傳的正確筆記本路徑。 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. 查看建立的*基底參數*，如螢幕擷取畫面所示。 它們會從 Data Factory 傳遞至 Databricks 筆記本。 

         ![基底參數](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **管線參數**的定義如下。

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. 設定資料集。
    1.  建立**SourceAvailabilityDataset**以檢查來源資料是否可用。

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** -用於複製來源資料。

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** –用於複製到接收/目的地位置。

        1.  已連結的服務- *sinkBlob_LS*在上一個步驟中建立。

        2.  檔案路徑- *sinkdata/staged_sink*。

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  選取 [ **Debug** ] 來執行管線。 您可以找到 Databricks 記錄的連結以取得更詳細的 Spark 記錄。

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    您也可以使用儲存體總管來確認資料檔案。 (為了建立與 Data Factory 管線執行的相互關聯，此範例會將管線執行識別碼從資料處理站附加至輸出資料夾。 如此，您即可往回追蹤每次執行所產生的檔案。)

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
