---
title: 使用 Azure 儲存體總管管理 Azure Cosmos DB 資源
description: 了解如何使用 Azure 儲存體總管連接到 Azure Cosmos DB 並管理其資源。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1948ae186662c7f60f4d49c19a4d48b424a38f7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047474"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>使用 Azure 儲存體總管處理資料

在 Azure 儲存體總管中使用 Azure Cosmos DB 可讓使用者管理 Azure Cosmos DB 實體、操縱資料、更新預存程序及觸發程序，以及其他 Azure 實體 (例如儲存體 Blob 及佇列)。 現在您可以使用同一個工具在同一處管理您不同的 Azure 實體。 目前，Azure 儲存體總管支援對 SQL、MongoDB、Graph 和資料表 API 設定 Cosmos 帳戶。


## <a name="prerequisites"></a>必要條件

Cosmos 帳戶，需具有 SQL API 或適用於 MongoDB 的 Azure Cosmos DB API。 若您還沒有帳戶，您可以根據[Azure Cosmos DB：使用 .NET 及 Azure 入口網站建置 SQL API Web 應用程式](create-sql-api-dotnet.md)中的說明，在 Azure 入口網站中建立帳戶。

## <a name="installation"></a>安裝

在這裡安裝最新的 Azure 儲存體總管位元：[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，我們現在支援 Windows、Linux 及 MAC 版本。

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

1. 安裝 **Azure 儲存體總管**之後，請選取左側的 **外掛程式** 圖示，如下圖所示：

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="選取要連接的外掛程式圖示":::

2. 選取 [新增 Azure 帳戶]****，然後選取 [登入]****。

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="連接到所需的 Azure 訂用帳戶":::

2. 在 [ **Azure 登入** ] 對話方塊中，選取 [登 **入**]，然後輸入您的 Azure 認證。

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="登入您的 Azure 訂用帳戶":::

3. 從清單 **中選取您**的訂用帳戶，然後選取 [套用]。

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="從清單中選擇要篩選的訂用帳戶識別碼":::

    [總管] 窗格會更新，並顯示選取之訂閱中的帳戶。

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="從可用的清單中選取 Azure Cosmos DB 帳戶":::

    您已成功將您的 **Cosmos DB 帳戶**連線到您的 Azure 訂用帳戶。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>使用連接字串來連線到 Azure Cosmos DB

另外一種連線到 Azure Cosmos DB 的方式為使用連接字串。 使用下列步驟來使用連接字串進行連線。

1. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)****，以滑鼠右鍵按一下 [Cosmos DB 帳戶]****，選擇 [連線到 Cosmos DB...]****

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="使用連接字串來連線到 Azure Cosmos DB":::

2. 目前僅支援 SQL 和資料表 API。 選擇 [API]、[貼上 **連接字串**]、[輸入 **帳戶標籤**]、選取 **[下一步** ] 以檢查摘要， **然後選取 [連線] Azure Cosmos DB** 帳戶連線。 如需有關如何取得主要連接字串的詳細資訊，請參閱 [取得連接字串](manage-with-powershell.md#list-keys)。

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="輸入您的連接字串":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>使用本機模擬器連線到 Azure Cosmos DB

若要使用模擬器連線到 Azure Cosmos DB，請使用下列步驟 (目前僅支援 SQL 帳戶)。

1. 安裝模擬器並啟動。 如需了解如何安裝模擬器，請參閱 [Cosmos DB 模擬器](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)****，以滑鼠右鍵按一下 [Cosmos DB 帳戶]****，選擇 [連線到 Cosmos DB 模擬器...]****

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="從模擬器連接到 Azure Cosmos DB":::

3. 目前僅支援 SQL API。 貼上 **連接字串**、輸入 **帳戶標籤**、選取 **[下一步** ] 以檢查摘要， **然後選取 [連線] Azure Cosmos DB** 帳戶連接。 如需有關如何取得主要連接字串的詳細資訊，請參閱 [取得連接字串](manage-with-powershell.md#list-keys)。

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="從模擬器對話方塊連接到 Cosmos DB":::


## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 資源管理

您可以透過執行下列作業來管理 Azure Cosmos DB 帳戶：
* 在 Azure 入口網站中開啟帳戶
* 將資源新增至快速存取清單
* 搜尋和重新整理資源
* 建立和刪除資料庫
* 建立和刪除集合
* 建立、編輯、刪除和篩選文件
* 管理預存程序、觸發程序和使用者定義函式

### <a name="quick-access-tasks"></a>快速存取工作

以滑鼠右鍵按一下 [總管] 窗格中的訂用帳戶，您可以執行許多快速動作工作：

* 以滑鼠右鍵按一下 Azure Cosmos DB 帳戶或資料庫，您可以選擇 [在入口網站中開啟]**** 並使用瀏覽器在 Azure 入口網站上管理資源。

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="在入口網站中開啟":::

* 您也可以將 Azure Cosmos DB 帳戶、資料庫及集合新增至 [快速存取]****。
* [Search from Here] \(從這裡搜尋)**** 可在選取的路徑下使用關鍵字搜尋。

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="從這裡搜尋":::

### <a name="database-and-collection-management"></a>資料庫與集合管理

#### <a name="create-a-database"></a>建立資料庫

- 以滑鼠右鍵按一下 Azure Cosmos DB 帳戶，選擇 [建立資料庫]****，輸入資料庫名稱，然後按 ** ENTER 鍵**以完成。

  :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="在您的 Azure Cosmos 帳戶中建立資料庫":::

#### <a name="delete-a-database"></a>刪除資料庫

- 以滑鼠右鍵按一下資料庫，選取 [ **刪除資料庫**]，然後在快顯視窗中選取 **[是** ]。 資料庫節點隨即會刪除，而 Azure Cosmos DB 帳戶會自動重新整理。

  :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="刪除第一個資料庫":::

  :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="刪除第二個資料庫":::

#### <a name="create-a-collection"></a>建立集合

1. 以滑鼠右鍵按一下您的資料庫，選擇 [ **建立集合**]，然後提供下列資訊，例如 **集合識別碼**、 **儲存容量**等等。按一下 **[確定** ] 以完成。

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="在資料庫中建立第一個集合":::

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="在資料庫中建立第二個集合":::

2. 選取 [ **無限制** ] 以指定資料分割索引鍵，然後選取 **[確定** ] 以完成。

    若在建立集合時使用了分割區索引鍵，則一旦建立過程完成後，該分割區索引鍵的值便不能在集合上進行變更。

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="設定分割區索引鍵":::

#### <a name="delete-a-collection"></a>刪除集合

- 以滑鼠右鍵按一下集合，選取 [ **刪除集合**]，然後在快顯視窗中選取 **[是** ]。

    集合節點隨即刪除，且資料庫會自動重新整理。

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="刪除其中一個集合":::

### <a name="document-management"></a>文件管理

#### <a name="create-and-modify-documents"></a>建立及修改文件

- 若要建立新檔，請在左側視窗中開啟 [ **檔** ]，選取 [ **新增檔**]，在右窗格中編輯內容，然後選取 [ **儲存**]。 您也可以更新現有的檔，然後選取 [ **儲存**]。 按一下 [捨棄]**** 以捨棄變更。

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="建立新文件":::

#### <a name="delete-a-document"></a>刪除文件

- 按一下 [刪除]**** 按鈕來刪除選取的文件。

#### <a name="query-for-documents"></a>查詢文件

- 輸入 [SQL 查詢](how-to-sql-query.md) 來編輯檔篩選器， **然後選取**[套用]。

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="查詢特定檔":::

### <a name="graph-management"></a>圖表管理

#### <a name="create-and-modify-vertex"></a>建立及修改頂點

1. 若要建立新的頂點，請從左側視窗開啟 [ **圖形** ]、選取 [ **新增頂點**]、編輯內容，然後選取 **[確定]**。
2. 若要修改現有頂點，請在右窗格中選取畫筆圖示。

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="修改圖形的頂點":::

#### <a name="delete-a-graph"></a>建立圖表

- 若要刪除頂點，請選取頂點名稱旁邊的 [回收站] 圖示。

#### <a name="filter-for-graph"></a>篩選圖表

- 輸入 [gremlin 查詢](gremlin-support.md) ，然後選取 [套用 **篩選**]，以編輯圖形篩選。

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="執行圖形查詢":::

### <a name="table-management"></a>資料表管理

#### <a name="create-and-modify-table"></a>建立及修改資料表

1. 若要建立新的資料表，請從左側視窗開啟 **實體** ， **選取 [加入]**，在 [ **新增實體** ] 對話方塊中編輯內容，按一下 [新增 **屬性**]，然後選取 [ **插入**]。
2. 若要修改資料表，請選取 [ **編輯**]、[修改內容]，然後選取 [ **更新**]。

   :::image type="content" source="./media/storage-explorer/table.png" alt-text="建立和修改資料表":::

#### <a name="import-and-export-table"></a>匯入和匯出資料表

1. 若要匯入，請選取 [匯 **入** ] 按鈕，然後選擇現有的資料表。
2. 若要匯出，請選取 [ **匯出** ] 按鈕，然後選擇目的地。

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="匯入或匯出資料表":::

#### <a name="delete-entities"></a>刪除實體

- 選取實體，然後選取 [ **刪除**] 按鈕。

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="刪除資料表":::

#### <a name="query-table"></a>查詢資料表

- 按一下 [ **查詢** 按鈕]、[輸入查詢準則]，然後選取 [ **執行查詢** ] 按鈕。 按一下 [關閉查詢]**** 按鈕即可關閉 [查詢] 窗格。

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="查詢資料表中的資料":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理預存程序、觸發程序和 UDF

* 若要建立預存程式，請在左樹狀結構中，以滑鼠右鍵按一下 [ **預存**程式]，選擇 [ **建立預存**程式]，在左側輸入名稱，在右視窗中輸入預存程式腳本，然後選取 [ **建立**]。
* 您也可以按兩下來編輯現有的預存程式、進行更新，然後按一下 [ **更新** ] 以儲存，或選取 [ **捨棄** ] 取消變更。

  :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="建立和管理預存程式":::

* **觸發程序**及 **UDF** 的作業與**預存程序**雷同。

## <a name="troubleshooting"></a>疑難排解

[Azure 儲存體總管中的 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) 是獨立應用程式，可讓您從 Windows、macOS 或 Linux 連線到裝載在 Azure 和主權雲端上的 Azure Cosmos DB 帳戶。 也可讓您管理 Azure Cosmos DB 實體、操縱資料、更新預存程序及觸發程序，以及其他 Azure 實體 (例如儲存體 Blob 及佇列)。

這些是儲存體總管中有關 Azure Cosmos DB 常見問題的解決方案。

### <a name="sign-in-issues"></a>登入問題

在繼續之前，請先嘗試重新啟動您的應用程式，查看是否能修正問題。

#### <a name="self-signed-certificate-in-certificate-chain"></a>信任鏈結中的自我簽署憑證

有幾個原因可能會導致此錯誤，最常見的兩個原因是：

+ 您位於透明的 *proxy*後方，這表示有人 (例如您的 IT 部門) 正在攔截 HTTPS 流量、將其解密，然後使用自我簽署憑證將它加密。

+ 您正在執行軟體，例如防毒軟體，會將自我簽署的 TLS/SSL 憑證插入您收到的 HTTPS 訊息中。

當儲存體總管遇到這些「自我簽署憑證」的其中一個時，它可能就無法知曉收到的 HTTPS 訊息是否已遭竄改。 如果您有一份自我簽署憑證，則可以告知儲存體總管信任該憑證。 如果您不確定插入憑證的是誰，可以嘗試執行下列步驟來自行尋找：

1. 安裝 OpenSSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (任一輕裝版即可)
     - Mac 和 Linux：應該包含在作業系統中
2. 執行 OpenSSL
    - Windows：移至安裝目錄並找到 **/bin/**，然後按兩下 **openssl.exe**。
    - Mac 和 Linux：從終端機執行 **openssl**
3. 執行 `s_client -showcerts -connect microsoft.com:443`
4. 尋找自我簽署憑證。 如果不確定哪些是自我簽署的憑證，請尋找主旨 ("s:") 和簽發者 ("i:") 相同的所有位置。
5.  一旦發現任何自我簽署的憑證，請針對每個憑證，將從 **-----BEGIN CERTIFICATE-----** 到 **-----END CERTIFICATE-----** (含) 的所有內容，複製並貼到新的 .cer 檔案。
6.  開啟儲存體總管然後移至 [**編輯**  >  **SSL 憑證**匯  >  **入憑證**]。 使用檔案選擇器來尋找、選取及開啟您所建立的 .cer 檔案。

如果使用上述步驟找不到任何自我簽署的憑證，請傳送意見反應給我們，以取得更多協助。

#### <a name="unable-to-retrieve-subscriptions"></a>無法擷取訂用帳戶

如果您成功登入之後，卻無法擷取訂用帳戶：

- 登入[Azure 入口網站](https://portal.azure.com/)，確認您的帳戶可存取訂閱。
- 確定已使用正確的環境登入 ([Azure](https://portal.azure.com/)、[Azure 中國](https://portal.azure.cn/)、[Azure 德國](https://portal.microsoftazure.de/)、[Azure US Gov](https://portal.azure.us/) 或自訂環境/Azure Stack)
- 如果您是在 Proxy 背景，請確定已正確設定儲存體總管的 Proxy
- 嘗試移除再重新新增帳戶
- 嘗試從主目錄 (例如：C:\Users\ContosoUser) 刪除下列檔案，再重新新增帳戶：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 登入時若出現任何錯誤訊息，請查看開發人員工具主控台 (f12)

:::image type="content" source="./media/storage-explorer/console.png" alt-text="查看開發人員工具主控台是否有任何錯誤":::

#### <a name="unable-to-see-the-authentication-page"></a>看不到驗證頁面

如果您看不到驗證頁面：

- 載入登入頁面可能需要一些時間，視連線速度而定，請等待至少一分鐘再關閉 [驗證] 對話方塊
- 如果您是在 Proxy 背景，請確定已正確設定儲存體總管的 Proxy
- 按下 F12 鍵，開發人員主控台會隨即顯示。 查看開發人員主控台的回應，看看能否找到任何驗證無法運作的線索

#### <a name="cannot-remove-account"></a>無法移除帳戶

如果無法移除帳戶，或重新驗證連結不執行任何動作

- 嘗試從主目錄刪除下列檔案，再重新新增帳戶：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 如果您想要移除 SAS 連結的儲存體資源，請刪除：
  - Windows 是 %AppData%/StorageExplorer 資料夾
  - Mac 是 /Users/<您的名稱>/Library/Applicaiton SUpport/StorageExplorer
  - Linux 是 ~/.config/StorageExplorer
  - 如果您刪除這些檔案，就**必須重新輸入所有認證**


### <a name="httphttps-proxy-issue"></a>Http/Https Proxy 的問題

在 ASE 中設定 Http/Https Proxy 時，無法在左側樹狀目錄中列出 Azure Cosmos DB 節點。 目前您可以使用 Azure 入口網站中的 Azure Cosmos DB 資料總管解決此問題。

### <a name="development-node-under-local-and-attached-node-issue"></a>「本機與已連結」節點下的「開發」節點問題

在左側樹狀結構中，選取 [本機和已連結] 節點下的 [開發] 節點之後，就不會有回應。  這是預期中的行為。 將在下一個版本中提供 Azure DB Cosmos 本機模擬器支援。

:::image type="content" source="./media/storage-explorer/development.png" alt-text="開發節點":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>在「本機與已連結」節點中連結 Azure Cosmos DB 帳戶的錯誤

如果您在「本機與已連結」節點中連結 Azure Cosmos DB 帳戶後看到以下錯誤，請檢查您是否使用正確的連接字串。

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="在本機與已連結中連結 Azure Cosmos DB 的錯誤":::

### <a name="expand-azure-cosmos-db-node-error"></a>展開 Azure Cosmos DB 節點錯誤

當您嘗試展開左側的樹狀節點時，可能會看到下列錯誤。

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="展開 Azure Cosmos DB 節點錯誤":::

請嘗試下列建議：

- 檢查 Azure Cosmos DB 帳戶是否正在進行佈建，並在帳戶成功建立後再試一次。
- 如果帳戶是在「快速存取」節點或「本機與已連結」節點下，請檢查帳戶是否已遭到刪除。 如果是這樣，您需要以手動方式移除節點。

## <a name="next-steps"></a>後續步驟

* 請觀看下列影片以了解如何在 Azure 儲存體總管中使用 Azure Cosmos DB：[在 Azure 儲存體總管中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[開始使用儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)中深入了解儲存體總管並連線更多服務。
