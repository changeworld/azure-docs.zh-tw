---
title: 使用 Azure 儲存體總管管理 Azure Cosmos DB 資源
description: 了解如何使用 Azure 儲存體總管連接到 Azure Cosmos DB 並管理其資源。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 9260f2892bdcc6a694e1e54e29cb06bae90298eb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074466"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>使用 Azure 儲存體總管管理 Azure Cosmos DB 資源
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

您可以使用 Azure 儲存體 explorer 連接到 Azure Cosmos DB。 它可讓您連線到 Azure 上裝載的 Azure Cosmos DB 帳戶，以及從 Windows、macOS 或 Linux 主權雲端。

使用相同的工具，在同一個位置管理不同的 Azure 實體。 您可以管理 Azure Cosmos DB 實體、運算元據、更新預存程式和觸發程式，以及其他 Azure 實體（例如儲存體 blob 和佇列）。 Azure 儲存體總管支援針對 SQL、MongoDB、Graph 和資料表 Api 所設定的 Cosmos 帳戶。

> [!NOTE]
> Azure Cosmos DB 與儲存體總管的整合已被取代。 在此版本中，將不會移除任何現有的功能至少一年。 您應改為使用 [Azure 入口網站](https://portal.azure.com/)、 [azure 入口網站的桌面應用程式](https://portal.azure.com/App/Download) 或獨立的 [azure Cosmos explorer](data-explorer.md) 。 替代選項包含儲存體總管目前不支援的許多新功能。

## <a name="prerequisites"></a>Prerequisites

具有 SQL API 的 Cosmos 帳戶，或適用于 MongoDB 的 Azure Cosmos DB API。 如果您沒有帳戶，可以在 Azure 入口網站中建立一個帳戶。 如需詳細資訊，請參閱 [Azure Cosmos DB：使用 .Net 建立 SQL API web 應用程式和 Azure 入口網站](create-sql-api-dotnet.md) 。

## <a name="installation"></a>安裝

若要安裝最新的 Azure 儲存體總管位，請參閱 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。 我們支援 Windows、Linux 和 macOS 版本。

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

1. 安裝 **Azure 儲存體總管** 之後，請選取左窗格 **中的外掛程式** 圖示。

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 選取 [新增 Azure 帳戶]  ，然後選取 [登入]  。

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 在 [ **Azure 登入** ] 對話方塊中，選取 [登 **入** ]，然後輸入您的 Azure 認證。

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 從清單中選取您的訂用帳戶，然後選取 [套用]  。

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

    [Explorer] 窗格會更新並顯示所選訂用帳戶中的帳戶。

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

    您的 **Cosmos DB 帳戶** 已連線到您的 Azure 訂用帳戶。

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>使用連接字串連接到 Azure Cosmos DB

您可以使用連接字串連接到 Azure Cosmos DB。 此方法僅支援 SQL 和資料表 Api。 遵循下列步驟以連接字串：

1. 在左側樹狀目錄中尋找 [ **本機] 和** [已連接]，以滑鼠右鍵按一下 **Cosmos DB 帳戶** ]，然後選取 **[連接到 Cosmos DB]** 。

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

2. 在 [ **連接到 Cosmos DB** ] 視窗中：
   1. 從下拉式功能表中選取 API。
   1. 在 [ **連接字串** ] 方塊中貼上您的連接字串。 如需如何取出主要連接字串的詳細說明，請參閱 [取得連接字串](manage-with-powershell.md#list-keys)。
   1. 輸入 **帳戶標籤** ，然後選取 **[下一步]** 以查看摘要。
   1. 選取 **[連線] 以** 連接 Azure Cosmos DB 帳戶。

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>使用本機模擬器連接到 Azure Cosmos DB

使用下列步驟，透過模擬器連接到 Azure Cosmos DB。 此方法僅支援 SQL 帳戶。

1. 安裝 Cosmos DB 模擬器，然後開啟它。 如需如何安裝模擬器的詳細說明，請參閱 [Cosmos DB 模擬器](./local-emulator.md)。

1. 在左側樹狀目錄中尋找 [ **本機] 和** [已連接]，以滑鼠右鍵按一下 **Cosmos DB 帳戶** ]，然後選取 **[連接到 Cosmos DB 模擬器]** 。

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 在 [ **連接到 Cosmos DB** ] 視窗中：
   1. 在 [ **連接字串** ] 方塊中貼上您的連接字串。 如需有關如何取得主要連接字串的詳細資訊，請參閱 [取得連接字串](manage-with-powershell.md#list-keys)。
   1. 輸入 **帳戶標籤** ，然後選取 **[下一步]** 以查看摘要。
   1. 選取 **[連線] 以** 連接 Azure Cosmos DB 帳戶。

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 資源管理

使用下列作業來管理 Azure Cosmos DB 帳戶：

* 在 Azure 入口網站中開啟帳戶。
* 將資源新增至快速存取清單。
* 搜尋和重新整理資源。
* 建立和刪除資料庫。
* 建立和刪除集合。
* 建立、編輯、刪除和篩選檔。
* 管理預存程式、觸發程式和使用者定義函數。

### <a name="quick-access-tasks"></a>快速存取工作

您可以用滑鼠右鍵按一下 [Explorer] 窗格上的訂用帳戶來執行許多快速動作工作，例如：

* 在 Azure Cosmos DB 帳戶或資料庫上按一下滑鼠右鍵，然後選取 [ **在入口網站中開啟** ]，以在 Azure 入口網站的瀏覽器中管理資源。

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

* 以滑鼠右鍵按一下 Azure Cosmos DB 帳戶、資料庫或集合，然後選取 [ **新增至快速存取** ]，將其新增至 [快速存取] 功能表。

* 選取 [ **從這裡搜尋** ]，在選取的路徑下啟用關鍵字搜尋。

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

### <a name="database-and-collection-management"></a>資料庫與集合管理

#### <a name="create-a-database"></a>建立資料庫

1. 以滑鼠右鍵按一下 Azure Cosmos DB 帳戶，然後選取 [ **建立資料庫** ]。

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 輸入資料庫名稱，然後按 **enter** 鍵以完成。

#### <a name="delete-a-database"></a>刪除資料庫

1. 以滑鼠右鍵按一下資料庫，然後選取 [ **刪除資料庫** ]。 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 在快顯視窗中選取 **[是** ]。 資料庫節點隨即會刪除，而 Azure Cosmos DB 帳戶會自動重新整理。

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

#### <a name="create-a-collection"></a>建立集合

1. 以滑鼠右鍵按一下您的資料庫，然後選取 [ **建立集合** ]。

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 在 [建立集合] 視窗中，輸入所要求的資訊，例如 **集合識別碼** 和 **儲存容量** 等等。 選取 [確定] 來完成。

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

1. 選取 [ **無限制** ]，讓您可以指定分割區索引鍵，然後選取 **[確定** ] 以完成。

   > [!NOTE]
   > 如果您在建立集合時使用資料分割索引鍵，則在建立完成後，就無法變更集合上的資料分割索引鍵值。

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

#### <a name="delete-a-collection"></a>刪除集合

- 以滑鼠右鍵按一下集合，選取 [ **刪除集合** ]，然後在快顯視窗中選取 **[是** ]。

    集合節點隨即刪除，且資料庫會自動重新整理。

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

### <a name="document-management"></a>文件管理

#### <a name="create-and-modify-documents"></a>建立及修改文件

- 在左窗格中開啟 [ **檔** ]，選取 [ **新增檔** ]，在右窗格中編輯內容，然後選取 [ **儲存** ]。
- 您也可以更新現有的檔，然後選取 [ **儲存** ]。 若要捨棄變更，請選取 [ **捨棄** ]。

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

#### <a name="delete-a-document"></a>刪除文件

* 選取 [ **刪除** ] 按鈕來刪除選取的檔。

#### <a name="query-for-documents"></a>查詢文件

* 若要編輯檔篩選器，請輸入 [SQL 查詢](./sql-query-getting-started.md)， **然後選取** [套用]。

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

### <a name="graph-management"></a>圖表管理

#### <a name="create-and-modify-a-vertex"></a>建立和修改頂點

* 若要建立新的頂點，請從左窗格開啟 **圖表** ，選取 [ **新增頂點** ]，編輯內容，然後選取 **[確定]** 。
* 若要修改現有頂點，請在右窗格中選取 [畫筆] 圖示。

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

#### <a name="delete-a-graph"></a>建立圖表

* 若要刪除頂點，請選取頂點名稱旁邊的 [回收站] 圖示。

#### <a name="filter-for-graph"></a>篩選圖表

* 若要編輯圖形篩選，請輸入 [gremlin 查詢](gremlin-support.md)，然後選取 [套用 **篩選** ]。

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

### <a name="table-management"></a>資料表管理

#### <a name="create-and-modify-a-table"></a>建立和修改資料表

* 若要建立新的資料表：
   1. 在左窗格中，開啟 [ **實體** ]，然後選取 [ **新增** ]。
   1. 在 [ **新增實體** ] 對話方塊中，編輯內容。
   1. 選取 [ **加入屬性** ] 按鈕以加入屬性。
   1. 選取 [插入]  。

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

* 若要修改資料表，請選取 [ **編輯** ]，修改內容，然後選取 [ **更新** ]。

   

#### <a name="import-and-export-table"></a>匯入和匯出資料表

* 若要匯入，請選取 [匯 **入** ] 按鈕，然後選擇現有的資料表。
* 若要匯出，請選取 [ **匯出** ] 按鈕，然後選擇目的地。

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

#### <a name="delete-entities"></a>刪除實體

* 選取實體，然後選取 [ **刪除** ] 按鈕。

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

#### <a name="query-a-table"></a>查詢資料表

- 選取 [ **查詢** ] 按鈕、輸入查詢準則，然後選取 [ **執行查詢** ] 按鈕。 若要關閉 [查詢] 窗格，請選取 [ **關閉查詢** ] 按鈕。

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理預存程序、觸發程序和 UDF

* 若要建立預存程式：
  1. 在左側樹狀目錄中，以滑鼠右鍵按一下 [ **預存程式** ]，然後選取 [ **建立預存** 程式]。
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::
  
  1. 在左側輸入名稱，在右窗格中輸入預存程式腳本，然後選取 [ **建立** ]。
  
* 若要編輯現有的預存程式，請按兩下程式並進行更新，然後選取 [ **更新** ] 以儲存。 您也可以選取 [ **捨棄** ] 來取消變更。

* **觸發** 程式和 **UDF** 的作業類似于 **預存程式** 。

## <a name="troubleshooting"></a>疑難排解

以下是當您在儲存體總管中使用 Azure Cosmos DB 時，所發生之常見問題的解決方案。

### <a name="sign-in-issues"></a>登入問題

首先，重新開機您的應用程式，以查看是否能修正問題。 如果問題持續發生，請繼續進行疑難排解。

#### <a name="self-signed-certificate-in-certificate-chain"></a>信任鏈結中的自我簽署憑證

有幾個原因可能會導致您看到此錯誤，兩個最常見的原因是：

* 您是在 *透明的 proxy* 後方。 有人（例如您的 IT 部門）會攔截 HTTPS 流量、將其解密，然後使用自我簽署憑證將它加密。

* 您正在執行軟體，例如防毒軟體。 軟體會將自我簽署的 TLS/SSL 憑證插入您收到的 HTTPS 訊息中。

當儲存體總管找到自我簽署的憑證時，就不知道它收到的 HTTPS 訊息是否已遭篡改。 如果您有自我簽署憑證的複本，您可以告訴儲存體總管信任它。 如果您不確定插入憑證的人，可以遵循下列步驟來嘗試找出：

1. 安裝 OpenSSL：

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html)：任何燈光版本都沒問題。
     - macOS 和 Linux：應該包含在您的作業系統中。

1. 執行 OpenSSL：
    * Windows：移至安裝目錄，然後按一下 [ **/bin/** ]，然後按兩下 [ **openssl.exe** ]。
    * Mac 和 Linux：從終端機執行 **openssl** 。
1. 執行 `s_client -showcerts -connect microsoft.com:443`。
1. 尋找自我簽署憑證。 如果您不確定哪些是自我簽署的，請尋找主體 ( "s：" ) 和簽發者 ( "i：" ) 相同的任何位置。
1. 如果您找到任何自我簽署的憑證，請複製並貼上所有內容，包括 **-----[開始憑證]-----** **-----[結束** 憑證]-----新增至新的憑證。每一個的 CER 檔案。
1. 開啟儲存體總管，然後移至 [ **編輯**  >  **SSL 憑證** 匯  >  **入憑證** ]。 使用檔案選擇器來尋找、選取，然後開啟。您建立的 CER 檔案。

如果您找不到任何自我簽署的憑證，您可以傳送意見反應以取得更多協助。

#### <a name="unable-to-retrieve-subscriptions"></a>無法擷取訂用帳戶

如果您在登入後無法取得訂用帳戶，請嘗試下列建議：

* 確認您的帳戶具有訂用帳戶的存取權。 若要這樣做，請登入 [Azure 入口網站](https://portal.azure.com/)。
* 請確定您已登入正確的環境：
  * [Azure](https://portal.azure.com/)
  * [Azure China](https://portal.azure.cn/)
  * [Azure Germany](https://portal.microsoftazure.de/)
  * [Azure US Gov](https://portal.azure.us/)
  * 自訂環境/Azure Stack
* 如果您是在 proxy 後方，請確定已正確設定儲存體總管 proxy。
* 移除帳戶，然後再次新增它。
* 從主目錄中刪除下列檔案 (例如： C:\Users\ContosoUser) ，然後再次新增帳戶：
  * .adalcache
  * .devaccounts
  * .extaccounts
* 按 F12 鍵以開啟開發人員主控台。 當您登入時，請觀賞主控台中的任何錯誤訊息。

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

#### <a name="unable-to-see-the-authentication-page"></a>看不到驗證頁面

如果您看不到驗證頁面：

* 視您的連線速度而定，載入登入頁面可能需要一些時間。 請等待至少一分鐘再關閉 [驗證] 對話方塊。
* 如果您是在 proxy 後方，請確定已正確設定儲存體總管 proxy。
* 在 [開發人員工具主控台] (F12) 中，查看回應以查看是否可以找到驗證無法運作的線索。

#### <a name="cant-remove-an-account"></a>無法移除帳戶

如果您無法移除帳戶，或重新驗證連結不會執行任何動作：

* 從主目錄刪除下列檔案，然後再次新增帳戶：
  * .adalcache
  * .devaccounts
  * .extaccounts

* 如果您想要移除 SAS 連結的儲存體資源，請刪除：
  * Windows 是 %AppData%/StorageExplorer 資料夾
  * MacOS 的/Users/<your_name>/Library/application support 支援/>storageexplorer
  * Linux 是 ~/.config/StorageExplorer
  
  > [!NOTE]
  > 如果您刪除這些檔案， **則必須重新輸入所有認證** 。

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS proxy 問題

當您在 ASE 中設定 HTTP/HTTPS proxy 時，無法在左側樹狀目錄中列出 Azure Cosmos DB 節點。 您可以使用 Azure 入口網站中的 Azure Cosmos DB 資料 explorer 作為解決方式。

### <a name="development-node-under-local-and-attached-node-issue"></a>「本機與已連結」節點下的「開發」節點問題

在左側樹狀結構中，選取 [本機] 和 [ **附加** ] 節點下的 [ **開發** ] 節點之後，就不會有回應。 這是預期中的行為。

:::image type="content" source="./media/storage-explorer/development.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>在 **本機和附加** 節點錯誤中附加 Azure Cosmos DB 帳戶

如果您在 **本機和附加** 的節點中附加 Azure Cosmos DB 帳戶之後看到下列錯誤，請確定您使用的是正確的連接字串。

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

### <a name="expand-azure-cosmos-db-node-error"></a>展開 Azure Cosmos DB 節點錯誤

當您嘗試展開左側樹狀結構中的節點時，可能會看到下列錯誤。

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="顯示左窗格中外掛程式圖示的螢幕擷取畫面。":::

請嘗試下列建議：

* 檢查 Azure Cosmos DB 帳戶是否處於布建進度。 成功建立帳戶時，請再試一次。
* 如果帳戶是在 [ **快速存取** ] 或 [ **本機] 和 [附加** ] 節點底下，請檢查帳戶是否已刪除。 如果是的話，您需要手動移除節點。

## <a name="next-steps"></a>下一步

* 觀看這段影片以瞭解如何在 Azure 儲存體總管中使用 Azure Cosmos DB： [在 Azure 儲存體總管中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[開始使用儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)中深入了解儲存體總管並連線更多服務。