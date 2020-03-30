---
title: 在 Azure 存儲中託管靜態網站
description: 瞭解如何直接從 Azure 存儲 GPv2 帳戶中的容器提供靜態內容（HTML、CSS、JavaScript 和影像檔）。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247005"
---
# <a name="host-a-static-website-in-azure-storage"></a>在 Azure 存儲中託管靜態網站

可以直接從 Azure 存儲 GPv2 帳戶中的容器提供靜態內容（HTML、CSS、JavaScript 和影像檔）。 要瞭解更多資訊，請參閱[Azure 存儲中的靜態網站託管](storage-blob-static-website.md)。

本文介紹如何使用 Azure 門戶、Azure CLI 或 PowerShell 啟用靜態網站託管。

## <a name="enable-static-website-hosting"></a>啟用靜態網站託管

靜態網站託管是您必須在存儲帳戶上啟用的功能。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com/)以開始進行操作。

2. 找出您的儲存體帳戶，然後顯示帳戶概觀。

3. 選取 [靜態網站]**** 以顯示靜態網站的設定頁面。

4. 選取 [已啟用]**** 以啟用儲存體帳戶的靜態網站代管功能。

5. 在 **"索引文檔名稱**"欄位中，指定預設索引頁（例如 *：index.html*）。 

   當使用者瀏覽至您靜態網站的根目錄時，就會顯示此預設索引頁面。  

6. 在 **"錯誤文檔路徑**"欄位中，指定預設錯誤頁（例如 *：404.html*）。 

   當使用者嘗試瀏覽至您靜態網站中所沒有的網頁時，就會顯示此預設錯誤頁面。

7. 按一下 [儲存]****。 Azure 入口網站現在即會顯示您的靜態網站端點。 

    ![啟用儲存體帳戶的靜態網站代管功能](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

可以使用[Azure 命令列介面 （CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)啟用靜態網站託管。

1. 首先，打開[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或者如果您在本地[安裝了](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI，請打開命令主控台應用程式，如 Windows PowerShell。

2. 如果您的身份與多個訂閱相關聯，則將活動訂閱設置為將承載靜態網站的存儲帳戶的訂閱。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   將`<subscription-id>`預留位置值替換為訂閱的 ID。

3. 啟用靜態網站託管。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

   * 將`<error-document-name>`預留位置替換為瀏覽器請求網站上不存在的頁面時向使用者顯示的錯誤文檔的名稱。

   * 將`<index-document-name>`預留位置替換為索引文檔的名稱。 本文檔通常是"index.html"。

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

<a id="powershell" />

您可以使用 Azure PowerShell 模組啟用靜態網站託管。

1. 打開 Windows PowerShell 命令視窗。

2. 驗證您是否具有 Azure PowerShell 模組 Az 版本 0.7 或更高版本。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

3. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

   ```powershell
   Connect-AzAccount
   ```

4. 如果您的身份與多個訂閱相關聯，則將活動訂閱設置為將承載靜態網站的存儲帳戶的訂閱。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   將`<subscription-id>`預留位置值替換為訂閱的 ID。

5. 獲取定義要使用的存儲帳戶的存儲帳戶上下文。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 將`<resource-group-name>`預留位置值替換為資源組的名稱。

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

6. 啟用靜態網站託管。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 將`<error-document-name>`預留位置替換為瀏覽器請求網站上不存在的頁面時向使用者顯示的錯誤文檔的名稱。

   * 將`<index-document-name>`預留位置替換為索引文檔的名稱。 本文檔通常是"index.html"。

---

## <a name="upload-files"></a>上傳檔案 

### <a name="portal"></a>[入口網站](#tab/azure-portal)

這些說明演示如何使用 Azure 門戶中顯示的存儲資源管理器版本上載檔。 但是，您還可以使用在 Azure 門戶之外運行的[存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)版本。 您可以使用 **$web**AzCopy、PowerShell、CLI 或任何可以將檔上載到帳戶$web容器的自訂應用程式。 [AzCopy](../common/storage-use-azcopy-v10.md) 有關使用 Visual Studio 代碼上傳檔的分步教程，請參閱[教程：在 Blob 存儲上託管靜態網站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)。

1. 選擇**存儲資源管理器（預覽）。**

2. 展開**BLOB 容器**節點，然後選擇 **$web**容器。

3. 選擇**上傳**按鈕以上載檔。

   ![上傳檔案](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. 如果希望瀏覽器顯示檔的內容，請確保該檔的內容類型設置為`text/html`。 

   ![檢查內容類型](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > 存儲資源管理器會自動將此屬性設置到`text/html`通常認可的擴展（如`.html`） 但是，在某些情況下，您必須自己設置。 如果不將此屬性設置為`text/html`，瀏覽器將提示使用者下載該檔，而不是呈現內容。 要設置此屬性，請按右鍵該檔，**然後按一下屬性**。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

將物件從原始目錄上載到 *$web*容器。

> [!NOTE]
> 如果使用 Azure 雲外殼，請確保在引用`\``$web`容器時添加逸出字元（例如： `\$web`。 如果使用 Azure CLI 的本地安裝，則不必使用逸出字元。

此示例假定您正在運行來自 Azure 雲外殼會話的命令。

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

* 將`<source-path>`預留位置替換為要上載的檔位置的路徑。

> [!NOTE]
> 如果使用 Azure CLI 的位置安裝，則可以使用本地電腦上的任意位置的路徑（例如： `C:\myFolder`。
>
> 如果使用 Azure 雲外殼，則必須引用雲外殼可見的檔共用。 此位置可以是雲共用本身的檔共用，也可以是您從雲外殼裝載的現有檔共用。 要瞭解如何執行此操作，請參閱 Azure[雲殼中的"持久化檔](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)"。

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

將物件從原始目錄上載到 *$web*容器。

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* 將`<path-to-file>`預留位置值替換為要上載的檔的完全限定路徑（例如： `C:\temp\index.html`。

* 將`<blob-name>`預留位置值替換為要為生成的 blob 指定的名稱（例如： `index.html`。

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>使用 Azure 門戶查找網站 URL

您可以使用網站的公共 URL 從瀏覽器查看網站的頁面。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

<a id="portal-find-url" />

在存儲帳戶帳戶帳戶概覽頁面旁邊的窗格中，選擇**靜態網站**。 網站的 URL 將顯示在 **"主終結點"** 欄位中。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

使用以下命令查找靜態網站的公共 URL：

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

* 將`<resource-group-name>`預留位置值替換為資源組的名稱。

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

<a id="powershell-find-url" />

使用以下命令查找靜態網站的公共 URL：

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* 將`<resource-group-name>`預留位置值替換為資源組的名稱。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>在靜態網站頁面上啟用指標

啟用指標後 **，$web**容器中檔的流量統計資訊將報告在指標儀表板中。

1. 按一下存儲帳戶功能表的 **"監視器"** 部分下的 **"指標**"。

   > [!div class="mx-imgBorder"]
   > ![指標連結](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > 藉由連結到不同的計量 API 來產生計量資料。 入口網站只會顯示指定時間範圍內所使用的 API 成員，以便只專注於傳回資料的成員。 為了確保您能夠選擇必要的 API 成員，第一步是擴展時間範圍。

2. 按一下時間範圍按鈕，選擇時間範圍，然後按一下"**應用**"。

   ![Azure 儲存體靜態網站計量的時間範圍](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 從*命名空間*下拉清單中選擇**Blob。**

   ![Azure 儲存體靜態網站計量的命名空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 然後選取 [輸出]**** 計量。

   ![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. 從 [彙總]** 選取器中選取 [總和]****。

   ![Azure 儲存體靜態網站計量的彙總](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 按一下"**添加篩選器"** 按鈕，並從*屬性*選擇器中選擇**API 名稱**。

   ![Azure 儲存體靜態網站計量的 API 名稱](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 選中 *"值*選擇器 **"中的 GetWeb內容**旁邊的核取方塊以填充指標報告。

   ![Azure 儲存體靜態網站計量的 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > 僅當該 API 成員在給定的時間範圍內使用時，才會顯示**GetWeb內容**核取方塊。 入口網站只會顯示指定時間範圍內所使用的 API 成員，以便只專注於傳回資料的成員。 如果在此清單中找不到特定的 API 成員，則展開時間範圍。

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用靜態網站配置自訂域。 請參閱[將自訂域映射到 Azure Blob 存儲終結點](storage-custom-domain-name.md)。

