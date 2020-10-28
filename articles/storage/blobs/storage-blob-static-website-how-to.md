---
title: 在 Azure 儲存體中裝載靜態網站
description: 了解如何直接在 Azure 儲存體 GPv2 帳戶中的容器內提供靜態內容 (HTML、CSS、JavaScript 和影像檔。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 2359c762c9ad653ee2eed294709ad7bf6b2a31c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671102"
---
# <a name="host-a-static-website-in-azure-storage"></a>在 Azure 儲存體中裝載靜態網站

您可直接從 Azure 儲存體 GPv2 帳戶中的容器內提供靜態內容 (HTML、CSS、JavaScript 和影像檔)。 若要深入了解，請參閱 [Azure 儲存體中的靜態網站裝載](storage-blob-static-website.md)。

本文說明如何使用 Azure 入口網站、Azure CLI 或 PowerShell 來啟用靜態網站裝載。

> [!NOTE]
> 務必建立一般用途的 v2 標準儲存體帳戶。 靜態網站無法在任何其他類型的儲存體帳戶中使用。

## <a name="enable-static-website-hosting"></a>啟用靜態網站裝載

靜態裝載裝載是必須在儲存體帳戶上啟用的功能。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com/)以開始進行操作。

2. 找出您的儲存體帳戶，然後顯示帳戶概觀。

3. 選取 [靜態網站] 以顯示靜態網站的設定頁面。

4. 選取 [已啟用] 以啟用儲存體帳戶的靜態網站代管功能。

5. 在 [索引文件名稱] 欄位中，指定預設的索引頁 (例如： *index.html* )。 

   當使用者瀏覽至您靜態網站的根目錄時，就會顯示此預設索引頁面。  

6. 在 [錯誤文件路徑] 欄位中，指定預設的錯誤頁面 (例如： *404.html* )。 

   當使用者嘗試瀏覽至您靜態網站中所沒有的網頁時，就會顯示此預設錯誤頁面。

7. 按一下 [檔案] 。 Azure 入口網站現在即會顯示您的靜態網站端點。 

    ![啟用儲存體帳戶的靜態網站代管功能](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

您可使用 [Azure 命令列介面 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 來啟用靜態網站裝載。

1. 首先，開啟 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或若已在本機[安裝](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI，請開啟 Windows PowerShell 等命令主控台應用程式。

2. 若身分識別與多個訂用帳戶建立關聯，請將使用中訂用帳戶設為將裝載靜態網站儲存體帳戶的訂用帳戶。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   使用訂閱識別碼取代 `<subscription-id>` 預留位置值。

3. 啟用靜態網站裝載。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

   * 將 `<error-document-name>` 預留位置值取代為在瀏覽器要求的頁面於網站上不存在時，要向使用者顯示的錯誤文件名稱。

   * 將 `<index-document-name>` 預留位置值取代為索引文件的名稱。 此文件通常是 "index.html"。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

您可使用 Azure PowerShell 模組來啟用靜態網站裝載。

1. 開啟 Windows PowerShell 命令視窗。

2. 驗證已擁有 Azure PowerShell 模組 Az 版本 0.7 或更新版本。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

3. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

   ```powershell
   Connect-AzAccount
   ```

4. 若身分識別與多個訂用帳戶建立關聯，請將使用中訂用帳戶設為將裝載靜態網站儲存體帳戶的訂用帳戶。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   使用訂閱識別碼取代 `<subscription-id>` 預留位置值。

5. 取得定義想要使用儲存體帳戶的儲存體帳戶內容。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 將 `<resource-group-name>` 預留位置值取代為資源群組的名稱。

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

6. 啟用靜態網站裝載。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 將 `<error-document-name>` 預留位置值取代為在瀏覽器要求的頁面於網站上不存在時，要向使用者顯示的錯誤文件名稱。

   * 將 `<index-document-name>` 預留位置值取代為索引文件的名稱。 此文件通常是 "index.html"。

---

## <a name="upload-files"></a>上傳檔案 

### <a name="portal"></a>[入口網站](#tab/azure-portal)

這些指示會示範如何使用出現在 Azure 入口網站中的儲存體總管版本來上傳檔案。 但是，您也可以使用在 Azure 入口網站外部執行的[儲存體總管](https://azure.microsoft.com/features/storage-explorer/)版本。 您可使用 [AzCopy](../common/storage-use-azcopy-v10.md)、PowerShell、CLI 或任何可將檔案上傳到帳戶其 **$web** 容器的自訂應用程式。 如需使用 Visual Studio Code 上傳檔案的逐步教學課程，請參閱[教學課程：將靜態網站裝載於 Blob 儲存體上](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. 選取 [儲存體總管 (預覽)]。

2. 展開 [Blob 容器] 節點，然後選取 **$web** 容器。

3. 選擇 [上傳] 按鈕來上傳檔案。

   ![上傳檔案](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. 如果想要讓瀏覽器顯示檔案內容，請確認將該檔案的內容類型設為 `text/html`。 

   ![檢查內容類型](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > 儲存體總管會自動針對常見的副檔名 (例如 `.html`)，將此屬性設為 `text/html`。 但是，在某些案例中，將需要自行設定。 如果沒有將此屬性設為 `text/html`，則瀏覽器會提示使用者下載檔案，而非轉譯內容。 若要設定此屬性，請以滑鼠右鍵按一下檔案，然後按一下 [屬性]。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

將物件從來源目錄上傳至 *$web* 容器。

此範例假設您正在從 Azure Cloud Shell 工作階段執行命令。

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> 若瀏覽器提示使用者下載檔案，而非轉譯內容，則可將 `--content-type 'text/html; charset=utf-8'` 附加到命令。 

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

* 將 `<source-path>` 預留位置值取代為想要上傳檔案的位置路徑。

> [!NOTE]
> 若正在使用 Azure CLI 的本機安裝，則可指向本機電腦上任何位置的路徑 (例如：`C:\myFolder`)。
>
> 若正在使用 Azure Cloud Shell，則將需要參考 Cloud Shell 可見的檔案共用。 此位置可以是雲端共用本身的檔案共用，或在 Cloud Shell 中掛接的現有檔案共用。 若要了解如何進行此操作，請參閱[在 Azure Cloud Shell 中保存檔案](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage) (機器翻譯)。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

將物件從來源目錄上傳至 *$web* 容器。

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> 若瀏覽器提示使用者下載檔案，而非轉譯內容，則可將 `-Properties @{ ContentType = "text/html; charset=utf-8";}` 附加到命令。

* 將 `<path-to-file>` 預留位置值取代為想要上傳檔案的完整路徑 (例如：`C:\temp\index.html`)。

* 將 `<blob-name>` 預留位置值取代為想要提供所產生 blob 的名稱 (例如：`index.html`)。

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>尋找網站 URL

您可使用網站的公用 URL 來從瀏覽器檢視網站頁面。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

在儲存體帳戶帳戶概觀頁面旁邊出現的窗格中，選取 [靜態網站]。 您網站的 URL 會出現在 [主要端點] 欄位中。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用下列命令來尋找靜態網站的公用 URL：

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

* 將 `<resource-group-name>` 預留位置值取代為資源群組的名稱。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列命令來尋找靜態網站的公用 URL：

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* 將 `<resource-group-name>` 預留位置值取代為資源群組的名稱。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>在靜態網站頁面上啟用計量

啟用計量之後，計量儀表板會報告與 **$web** 容器中檔案有關的流量統計資料。

1. 按一下儲存體帳戶功能表 [監視] 區段下方的 [計量]。

   > [!div class="mx-imgBorder"]
   > ![計量連結](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > 藉由連結到不同的計量 API 來產生計量資料。 入口網站只會顯示指定時間範圍內所使用的 API 成員，以便只專注於傳回資料的成員。 為了確保可選取必要的 API 成員，第一個步驟是展開時間範圍。

2. 按一下時間範圍按鈕，選擇時間範圍，然後按一下 [套用]。

   ![Azure 儲存體靜態網站計量的時間範圍](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 從 [命名空間] 下拉式清單中選取 [Blob]。

   ![Azure 儲存體靜態網站計量的命名空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 然後選取 [輸出] 計量。

   ![顯示 Azure 儲存體靜態網站輸出度量的螢幕擷取畫面。](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. 從 [彙總] 選取器中選取 [總和]。

   ![Azure 儲存體靜態網站計量的彙總](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 按一下 [新增篩選] 按鈕，然後從 [屬性] 選取器中選擇 [API 名稱]。

   ![Azure 儲存體靜態網站計量的 API 名稱](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 選取 [值] 選取器中 [GetWebContent] 旁邊的方塊，以填入計量報告。

   ![Azure 儲存體靜態網站計量的 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > 只有在於指定時間範圍內使用該 API 成員時，才會顯示 [GetWebContent] 核取方塊。 入口網站只會顯示指定時間範圍內所使用的 API 成員，以便只專注於傳回資料的成員。 若無法在此清單上找到特定 API 成員，請展開時間範圍。

## <a name="next-steps"></a>後續步驟

* 了解如何設定靜態網站的自訂網域。 請參閱[將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)。

