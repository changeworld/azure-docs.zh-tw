---
title: 以累加方式複製新檔案和更新檔案的資料工具
description: 建立 Azure data factory，然後使用資料複製工具，以累加方式根據 LastModifiedDate 載入新的檔案。
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
ms.date: 6/10/2020
ms.openlocfilehash: 402214da75bffd278e12db94f089d64acd62221e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84730133"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>使用資料複製工具，以累加方式根據 LastModifiedDate 複製新的和變更的檔案

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教學課程中，您將使用 Azure 入口網站來建立 data factory。 然後，您將使用資料複製工具來建立管線，以將新的和變更的檔案以累加方式從 Azure Blob 儲存體複製到 Azure Blob 儲存體。 它會使用 `LastModifiedDate` 來決定要複製的檔案。

當您完成此處的步驟之後，Azure Data Factory 將掃描來源存放區中的所有檔案、套用檔案篩選， `LastModifiedDate` 然後只複製到最後一次新增或已更新的檔案。 請注意，如果 Data Factory 掃描大量的檔案，您應該仍預期會有很長的持續時間。 檔案掃描需要花費很多時間，即使已減少複製的資料量也是如此。

> [!NOTE]
> 如果您不熟悉 Data Factory，請參閱 [Data Factory 簡介](introduction.md)。

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線。
> * 監視管線和活動執行。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存體帳戶**：針對來源和接收資料存放區使用 Blob 儲存體。 如果您沒有 Azure 儲存體帳戶，請遵循 [建立儲存體帳戶](../storage/common/storage-account-create.md)中的指示。

## <a name="create-two-containers-in-blob-storage"></a>在 Blob 儲存體中建立兩個容器

完成下列步驟，以準備教學課程的 Blob 儲存體：

1. 建立名為 **source**的容器。 您可以使用各種工具來執行這項工作，例如 [Azure 儲存體總管](https://storageexplorer.com/)。

2. 建立名為 **destination**的容器。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左窗格中選取 [建立資源]****。 選取**分析**  >  **Data Factory**：

   ![選取 Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站] 頁面的 [名稱] 下，輸入 **ADFTutorialDataFactory**。

   資料處理站的名稱必須是「全域唯一」的名稱。 您可能會收到此錯誤訊息：

   ![無法使用名稱的錯誤訊息](./media/doc-common-process/name-not-available-error.png)

   如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 例如，使用**您的名稱****ADFTutorialDataFactory**。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
3. 在 [ **訂**用帳戶] 底下，選取您將在其中建立新 data Factory 的 Azure 訂用帳戶。
4. 在 [ **資源群組**] 底下，執行下列其中一個步驟：

    * 選取 [ **使用現有** 的]，然後在清單中選取現有的資源群組。

    * 選取 [ **建立新** 的]，然後輸入資源群組的名稱。
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。

5. 在 [版本] 下，選取 [V2]。
6. 在 [位置]**** 下，選取資料處理站的位置。 清單中只會顯示支援的位置。 資料存放區 (例如 Azure 儲存體和 Azure SQL Database) 和計算 (例如，您的 data factory 所使用的 Azure HDInsight) 可以位於其他位置和區域。
8. 選取 [建立]****。
9. 建立資料處理站之後，data factory 的首頁隨即出現。
10. 若要在另一個索引標籤上開啟 Azure Data Factory 使用者介面 (UI) ，請選取 [ **作者 & 監視器** ] 磚：

    ![Data Factory 首頁](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用複製資料工具建立管線

1. 在 [ **開始** 使用] 頁面上，選取 [ **資料複製** ] 圖格以開啟資料複製工具：

   ![資料複製磚](./media/doc-common-process/get-started-page.png)

2. 在 [ **屬性** ] 頁面上，採取下列步驟：

    a. 在 [工作 **名稱**] 下，輸入 **DeltaCopyFromBlobPipeline**。

    b. 在 [工作 **步調] 或**[工作排程] 底下，選取 [依 **排程定期執行**]。

    c. 在 [ **觸發程式類型**] 下，選取 [ **輪轉視窗]**。

    d. 在 **[週期**] 下，輸入 **15 分鐘 (s) **。

    e. 選取 [下一步]  。

    Data Factory 會使用指定的工作名稱建立管線。

    ![複製資料屬性頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. 在 [ **來源資料存放區** ] 頁面上，完成下列步驟：

    a. 選取 [  **建立新連接** ] 以新增連接。

    b. 從資源庫中選取 **Azure Blob 儲存體** ，然後選取 [ **繼續**]：

    ![選取 Azure Blog 儲存體](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. 在 [ **新增連結服務] (Azure Blob 儲存體) ** 頁面上，從 [ **儲存體帳戶名稱** ] 清單中選取您的儲存體帳戶。 測試連接，然後選取 [ **建立**]。

    d. 選取新的連結服務，然後選取 **[下一步]**：

   ![選取新的連結服務](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在 [選擇輸入檔案或資料夾] 頁面上，完成下列步驟︰

    a. 流覽並選取 **源** 資料夾，然後選取 **[選擇**]。

    ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. 在 [檔案 **載入行為**] 底下，選取 [ **增量載入： LastModifiedDate**]。

    c. 選取 [ **二進位檔複製** ]，然後選取 **[下一步]**：

     ![選擇輸入檔案或資料夾頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. 在 [ **目的地資料存放區** ] 頁面上，選取您所建立的 **AzureBlobStorage** 服務。 這是與來源資料存放區相同的儲存體帳戶。 然後選取 [下一步]。

6. 在 [選擇輸出檔案或資料夾]**** 頁面上，完成下列步驟︰

    a. 流覽並選取 **目的** 資料夾，然後選取 **[選擇**：

    ![選擇輸出檔案或資料夾頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. 選取 [下一步]  。

7. 在 [設定] 頁面上，選取 [下一步]。

8. 在 [ **摘要** ] 頁面上，檢查設定，然後選取 **[下一步]**。

    ![摘要頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. 在**部署頁面**上選取 [監視]**** 來監視管線 (工作)。

    ![部署頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. 請注意，系統會自動選取左側的 [監視] 索引標籤。 應用程式會切換至 [ **監視** ] 索引標籤。您會看到管線的狀態。 選取 [重新整理] 即可重新整理清單。 選取 [ **管線名稱** ] 下的連結以查看活動執行詳細資料，或重新執行管線。

    ![重新整理清單並查看活動執行詳細資料](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. 管線中只有一個活動 (複製活動) ，因此您只會看到一個專案。 如需複製作業的詳細資訊，請在 [**活動名稱**] 欄中選取 [眼鏡]) 圖示 (的 [**詳細資料**] 連結。 如需屬性的詳細資訊，請參閱 [複製活動總覽](copy-activity-overview.md)。

    ![管線中的複製活動](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    由於 Blob 儲存體帳戶的來源容器中沒有任何檔案，因此您不會看到任何複製到帳戶中目的地容器的檔案：

    ![來源容器或目的地容器中沒有任何檔案](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. 建立空的文字檔，並將它命名為 **file1.txt**。 將此文字檔上傳至您儲存體帳戶中的來源容器。 您可以使用各種工具來執行這些工作，例如 [Azure 儲存體總管](https://storageexplorer.com/)。

    ![建立 file1.txt，並將其上傳至來源容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. 若要返回 [ **管線執行** ] 視圖，請選取 [ **所有管線執行**]，並等候相同的管線再次自動觸發。  

14. 當第二個管線執行完成時，請遵循先前所述的相同步驟來檢查活動執行的詳細資料。  

    您會看到已從來源容器將一個檔案 ( # A0) 複製到 Blob 儲存體帳戶的目的地容器：

    ![file1.txt 已從來源容器複製到目的地容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. 建立另一個空白文字檔，並將它命名為 **file2.txt**。 將此文字檔上傳至 Blob 儲存體帳戶中的來源容器。

16. 針對第二個文字檔重複步驟13和14。 您會看到在此管線執行期間，只會從來源容器將新的檔案複製到儲存體帳戶的目的地容器， ( # A0) 。  

    您也可以使用 [Azure 儲存體總管](https://storageexplorer.com/) 來確認已複製一個檔案，以掃描檔案：

    ![使用 Azure 儲存體總管來掃描檔案](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>接下來的步驟
移至下列教學課程，瞭解如何使用 Azure 上的 Apache Spark 叢集來轉換資料：

> [!div class="nextstepaction"]
>[使用 Apache Spark 叢集來轉換雲端中的資料](tutorial-transform-data-spark-portal.md)
