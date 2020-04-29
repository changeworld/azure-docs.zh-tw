---
title: 使用 Azure Data Factory 從 Office 365 載入資料
description: 使用 Azure Data Factory 從 Office 365 複製資料
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 3422176ed89b7f575c11cc40e5be8420da0018b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415803"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Office 365 載入資料

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

此文章說明如何使用 Data Factory「將資料從 Office 365 載入至 Azure Blob 儲存體」__。 您可以依照類似的步驟，將資料複製到 Azure Data Lake Gen1 或 Gen2。 如需有關從 Office 365 複製資料的一般資訊，請參閱 [Office 365 連接器文章](connector-office-365.md)。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側功能表上，選取 [**建立資源** > **分析** > ]**Data Factory**： 
   
   ![在 [新增] 窗格中選取資料處理站](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站]**** 頁面中，為下圖所示的欄位提供值：
      
   ![新增資料處理站頁面](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **名稱**：輸入 Azure 資料處理站的全域唯一名稱。 如果您收到「資料處理站名稱*LoadFromOffice365Demo*無法使用」錯誤，請為資料處理站輸入不同的名稱。 例如，您可以使用_**yourname**_**LoadFromOffice365Demo**。 請嘗試再次建立資料處理站。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
    * **訂用帳戶**：選取用來在其中建立資料處理站的 Azure 訂用帳戶。 
    * **資源群組**：從下拉式清單中選取現有資源群組，或選取 [新建]**** 選項，然後輸入資源群組的名稱。 若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。  
    * **版本**：選取 [V2]****。
    * **位置**：選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區可位於其他位置和區域。 這些資料存放區包含 Azure Data Lake Store、Azure 儲存體、Azure SQL Database 等等。

3. 選取 [建立]  。
4. 建立完成後，請移至資料處理站。 您會看到如下圖所示的 [Data Factory]**** 首頁：
   
   ![Data Factory 首頁](./media/load-office-365-data/data-factory-home-page.png)

5. 選取 [編寫與監視]**** 圖格，以在另一個索引標籤中啟動資料整合應用程式。

## <a name="create-a-pipeline"></a>建立管線

1. 在 [讓我們開始吧] 頁面上，選取 [**建立管線**]。
 
    ![建立管線](./media/load-office-365-data/create-pipeline-entry.png)

2. 在管線的 [**一般]** 索引標籤中，輸入 "CopyPipeline" 作為管線的**名稱**。

3. 在 [活動] 工具箱中 > [移動和轉換] 類別 > 將 [複製]**** 活動從工具箱中拖放到管線設計工具介面。 指定 "CopyFromOffice365ToBlob" 作為活動名稱。

### <a name="configure-source"></a>設定來源

1. 移至管線 > [來源]**** 索引標籤，按一下 [+ 新增]**** 以建立來源資料集。 

2. 在 [新增資料集] 視窗中，選取 [ **Office 365**]，然後選取 [**繼續**]。
 
3. 您現在已在 [複製活動設定] 索引標籤中。請按一下 Office 365 資料集旁的 [**編輯**] 按鈕，繼續進行資料設定。

    ![設定 Office 365 資料集的一般資訊](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. 您會看到已為 Office 365 資料集開啟一個新的索引標籤。 在屬性視窗底部的 [**一般]** 索引標籤中，輸入 "SourceOffice365Dataset" 作為 [名稱]。
 
5. 移至屬性視窗的 [**連接]** 索引標籤。 在 [已連結的服務] 文字方塊旁，按一下 [+ 新增]****。

6. 在 [新增連結服務] 視窗中，輸入 "Office365LinkedService" 作為名稱，輸入服務主體識別碼和服務主體金鑰，然後測試連接並選取 [**建立**] 以部署連結服務。

    ![新增 Office 365 已連結的服務](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. 在連結服務建立後，您會回到資料集設定。 在 [**資料表**] 旁，選擇向下箭號以展開可用的 Office 365 資料集清單，然後選擇 [BasicDataSet_v0]。Message_v0] 下拉式清單中：

    ![設定 Office 365 資料集資料表](./media/load-office-365-data/edit-dataset.png)

8. 現在回到 [**管線** > **來源]** 索引標籤，繼續設定 Office 365 資料解壓縮的其他屬性。  使用者範圍和使用者範圍篩選器是選擇性的述詞，您可以定義這些述詞來限制要從 Office 365 解壓縮的資料。 如需如何設定這些設定，請參閱[Office 365 資料集屬性](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties)一節。

9. 您必須選擇其中一個日期篩選準則，並提供 [開始時間] 和 [結束時間] 值。

10. 按一下 [匯**入架構**] 索引標籤，匯入訊息資料集的架構。

    ![設定 Office 365 資料集結構描述](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>設定接收

1. 移至管線 > [接收]**** 索引標籤，然後選取 [+ 新增]**** 以建立接收資料集。
 
2. 在 [新增資料集] 視窗中，請注意，從 Office 365 複製時，只會選取支援的目的地。 選取 [ **Azure Blob 儲存體**]，選取 [二進位格式]，然後選取 [**繼續**]。  在此教學課程中，您會將 Office 365 資料複製到「Azure Blob 儲存體」中。

3. 按一下 [Azure Blob 儲存體] 資料集旁的 [**編輯**] 按鈕，以繼續進行資料設定。

4. 在 [屬性] 視窗的 [一般]**** 索引標籤上，於 [名稱] 中輸入 "OutputBlobDataset"。

5. 移至屬性視窗的 [**連接]** 索引標籤。 在 [連結服務] 文字方塊旁，選取 [+ 新增]****。

6. 在 [新增連結服務] 視窗中，輸入 "AzureStorageLinkedService" 作為名稱，從驗證方法的下拉式清單中選取 [服務主體]，填入服務端點、租使用者、服務主體識別碼和服務主體金鑰，然後選取 [儲存] 以部署連結服務。  如需了解如何設定「Azure Blob 儲存體」的服務主體驗證，請參閱[這裡](connector-azure-blob-storage.md#service-principal-authentication)。

    ![新增 Blob 已連結的服務](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>驗證管線

若要驗證管線，請從工具列中選取 [驗證]  。

您也可以按一下右上方的 [程式碼]，以查看與管線相關聯的 JSON 程式碼。

## <a name="publish-the-pipeline"></a>發佈管線

在頂端工具列中，選取 [全部發行]****。 此動作會將您已建立的實體 (資料集和管線) 發佈至 Data Factory。

![發佈變更](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>手動觸發管線

選取工具列上的 [新增觸發程序]****，然後選取 [立即觸發]****。 在 [管線執行] 頁面上，選取 [完成]****。 

## <a name="monitor-the-pipeline"></a>監視管線

移至左側的 [監視]  索引標籤。 您會看到手動觸發程序所觸發的管線執行。 您可以使用 [**動作**] 欄中的連結來查看活動詳細資料，以及重新執行管線。

![監視管線](./media/load-office-365-data/pipeline-status.png) 

若要檢視與此管線執行相關聯的活動執行，請選取 [動作] 資料行中的 [檢視活動執行]**** 連結。 此範例中只有一個活動，因此您在清單中只會看到一個項目。 如需關於複製作業的詳細資料，請選取 [動作] 資料行中的 [詳細資料]**** 連結 (眼鏡圖示)。

![監視活動](./media/load-office-365-data/activity-status.png) 

如果這是您第一次要求此內容的資料（其中一個資料表正在存取的組合、哪個目的地帳戶是要載入的資料，以及哪些使用者識別正在進行資料存取要求），您將會看到複製活動狀態為 [**進行中**]，而且只有當您按一下 [動作] 底下的 [詳細資料] 連結時，您才會看到狀態為**RequesetingConsent**。  資料存取核准者群組的成員必須先在 Privileged Access Management 中核准該要求，資料擷取才能繼續。

_要求同意時的狀態：_
![活動執行詳細資料-要求同意](./media/load-office-365-data/activity-details-request-consent.png) 

_擷取資料時的狀態：_

![活動執行詳細資料 - 擷取資料](./media/load-office-365-data/activity-details-extract-data.png) 

一旦提供同意，資料提取將會繼續，而在一段時間之後，管線執行會顯示為 [成功]。

![監視管線 - 成功](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

現在移至目的地 Azure Blob 儲存體，並確認已以二進位格式解壓縮 Office 365 資料。

## <a name="next-steps"></a>後續步驟

前往下列文章，以了解 Azure SQL 資料倉儲支援： 

> [!div class="nextstepaction"]
>[Office 365 連接器](connector-office-365.md)