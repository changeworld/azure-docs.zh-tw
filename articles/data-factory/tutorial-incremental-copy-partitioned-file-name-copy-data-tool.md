---
title: 依時間分割區的檔案名增量複製新檔案
description: 創建 Azure 資料工廠,然後使用複製資料的工具僅根據時間分區的檔案名增量載入新檔。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 6cc089a1efc3f5960a8bca8a36063bb1019bbcc6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409404"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>使用複製資料工具,根據時間分區的檔案名增量複製新檔案

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在這個教學課程中，您會使用 Azure 入口網站來建立資料處理站。 然後,使用複製資料工具建立一個管道,該管道根據從 Azure Blob 儲存到 Azure Blob 儲存的時間分區檔名增量複製新檔案。

> [!NOTE]
> 如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線。
> * 監視管線和活動執行。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存帳戶**:使用 Blob 儲存作為_來源_和_接收器_資料儲存。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-account-create.md)中的指示。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 儲存中建立兩個容器

通過執行這些步驟為本教程準備 Blob 存儲。

1. 建立名為**源**的容器。  在容器中將資料夾路徑創建為**2020/03/17/03。** 建立空白文字檔,並將此命名為**file1.txt**。 將 file1.txt 上傳到儲存帳戶中的資料夾路徑**來源/2020/03/17/03。**  您可以使用各種工具來執行這些工作，例如 [Azure 儲存體總管](https://storageexplorer.com/)。

    ![上傳檔案](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > 請用您的 UTC 時間調整資料夾名稱。  例如,如果當前 UTC 時間為 2020 年 3 月 17 日淩晨 3:38,則可以根據**源/[年]/{月/天]/{小時}/** 創建資料夾路徑作為**源/2020/03/17/03/**

2. 建立名為**目標的**容器。 您可以使用各種工具來執行這些工作，例如 [Azure 儲存體總管](https://storageexplorer.com/)。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左邊選單上,選擇 **「建立資源** > **資料 + 分析** > **資料工廠**」 :

   ![在 [新增] 窗格中選取資料處理站](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站]**** 頁面的 [名稱]**** 下，輸入 **ADFTutorialDataFactory**。

    資料處理站的名稱必須是「全域唯一」__ 的名稱。 您可能會收到下列錯誤訊息：

   ![新增資料處理站錯誤訊息](./media/doc-common-process/name-not-available-error.png)

   如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 例如，使用**您的名稱**__**ADFTutorialDataFactory**。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
3. 選取要在其中建立新資料處理站的 Azure **訂用帳戶**。
4. 針對 [資源群組]****，採取下列其中一個步驟︰

    a. 選取 [使用現有的] ****，然後從下拉式清單選取現有的資源群組。

    b. 選取 [建立新的] ****，然後輸入資源群組的名稱。 
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。

5. 在 [版本]**** 下，選取 [V2]**** 作為版本。
6. 在**位置**下,選擇數據工廠的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (例如 Azure 儲存體和 SQL Database) 和計算 (例如 Azure HDInsight) 可位於其他地區和區域。
7. 選取 [建立]  。
8. 建立完成後，隨即會顯示 **Data Factory** 首頁。
9. 選取 [編寫與監視]**** 圖格，可在另一個索引標籤中啟動 Azure Data Factory 使用者介面 (UI)。

    ![Data Factory 首頁](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用複製資料工具建立管線

1. 在「**讓我們開始」** 頁上,選擇 **「複製資料**」 標題以啟動「複製資料」 工具。

   ![複製資料工具圖格](./media/doc-common-process/get-started-page.png)

2. 屬性**屬性「 頁面上**」執行以下步驟:

    a. 在**工作名稱**下,輸入**增量複製從Blob管道**。

    b. 在**工作節奏或任務計劃下**,選擇 **「按計劃定期運行**」。

    c. 在 **「觸發類型」** 下,選擇 **「翻轉視窗**」。。

    d. 在 **"重複"** 下,輸入**1 小時。**

    e. 選取 [下一步]  。

    Data Factory 使用者介面會使用指定的工作名稱建立管線。

    ![屬性頁面](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. 在 [來源資料存放區] **** 頁面上，完成下列步驟：

    a. 按一下 [+ 建立新連線]**** 以新增連線
    
    b. 從資源庫選取 [Azure Blob 儲存體]，然後選取 [繼續]。
    
    c. 在新**連結服務(Azure Blob 儲存)** 頁上,輸入連結服務的名稱。 選擇 Azure 訂閱,然後從 **「存儲帳戶」 名稱**清單中選擇儲存帳戶。 測試連接,然後選擇 **"創建**"。

    ![來源資料存放區頁面](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. 在 **「源資料儲存」** 頁上選擇新創建的連結服務,然後單擊「**下一步**」。

4. 在 [選擇輸入檔案或資料夾]**** 頁面上，執行以下步驟︰

    a. 瀏覽並選擇**來源**容器,然後**選擇**。

    ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. 在 **'檔案載入行為**' 下, 選擇**增量載入:時間分區資料夾/檔案名稱**。

    c. 將動態資料夾路徑寫成**源/[年]/{每月}/{day}/{小時},** 並更改格式,如下圖所示。 檢查**二進位副本**,然後按下 **"下一步**"。

    ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. 在 **「目標資料儲存」** 頁上,選擇**AzureBlob 儲存**,這是與資料來源儲存相同的儲存帳戶,然後按下「**下一步**」。

    ![目的地資料存放區頁面](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. 在**選擇輸出檔或「資料夾**」 頁上,執行以下步驟:

    a. 瀏覽並選擇**目標**資料夾,然後按一下「**選擇**」。

    b. 將動態資料夾路徑寫成**目標/[年]/{每月}/{day}/{小時},** 並將格式更改為以下內容:

    ![選擇輸出檔案或資料夾](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. 按 [下一步]  。

    ![選擇輸出檔案或資料夾](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. 在 [設定]**** 頁面上，選取 [下一步]****。

8. 在 [摘要]**** 頁面上檢閱設定，然後選取 [下一步]****。

    ![摘要頁面](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. 在**部署頁面**上選取 [監視]**** 來監視管線 (工作)。
    ![部署頁面](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. 請注意，系統會自動選取左側的 [監視]**** 索引標籤。  當管道自動觸發時(大約一小時后),您需要等待管道的運行。 運行時,按一下管道名稱連結**DeltaCopyFromBlobPipeline**以查看活動運行詳細資訊或重新執行管道。 選取 [重新整理]**** 可重新整理清單。

    ![監視管線回合](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 調整**來源****與目標**欄位的欄寬度(如有必要)以顯示更多詳細資訊,您可以看到源檔(file1.txt)已從*源/2020/03/17/03/* 目標/具有相同檔名*複製到目標/2020/03/17/03。* 

    ![監視管線回合](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    還可以透過使用 Azure 儲存資源管理員(掃描https://storageexplorer.com/)檔案) 來驗證相同的內容。

    ![監視管線回合](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. 建立另一個空文字檔,新名稱為**file2.txt**。 將 file2.txt 檔上傳到儲存帳戶中的資料夾路徑**來源/2020/03/17/04。** 您可以使用各種工具來執行這些工作，例如 [Azure 儲存體總管](https://storageexplorer.com/)。

    > [!NOTE]
    > 您可能知道需要建立新的資料夾路徑。 請用您的 UTC 時間調整資料夾名稱。  例如,如果當前 UTC 時間是 2020 年 3 月 17 日淩晨 4:20,則可以按 **[年]/{月]/天/{小時}的規則**將資料夾路徑創建為**源/2020/03/17/04/。**

13. 要返回 **「管道運行」** 檢視,請選擇 **「所有管道運行**」,然後等待同一管道在一小時後再次自動觸發。  

    ![監視管線回合](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. 選擇新的**DeltaCopyFromBlobPipeline**連結,以便第二個管道在管道運行時運行,並執行相同的操作來詳細資訊。 您將看到源檔 (file2.txt) 已從**來源/2020/03/17/04/** 複製到**目標/2020/03/17/04/** 具有相同的檔案名。 還可以通過使用 Azure 儲存資源管理員**destination**(掃描https://storageexplorer.com/)目標容器中的檔案)來驗證相同的內容。


## <a name="next-steps"></a>後續步驟
進入下列教學課程，以了解如何在 Azure 上使用 Spark 叢集來轉換資料：

> [!div class="nextstepaction"]
>[在雲端中使用 Spark 叢集轉換資料](tutorial-transform-data-spark-portal.md)
