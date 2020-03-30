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
ms.openlocfilehash: cd2b530375beb3e8fa1f79e004f4f1ac7fd4d0bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443917"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Office 365 載入資料

此文章說明如何使用 Data Factory「將資料從 Office 365 載入至 Azure Blob 儲存體」__。 您可以依照類似的步驟，將資料複製到 Azure Data Lake Gen1 或 Gen2。 如需有關從 Office 365 複製資料的一般資訊，請參閱 [Office 365 連接器文章](connector-office-365.md)。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側功能表上，選擇 **"創建資源** > **分析** > **資料工廠**"： 
   
   ![在 [新增] 窗格中選取資料處理站](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站]**** 頁面中，為下圖所示的欄位提供值：
      
   ![新增資料處理站頁面](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **名稱**：輸入 Azure 資料處理站的全域唯一名稱。 如果收到錯誤"資料工廠名稱*LoadFromOffice365Demo*不可用"，請輸入資料工廠的其他名稱。 例如，您可以使用_**yourname**_**LoadFromOffice365Demo**。 請嘗試再次建立資料處理站。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
    * **訂用帳戶**：選取用來在其中建立資料處理站的 Azure 訂用帳戶。 
    * **資源群組**：從下拉式清單中選取現有資源群組，或選取 [新建]**** 選項，然後輸入資源群組的名稱。 若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。  
    * **版本**：選取 [V2]****。
    * **位置**：選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區可位於其他位置和區域。 這些資料存放區包含 Azure Data Lake Store、Azure 儲存體、Azure SQL Database 等等。

3. 選取 [建立]****。
4. 建立完成後，請移至資料處理站。 您會看到如下圖所示的 [Data Factory]**** 首頁：
   
   ![Data Factory 首頁](./media/load-office-365-data/data-factory-home-page.png)

5. 選取 [編寫與監視]**** 圖格，以在另一個索引標籤中啟動資料整合應用程式。

## <a name="create-a-pipeline"></a>建立管線

1. 在"讓我們開始"頁上，選擇 **"創建管道**"。
 
    ![建立管線](./media/load-office-365-data/create-pipeline-entry.png)

2. 在管道的 **"常規"選項卡**中，為管道**名稱**輸入"複製管道"。

3. 在 [活動] 工具箱中 > [移動和轉換] 類別 > 將 [複製]**** 活動從工具箱中拖放到管線設計工具介面。 指定 "CopyFromOffice365ToBlob" 作為活動名稱。

### <a name="configure-source"></a>設定來源

1. 移至管線 > [來源]**** 索引標籤，按一下 [+ 新增]**** 以建立來源資料集。 

2. 在"新建資料集"視窗中，選擇**Office 365，** 然後選擇 **"繼續**"。
 
3. 現在，您位於"複製活動配置"選項卡中。按一下 Office 365 資料集旁邊的 **"編輯"** 按鈕以繼續資料配置。

    ![設定 Office 365 資料集的一般資訊](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. 您會看到已為 Office 365 資料集開啟一個新的索引標籤。 在"屬性"視窗底部的 **"常規"選項卡**中，輸入名稱的"SourceOffice365資料集資料集"。
 
5. 轉到"屬性"視窗的 **"連接"選項卡**。 在 [已連結的服務] 文字方塊旁，按一下 [+ 新增]****。

6. 在"新建連結服務"視窗中，輸入"Office365LinkService"作為名稱，輸入服務主體 ID 和服務主體金鑰，然後測試連接並選擇 **"創建**"以部署連結服務。

    ![新增 Office 365 已連結的服務](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. 在連結服務建立後，您會回到資料集設定。 在**表**旁邊，選擇向下箭頭以展開可用 Office 365 資料集的清單，然後選擇"BasicDataSet_v0。下拉清單中的Message_v0"：

    ![設定 Office 365 資料集資料表](./media/load-office-365-data/edit-dataset.png)

8. 現在返回**管道** > **源選項卡**，繼續配置 Office 365 資料提取的其他屬性。  使用者範圍和使用者範圍篩選器是可選的謂詞，您可以定義這些謂詞來限制要從 Office 365 中提取的資料。 有關如何配置這些設置，請參閱[Office 365 資料集屬性](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties)部分。

9. 您需要選擇一個日期篩選器並提供開始時間和結束時間值。

10. 按一下"**導入架構"** 選項卡以導入消息資料集的架構。

    ![設定 Office 365 資料集結構描述](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>設定接收

1. 移至管線 > [接收]**** 索引標籤，然後選取 [+ 新增]**** 以建立接收資料集。
 
2. 在"新建資料集"視窗中，請注意，從 Office 365 複製時，僅選擇支援的目標。 選擇**Azure Blob 存儲**，選擇二進位格式，然後選擇"**繼續**"。  在此教學課程中，您會將 Office 365 資料複製到「Azure Blob 儲存體」中。

3. 按一下 Azure Blob 存儲資料集旁邊的 **"編輯"** 按鈕以繼續資料配置。

4. 在 [屬性] 視窗的 [一般]**** 索引標籤上，於 [名稱] 中輸入 "OutputBlobDataset"。

5. 轉到"屬性"視窗的 **"連接"選項卡**。 在 [連結服務] 文字方塊旁，選取 [+ 新增]****。

6. 在"新建連結服務"視窗中，輸入"AzureStorageLinkService"作為名稱，從身份驗證方法的下拉清單中選擇"服務主體"，填寫服務終結點、租戶、服務主體 ID 和服務主體鍵，然後選擇"保存到"部署連結的服務。  如需了解如何設定「Azure Blob 儲存體」的服務主體驗證，請參閱[這裡](connector-azure-blob-storage.md#service-principal-authentication)。

    ![新增 Blob 已連結的服務](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>驗證管線

若要驗證管線，請從工具列中選取 [驗證]****。

您也可以按一下右上方的 [程式碼]，以查看與管線相關聯的 JSON 程式碼。

## <a name="publish-the-pipeline"></a>發佈管線

在頂端工具列中，選取 [全部發行]****。 此動作會將您已建立的實體 (資料集和管線) 發佈至 Data Factory。

![發佈變更](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>手動觸發管線

選取工具列上的 [新增觸發程序]****，然後選取 [立即觸發]****。 在 [管線執行] 頁面上，選取 [完成]****。 

## <a name="monitor-the-pipeline"></a>監視管線

移至左側的 [監視]**** 索引標籤。 您會看到手動觸發程序所觸發的管線執行。 您可以使用 **"操作"** 列中的連結查看活動詳細資訊並重新運行管道。

![監視管線](./media/load-office-365-data/pipeline-status.png) 

若要檢視與此管線執行相關聯的活動執行，請選取 [動作] 資料行中的 [檢視活動執行]**** 連結。 此範例中只有一個活動，因此您在清單中只會看到一個項目。 如需關於複製作業的詳細資料，請選取 [動作] 資料行中的 [詳細資料]**** 連結 (眼鏡圖示)。

![監視活動](./media/load-office-365-data/activity-status.png) 

如果這是您第一次請求此上下文的資料（正在訪問的哪個資料表的組合，哪個目標帳戶是要載入到的資料，哪個使用者標識正在發出資料訪問請求），您將看到複製活動狀態為 **"正在進行**"，並且只有在按一下"操作"下的"詳細資訊"連結時，您才會看到狀態為 **"重新設置同意**"。  資料存取核准者群組的成員必須先在 Privileged Access Management 中核准該要求，資料擷取才能繼續。

_請求同意的狀態：_
![活動執行詳細資訊 - 請求同意](./media/load-office-365-data/activity-details-request-consent.png) 

_擷取資料時的狀態：_

![活動執行詳細資料 - 擷取資料](./media/load-office-365-data/activity-details-extract-data.png) 

一旦提供了同意，資料提取將繼續，並在一段時間後，管道運行將顯示為成功。

![監視管線 - 成功](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

現在轉到目標 Azure Blob 存儲，並驗證 Office 365 資料是否以二進位格式提取。

## <a name="next-steps"></a>後續步驟

前往下列文章，以了解 Azure SQL 資料倉儲支援： 

> [!div class="nextstepaction"]
>[Office 365 連接器](connector-office-365.md)