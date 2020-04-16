---
title: 資料工具,以增量方式複製新檔案並更新檔案
description: 創建 Azure 資料工廠,然後使用複製資料工具基於上次修改日期增量載入新檔案。
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
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399492"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>使用複製資料工具,以上次修改日期的複製新檔案和變更檔案

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教學中,您將使用 Azure 門戶建立數據工廠。 然後,您將使用"複製資料"工具創建僅從 Azure Blob 儲存到 Azure Blob 儲存的增量複製新檔案和更改檔案的管道。 它用於`LastModifiedDate`確定要複製的檔。

完成此處的步驟後,Azure 資料工廠將掃描源存儲中的所有檔,`LastModifiedDate`通過應用檔案篩選器,並僅複製到目標存儲中自上次更新的檔。 請注意,如果資料工廠掃描大量檔,您仍應期望持續時間長。 文件掃描非常耗時,即使複製的數據量減少了。

> [!NOTE]
> 如果您不熟悉 Data Factory，請參閱 [Data Factory 簡介](introduction.md)。

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線。
> * 監視管線和活動執行。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存帳戶**:對源和接收器數據存儲使用 Blob 存儲。 如果沒有 Azure 儲存帳戶,請按照["創建存儲帳戶](../storage/common/storage-account-create.md)"中的說明操作。

## <a name="create-two-containers-in-blob-storage"></a>在 Blob 儲存中建立兩個容器

通過完成以下步驟為本教程準備 Blob 儲存:

1. 建立名為**源**的容器。 可以使用各種工具執行此工作,如 Azure[儲存資源管理員](https://storageexplorer.com/)。

2. 建立名為**目標的**容器。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左窗格中選取 [建立資源]****。 選擇**分析** > **資料工廠**:

   ![選擇資料工廠](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站]**** 頁面的 [名稱]**** 下，輸入 **ADFTutorialDataFactory**。

   資料處理站的名稱必須是「全域唯一」的名稱。 您可能會收到以下錯誤訊息:

   ![名稱 無法使用錯誤訊息](./media/doc-common-process/name-not-available-error.png)

   如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 例如，使用**您的名稱**__**ADFTutorialDataFactory**。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
3. 在 **「訂閱」** 下,選擇將在其中創建新數據工廠的 Azure 訂閱。
4. 在**資源組**下,執行以下步驟之一:

    * 選擇 **「使用現有**資源」,然後在清單中選擇現有資源群組。

    * 選擇 **"創建新**",然後輸入資源組的名稱。
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。

5. 在 [版本]**** 下，選取 [V2]****。
6. 在 [位置]**** 下，選取資料處理站的位置。 清單中僅顯示受支援的位置。 數據存儲(例如,Azure 存儲和 Azure SQL 資料庫)和計算數據工廠使用的數據可以在其他位置和區域進行計算。
8. 選取 [建立]  。
9. 創建數據工廠後,將顯示數據工廠主頁。
10. 在單獨的選項卡上開啟 Azure 資料工廠使用者介面 (UI),請選擇 **「作者&監視器」** 磁貼:

    ![Data Factory 首頁](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用複製資料工具建立管線

1. 在「**讓我們開始」** 頁上,選擇 **「複製資料**」磁貼以開啟「複製資料」工具:

   ![複製資料磁貼](./media/doc-common-process/get-started-page.png)

2. 屬性**屬性「 頁面上**」執行以下步驟:

    a. 在**工作名稱**下,輸入**增量複製從Blob管道**。

    b. 在**工作節奏或任務計劃下**,選擇 **「按計劃定期運行**」。

    c. 在 **「觸發類型」** 下,選擇 **「翻轉」 視窗**。

    d. 在 **"重複"** 下,輸入**15 分鐘。**

    e. 選取 [下一步]  。

    資料工廠建立具有指定任務名稱的管道。

    ![複製資料屬性頁](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. 在**來源資料儲存**頁上,完成以下步驟:

    a. 選擇 **「建立新連線**」 以新增連線。

    b. 從庫中選擇**Azure Blob 儲存**,然後選擇 **"繼續**"

    ![選擇 Azure 部落格儲存](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. 在 **「新增連結服務(Azure Blob 儲存)」** 頁上,從**儲存帳戶名稱**清單中選擇儲存帳戶。 測試連接,然後選擇 **"創建**"。

    d. 選擇新的連結服務,然後選擇 **「下一步**」 :

   ![選擇新的連結服務](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在 [選擇輸入檔案或資料夾]**** 頁面上，完成下列步驟︰

    a. 瀏覽並選擇**來源**資料夾, 然後**選擇**。

    ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. 在**檔案載入行為**下,選擇**增量載入:上次修改日期**。

    c. 選擇**二進位副本**,然後選擇 **「下一步**」 :

     ![選擇輸入檔案或資料夾頁](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. 在 **「目標資料儲存」** 頁上,選擇您創建的**AzureBlob 儲存**服務。 這是與源數據存儲相同的存儲帳戶。 然後選取 [下一步]  。

6. 在 [選擇輸出檔案或資料夾]**** 頁面上，完成下列步驟︰

    a. 瀏覽並選擇**目標**資料夾,然後**選擇**:

    ![選擇輸出檔案或資料夾頁](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. 選取 [下一步]  。

7. 在 [設定]**** 頁面上，選取 [下一步]****。

8. 在**摘要的頁面**上,查看設置,然後選擇 **「下一步**」 。

    ![摘要頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. 在**部署頁面**上選取 [監視]**** 來監視管線 (工作)。

    ![部署頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. 請注意，系統會自動選取左側的 [監視]**** 索引標籤。 應用程式切換到 **「監視器」** 選項卡。您將看到管道的狀態。 選取 [重新整理]**** 可重新整理清單。 選擇 **「管道名稱**」 的連結以檢視活動執行詳細資訊或再次執行管道。

    ![重新更新清單並檢視活動執行詳細資訊](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 管道中只有一個活動(複製活動),因此您只看到一個條目。 有關複製操作的詳細資訊,請選擇**活動名稱**列中**的詳細資訊**連結(眼鏡圖示)。 有關屬性的詳細資訊,請參閱[複製活動概述](copy-activity-overview.md)。

    ![複製導管中的活動](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    由於 Blob 儲存帳號中的來源容器中沒有檔案,因此您不會看到任何檔案複製到帳戶中的目標容器:

    ![來源容器或目標容器中沒有檔案](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. 建立空文字檔案,並命名它**的檔案1.txt。** 將此文字檔上傳到儲存帳戶中的來源容器。 可以使用各種工具來執行這些工作,如[Azure 儲存資源管理員](https://storageexplorer.com/)。

    ![建立 file1.txt 並將上傳到來源容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. 要返回**管道運行**檢視,請選擇 **「所有管道運行**」,然後等待同一管道再次自動觸發。  

    ![選擇"所有管道運行"](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 當第二個管道運行完成時,按照前面提到的相同的步驟來查看活動運行詳細資訊。  

    您將看到一個檔案(file1.txt)已從來源容器複製到 Blob 儲存帳戶的位址:

    ![file1.txt 已從來源的容器複製到目標容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. 建立另一個空文字檔案並命名**file2.txt**。 將此文字檔上載到 Blob 儲存帳戶中的來源容器。

16. 對第二個文本檔重複步驟 13 和 14。 您將看到,在此管道運行期間,只有新檔 (file2.txt) 從源容器複製到存儲帳戶的目標容器。  

    可驗證是否僅複製了一個檔案,使用[Azure 儲存資源管理員](https://storageexplorer.com/)掃描檔案:

    ![使用 Azure 儲存資源管理員掃描檔案](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>後續步驟
跳到以下教學,瞭解如何在 Azure 上使用 Apache Spark 叢集轉換資料:

> [!div class="nextstepaction"]
>[使用 Apache Spark 叢集轉換雲中的資料](tutorial-transform-data-spark-portal.md)
