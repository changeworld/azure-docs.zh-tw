---
title: 資料工具，以增量方式複製新檔並更新檔
description: 創建 Azure 資料工廠，然後使用"複製資料"工具基於上次修改日期增量載入新檔。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131187"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>使用"複製資料"工具，基於上次修改日期增量複製新檔和更改檔

在本教程中，您將使用 Azure 門戶創建資料工廠。 然後，您將使用"複製資料"工具創建一個管道，該管道僅根據從 Azure Blob 存儲到 Azure Blob 存儲**的上次修改日期**增量複製新檔並複製新檔。

這樣，ADF 將掃描源存儲中的所有檔，按其 Last 修改日期應用檔篩選器，並且僅從上次後將新的和更新的檔案複製到目標存儲。  請注意，如果您讓 ADF 掃描大量檔，但只將一些檔案複製到目標，您仍會期望由於檔掃描而延長持續時間也非常耗時。   

> [!NOTE]
> 如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。

在本教學課程中，您將會執行下列工作：

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線。
> * 監視管線和活動執行。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**：如果沒有 Azure 訂閱，請先創建一個[免費帳戶](https://azure.microsoft.com/free/)。"
* **Azure 存儲帳戶**：使用 Blob 存儲作為_源_和_接收器_資料存儲。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-account-create.md)中的指示。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 存儲中創建兩個容器

通過執行這些步驟為本教程準備 Blob 存儲。

1. 創建名為**源**的容器。 可以使用各種工具執行此任務，例如[Azure 存儲資源管理器](https://storageexplorer.com/)。

2. 創建名為**目標的**容器。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側功能表上，選擇 **"創建資源** > **資料 + 分析** > **資料工廠**" ：

   ![在 [新增] 窗格中選取資料處理站](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站]**** 頁面的 [名稱]**** 下，輸入 **ADFTutorialDataFactory**。

   資料處理站的名稱必須是「全域唯一」__ 的名稱。 您可能會收到下列錯誤訊息：

   ![新增資料處理站錯誤訊息](./media/doc-common-process/name-not-available-error.png)

   如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 例如，使用**您的名稱**__**ADFTutorialDataFactory**。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
3. 選擇將在其中創建新資料工廠的 Azure**訂閱**。
4. 針對 [資源群組]****，採取下列其中一個步驟︰

    * 選擇 **"使用現有**資源"並從下拉清單中選擇現有資源組。

    * 選擇 **"創建新**"並輸入資源組的名稱。 
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。

5. 在**版本**下，選擇**V2**。
6. 在**位置**下，選擇資料工廠的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料存儲（例如，Azure 存儲和 SQL 資料庫）和計算資料工廠使用的資料可以在其他位置和區域進行計算。
8. 選取 [建立]****。
9. 建立完成後，隨即會顯示 **Data Factory** 首頁。
10. 要在單獨的選項卡上打開 Azure 資料工廠使用者介面 （UI），請選擇 **"作者&監視器"** 磁貼。

    ![Data Factory 首頁](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用複製資料工具建立管線

1. 在"**讓我們開始"** 頁上，選擇 **"複製資料**"標題以打開"複製資料"工具。

   ![複製資料工具圖格](./media/doc-common-process/get-started-page.png)

2. 在 **"屬性"** 頁上，執行以下步驟：

    a. 在**任務名稱**下，輸入**增量複製從Blob管道**。

    b. 在**任務節奏**或**任務計畫下**，選擇 **"按計劃定期運行**"。

    c. 在 **"觸發類型"** 下，選擇 **"輪轉視窗**"。

    d. 在 **"重複"** 下，輸入**15 分鐘。**

    e. 選取 [下一步]****。

    Data Factory 使用者介面會使用指定的工作名稱建立管線。

    ![屬性頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. 在 [來源資料存放區] **** 頁面上，完成下列步驟：

    a. 選擇 **= 創建新連接**，以添加連接。

    b. 從資源庫選取 [Azure Blob 儲存體]****，然後選取 [繼續]****。

    ![來源資料存放區頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. 在 **"新建連結服務（Azure Blob 存儲）"** 頁上，從**存儲帳戶名稱**清單中選擇存儲帳戶。 測試連接，然後選擇 **"創建**"。

    d. 選擇新創建的連結服務，然後選擇 **"下一步**"。

   ![來源資料存放區頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在 [選擇輸入檔案或資料夾]**** 頁面上，完成下列步驟︰

    a. 流覽並選擇**源**資料夾，然後**選擇**。

    ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. 在**檔載入行為**下，選擇**增量載入：上次修改日期**。

    c. 檢查**二進位副本**並選擇 **"下一步**"。

     ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. 在 **"目標資料存儲"** 頁上，選擇您創建的**Azure Blob 存儲**。 這是與來源資料存儲相同的存儲帳戶。 然後選擇 **"下一步**"。

6. 在 [選擇輸出檔案或資料夾]**** 頁面上，完成下列步驟︰

    a. 流覽並選擇**目標**資料夾，然後**選擇**。

    ![選擇輸出檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. 選取 [下一步]****。

7. 在 [設定]**** 頁面上，選取 [下一步]****。

8. 在 **"摘要"** 頁上，查看設置，然後選擇 **"下一步**"。

    ![摘要頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. 在**部署頁面**上選取 [監視]**** 來監視管線 (工作)。

    ![部署頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. 請注意，系統會自動選取左側的 [監視]**** 索引標籤。 應用程式切換到 **"監視器"** 選項卡。您將看到管道的狀態。 選取 [重新整理]**** 可重新整理清單。 按一下 PIPELINE **NAME**下的連結以查看活動運行詳細資訊或重新運行管道。 

    ![刷新清單並選擇"查看活動運行"](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 管道中只有一個活動（複製活動），因此您只看到一個條目。 有關複製操作的詳細資訊，請選擇**活動名稱**列下方**的"詳細資訊**"連結（眼鏡圖示）。 如需屬性的詳細資訊，請參閱[複製活動概觀](copy-activity-overview.md)。 

    ![複製活動正在管道中](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    由於 Blob 存儲帳戶中的**源**容器中沒有檔，因此您不會看到任何檔案複製到 Blob 存儲帳戶中**的目標**容器。

    ![源容器或目標容器中沒有檔](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. 創建一個空文字檔，並命名它**的檔1.txt。** 將此文字檔上載到存儲帳戶中的**源**容器。 您可以使用各種工具來執行這些工作，例如 [Azure 儲存體總管](https://storageexplorer.com/)。

    ![創建 file1.txt 並上載到源容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. 要返回 **"管道運行"** 視圖，請選擇 **"所有管道運行**"，然後等待再次自動觸發同一管道。  

    ![選擇所有管道運行](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 當第二個管道運行完成時，按照上述相同的步驟查看活動運行詳細資訊。  

    您將看到一個檔（file1.txt）已從**源**容器複製到 Blob 存儲帳戶**的目標**容器。

    ![File1.txt 已從源容器複製到目標容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. 創建另一個空文字檔並命名它**file2.txt**。 將此文字檔上載到 Blob 存儲帳戶中的**源**容器。

16. 重複第二個文字檔的步驟 13 和 14。 在下一次管道運行中，您將看到只有新檔 （file2.txt） 從**源**容器複製到存儲帳戶**的目標**容器。  

    還可以通過使用[Azure 存儲資源管理器](https://storageexplorer.com/)來掃描檔來驗證此情況。

    ![使用 Azure 存儲資源管理器掃描檔](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>後續步驟
提前到以下教程，瞭解如何在 Azure 上使用 Apache Spark 群集轉換資料：

> [!div class="nextstepaction"]
>[使用 Apache Spark 群集轉換雲中的資料](tutorial-transform-data-spark-portal.md)
