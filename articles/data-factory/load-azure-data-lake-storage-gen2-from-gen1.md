---
title: 將資料從 Azure 資料儲存第 1 代複製到第 2 代
description: 使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 81f8577592f1d53627bc09a2f9ace8c060ad4660
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668859"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2

Azure 資料湖儲存 Gen2 是一組專用於大數據分析的功能,內置於[Azure Blob 儲存](../storage/blobs/storage-blobs-introduction.md)中。 您可以使用檔案系統和物件儲存範例與資料進行介面。

如果當前使用 Azure 資料儲存第 1 代,則可以使用 Azure 資料工廠將數據從資料儲存第 1 代複製到第 2 代,從而評估 Azure 數據儲存湖儲存 Gen2。

Azure Data Factory 是完全受控的雲端式資料整合服務。 您可以使用該服務使用來自大量本地和基於雲端資料儲存的數據填充湖中,並在構建分析解決方案時節省時間。 有關支援的連接器清單,請參閱[支持的數據儲存](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure Data Factory 提供可向外延展的受控資料移動解決方案。 由於數據工廠的橫向擴展體系結構,它可以以高輸送量引入數據。 有關詳細資訊,請參閱[複製活動性能](copy-activity-performance.md)。

本文介紹如何使用資料工廠複製數據工具將數據從 Azure 資料湖存儲 Gen1 複製到 Azure 資料湖存儲 Gen2 中。 您可以依照類似的步驟，從其他類型的資料存放區複製資料。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 內有資料的 Azure Data Lake Storage Gen1 帳戶。
* 啟用了數據存儲第 2 代的 Azure 儲存帳戶。 如果您沒有儲存帳號,[請建立帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側選單中,選擇 **「創建資源** > **資料 + 分析** > **資料工廠**」。。
   
   ![新表單的表格中的資料工廠選擇](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在 **'新增資料工廠'** 頁上,為下圖中顯示的欄位提供值: 
      
   ![新資料工廠頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名稱**：輸入 Azure 資料處理站的全域唯一名稱。 如果您收到「資料處理站名稱 \"LoadADLSDemo\" 無法使用」的錯誤，請為資料處理站輸入其他名稱。 例如，使用**您的名稱**__**ADFTutorialDataFactory**。 再次創建數據工廠。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
    * **訂用帳戶**：選取用來在其中建立資料處理站的 Azure 訂用帳戶。 
    * **資源組**:從下拉清單中選擇現有資源組。 您還可以選擇「**建立新**選項」 並輸入資源群組的名稱。 若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。 
    * **版本**：選取 [V2]****。
    * **位置**：選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區可位於其他位置和區域。 

3. 選取 [建立]  。
4. 創建完成後,轉到數據工廠。 您會看到如下圖所示的 [Data Factory]**** 首頁： 
   
   ![Data Factory 首頁](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. 選擇 **「作者&監視器」** 磁貼以在單獨的選項卡中啟動數據整合應用程式。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>將資料載入 Azure Data Lake Storage Gen2 中

1. 在「**開始」** 頁上,選擇 **「複製資料」** 磁貼以啟動複製數據工具。 

   ![複製資料工具磁貼](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 在 **「屬性」** 頁上,為**工作名稱**欄位指定**CopyFromADLSGen1ToGen2。** 選取 [下一步]  。

    ![屬性頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 在 **「源資料儲存」** 頁上,選擇 **「創建新連線**」 。

    ![來源資料存放區頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 從連接器庫中選擇**Azure 資料湖儲存第 1 代**,然後選擇「**繼續**」。
    
    ![來源資料存放區 Azure Data Lake Storage Gen1 頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. 在 **'Azure 資料儲存湖儲存第 1 代連線' 頁上**, 按照以下步驟操作:

   a. 選取 [Data Lake Storage Gen1] 作為帳戶名稱，並指定或驗證 [租用戶]****。
  
   b. 選擇 **「測試連接**」以驗證設定。 然後選取 [完成]****。
  
   c. 您將看到已建立新連接。 選取 [下一步]  。
   
   > [!IMPORTANT]
   > 在本演練中,使用 Azure 資源的託管標識對 Azure 數據湖存儲 Gen1 進行身份驗證。 在 Azure 資料的資料儲存 Gen1 中所指定的管理識別的權限,請按照[這些說明操作](connector-azure-data-lake-store.md#managed-identity)。
   
   ![指定 Azure Data Lake Storage Gen1 帳戶](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. 在**選擇輸入檔案或資料夾**「頁上,瀏覽到要複製的資料夾和檔案。 選擇資料夾或檔案,選擇 **。**

    ![選擇輸入檔案或資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. 通過選擇遞**歸檔和****二進位複製**選項來指定複製行為。 選取 [下一步]  。

    ![指定輸出資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. 在 **「目標資料儲存」** 頁上,選擇 **「創建新連接** > **Azure 資料湖存儲第 2** > 代**繼續**」。

    ![目的地資料存放區頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. 在 **「指定 Azure 資料儲存湖儲存 Gen2」連線**頁上,按照以下步驟操作:

   a. 從**儲存帳戶名稱**「下拉清單中選擇支援資料存儲湖存儲Gen2的帳戶。
   
   b. 選取 [完成]**** 以建立連線。 然後選擇 **「下一步**」。
   
   ![指定 Azure Data Lake Storage Gen2 帳戶](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. 在**選擇輸出檔或資料夾**「頁上,將**從adlsgen1的副本**輸入為輸出資料夾名稱,然後選擇 **」下一步**」。 數據工廠在複製期間創建相應的 Azure 資料儲存湖儲存 Gen2 檔案系統和子資料夾(如果它們不存在)。

    ![指定輸出資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. 在 [設定]**** 頁面上，選取 [下一步]**** 來使用預設設定。

12. 在**摘要的頁面**上,查看設置,然後選擇 **「下一步**」 。

    ![摘要頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. 在 **「部署」頁上**,選擇 **「監視器」** 以監視管道。

    ![部署頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 請注意，系統會自動選取左側的 [監視]**** 索引標籤。 [動作]**** 資料行中會有連結可供檢視活動執行詳細資料，以及重新執行管線。

    ![監視管線回合](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 若要檢視與此管線執行相關聯的活動執行，請選取 [動作]**** 資料行中的 [檢視活動執行]**** 連結。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 若要切換回 [管線執行] 檢視，請選取頂端的 [管線]**** 連結。 選取 [重新整理]**** 可重新整理清單。 

    ![監視活動回合](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 若要監視每個複製活動的執行詳細資料，請選取活動監控檢視中 [動作]**** 底下的 [詳細資料]**** 連結 (眼鏡圖示)。 您可以監視詳細資訊,例如從源複製到接收器的數據量、數據輸送量、具有相應持續時間的執行步驟以及使用的配置。

    ![監視活動執行詳細資料](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. 驗證數據是否複製到 Azure 資料湖儲存 Gen2 帳戶中。

## <a name="best-practices"></a>最佳作法

要評估從 Azure 資料儲存第 1 代升級到 Azure 資料儲存湖儲存第 2 代,請參閱[將大數據分析解決方案從 Azure 資料儲存庫第 1 代升級到 Azure 資料儲存湖儲存第 2 代](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)。 以下各節介紹使用數據工廠將數據升級到數據湖存儲第 1 代的最佳做法。

### <a name="data-partition-for-historical-data-copy"></a>歷史資料複本的資料分割區

- 如果數據存儲 Gen1 中的總資料大小小於 30 TB 且檔數小於 100 萬,則可以在單個複製活動中複製所有數據。
- 如果要複製的數據量較大,或者希望靈活地分批管理數據遷移並使每個遷移在特定時間範圍內完成,請對數據進行分區。 分區還降低了任何意外問題的風險。

使用概念驗證來驗證端到端解決方案並測試環境中的複製輸送量。 主要的概念驗證步驟: 

1. 創建一個資料工廠管道,具有單個複製活動,以將資料湖存儲 Gen1 複製到資料湖存儲 Gen2 中的多個 TB,以獲得複製性能基準。 從[數據整合單元 (DIUs)](copy-activity-performance-features.md#data-integration-units)開始,為 128。 
2. 根據步驟 1 中的副本輸送量,計算整個數據遷移所需的估計時間。 
3. ( 選擇性的 )建立控制表並定義檔案篩選器以對要遷移的檔進行分區。 對檔案進行分割區的方法是: 

    - 使用通配符篩選器按資料夾名稱或資料夾名稱進行分區。 建議採用此方法。
    - 按檔上次修改時間進行分區。

### <a name="network-bandwidth-and-storage-io"></a>網路頻寬和儲存 I/O 

您可以控制資料工廠複製作業的併發性,這些作業從資料存儲第 1 代讀取數據並將數據寫入資料存儲 Gen2。 通過這種方式,您可以管理該存儲 I/O 上的使用方式,以避免在遷移期間影響數據湖存儲 Gen1 上的正常業務工作。

### <a name="permissions"></a>權限 

在資料工廠中,[資料湖儲存 Gen1 連接器](connector-azure-data-lake-store.md)支援 Azure 資源身份驗證的服務主體和託管標識。 [Data Lake 儲存 Gen2 連接器](connector-azure-data-lake-storage.md)支援 Azure 資源身份驗證的帳戶密鑰、服務主體和託管標識。 要使 Data 工廠能夠導航和複製所需的所有檔案或存取控制列表 (ACL),請為您提供的帳戶授予足夠高的許可權,以便造訪、讀取或寫入所有檔,並在選擇時設定 ACL。 在遷移期間授予其超級使用者或所有者角色。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留資料湖儲存第 1 代中的 ACL

如果要在從資料存儲庫第 1 代升級到資料儲存庫 Gen2 時複製 ACL 和資料檔,請參閱[保留來自資料存儲湖存儲 Gen1 的 ACL。](connector-azure-data-lake-storage.md#preserve-acls) 

### <a name="incremental-copy"></a>增量複製 

您可以使用多種方法僅載入 Data Lake 儲存 Gen1 的新檔案或更新的檔案:

- 按分區資料夾或檔名載入新檔或更新檔。 例如 /2019/05/13/*。
- 按上次修改日期載入新檔或更新檔。
- 通過任何第三方工具或解決方案識別新的或更新的檔。 然後透過參數或表或檔將檔案或資料夾名稱傳遞到資料工廠管道。 

執行增量載入的正確頻率取決於 Azure 資料湖儲存 Gen1 中的檔案總數以及每次要載入的新檔或更新檔的數量。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [複製活動概述](copy-activity-overview.md)
> [Azure 資料儲存第一代連接器](connector-azure-data-lake-store.md)
> [Azure 資料湖儲存 Gen2 連接器](connector-azure-data-lake-storage.md)