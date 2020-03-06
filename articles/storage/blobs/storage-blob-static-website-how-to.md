---
title: 在 Azure 儲存體中裝載靜態網站
description: 瞭解如何直接從 Azure 儲存體 GPv2 帳戶中的容器提供靜態內容（HTML、CSS、JavaScript 和影像檔案）。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: e312cc0dc6c58bb33a737e1fc28dd6eb3578b764
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330246"
---
# <a name="host-a-static-website-in-azure-storage"></a>在 Azure 儲存體中裝載靜態網站

您可以直接從 Azure 儲存體 GPv2 帳戶中的容器提供靜態內容（HTML、CSS、JavaScript 和影像檔案）。 若要深入瞭解，請參閱[Azure 儲存體中的靜態網站裝載](storage-blob-static-website.md)。

本文說明如何使用 Azure 入口網站、Azure CLI 或 PowerShell 來啟用靜態網站裝載。

## <a name="enable-static-website-hosting"></a>啟用靜態網站裝載

靜態網站裝載是您必須在儲存體帳戶上啟用的功能。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com/)以開始進行操作。

2. 找出您的儲存體帳戶，然後顯示帳戶概觀。

3. 選取 [靜態網站] 以顯示靜態網站的設定頁面。

4. 選取 [已啟用] 以啟用儲存體帳戶的靜態網站代管功能。

5. 在 [**索引檔案名稱**] 欄位中，指定預設的索引頁（例如： *Index .html*）。 

   當使用者瀏覽至您靜態網站的根目錄時，就會顯示此預設索引頁面。  

6. 在 [**錯誤檔路徑**] 欄位中，指定預設的錯誤網頁（例如： *404 .html*）。 

   當使用者嘗試瀏覽至您靜態網站中所沒有的網頁時，就會顯示此預設錯誤頁面。

7. Haga clic en **Guardar**. Azure 入口網站現在即會顯示您的靜態網站端點。 

    ![啟用儲存體帳戶的靜態網站代管功能](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

您可以使用[Azure 命令列介面（CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)來啟用靜態網站裝載。

1. 首先，開啟[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或者如果您已在本機[安裝](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI，請開啟命令主控台應用程式（例如 Windows PowerShell）。

2. 如果您的身分識別與多個訂用帳戶相關聯，請將您的使用中訂用帳戶設定為將裝載靜態網站之儲存體帳戶的訂用帳戶。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   以您訂用帳戶的識別碼取代 `<subscription-id>` 的預留位置值。

3. 啟用靜態網站裝載。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

   * 將 `<error-document-name>` 預留位置取代為當瀏覽器要求網站上不存在的頁面時，將對使用者顯示的錯誤檔案名稱。

   * 以索引檔的名稱取代 `<index-document-name>` 的預留位置。 這份檔通常是「index .html」。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

您可以使用 Azure PowerShell 模組來啟用靜態網站裝載。

1. 開啟 Windows PowerShell 命令視窗。

2. 確認您已 Azure PowerShell 模組 Az 0.7 版或更新版本。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

3. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

   ```powershell
   Connect-AzAccount
   ```

4. 如果您的身分識別與多個訂用帳戶相關聯，請將您的使用中訂用帳戶設定為將裝載靜態網站之儲存體帳戶的訂用帳戶。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   以您訂用帳戶的識別碼取代 `<subscription-id>` 的預留位置值。

5. 取得儲存體帳戶內容，以定義您想要使用的儲存體帳戶。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 以您的資源組名取代 `<resource-group-name>` 的預留位置值。

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

6. 啟用靜態網站裝載。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 將 `<error-document-name>` 預留位置取代為當瀏覽器要求網站上不存在的頁面時，將對使用者顯示的錯誤檔案名稱。

   * 以索引檔的名稱取代 `<index-document-name>` 的預留位置。 這份檔通常是「index .html」。

---

## <a name="upload-files"></a>上傳檔案 

### <a name="portal"></a>[入口網站](#tab/azure-portal)

這些指示說明如何使用 Azure 入口網站中出現的儲存體總管版本來上傳檔案。 不過，您也可以使用在 Azure 入口網站外部執行的[儲存體總管](https://azure.microsoft.com/features/storage-explorer/)版本。 您可以使用[AzCopy](../common/storage-use-azcopy-v10.md)、POWERSHELL、CLI，或任何可將檔案上傳至您帳戶之 **$web**容器的自訂應用程式。 如需使用 Visual Studio 的程式碼上傳檔案的逐步教學課程，請參閱[教學課程：在 Blob 儲存體上裝載靜態網站](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)。

1. 選取 **[儲存體總管（預覽）** ]。

2. 展開 [ **BLOB 容器**] 節點，然後選取 [ **$web** ] 容器。

3. 選擇 [**上傳**] 按鈕來上傳檔案。

   ![上傳檔案](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. 如果您想要讓瀏覽器顯示檔案的內容，請確定該檔案的內容類型已設定為 [`text/html`]。 

   ![檢查內容類型](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > 儲存體總管會自動將此屬性設定為一般可辨識延伸模組的 `text/html`，例如 `.html`。 不過，在某些情況下，您必須自行設定。 如果您未將此屬性設定為 [`text/html`]，瀏覽器會提示使用者下載檔案，而不是轉譯內容。 若要設定此屬性，請以滑鼠右鍵按一下該檔案，然後按一下 [**屬性**]。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

將物件從來源目錄上傳至 *$web* 容器。

> [!NOTE]
> 如果您使用 Azure Cloud Shell，請務必在參考 `$web` 容器時加入 `\` 的 escape 字元（例如： `\$web`）。 如果您是使用 Azure CLI 的本機安裝，則不需要使用 escape 字元。

這個範例假設您正在執行來自 Azure Cloud Shell 會話的命令。

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

* 以您想要上傳之檔案的位置路徑取代 `<source-path>` 預留位置。

> [!NOTE]
> 如果您要使用 Azure CLI 的位置安裝，則可以使用本機電腦上任何位置的路徑（例如： `C:\myFolder`。
>
> 如果您使用 Azure Cloud Shell，就必須參考 Cloud Shell 可以看見的檔案共用。 這個位置可能是雲端共用本身的檔案共用，或是您從 Cloud Shell 掛接的現有檔案共用。 若要瞭解如何執行這項操作，請參閱[在 Azure Cloud Shell 中保存](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)盤案。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

將物件從來源目錄上傳至 *$web* 容器。

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* 以您要上傳之檔案的完整路徑來取代 `<path-to-file>` 的預留位置值（例如： `C:\temp\index.html`）。

* 將 `<blob-name>` 的預留位置值取代為您想要為產生的 blob 提供的名稱（例如： `index.html`）。

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>使用 Azure 入口網站來尋找網站 URL

您可以使用網站的公用 URL，從瀏覽器查看網站的頁面。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

<a id="portal-find-url" />

在您儲存體帳戶的帳戶總覽頁面旁出現的窗格中，選取 [**靜態網站**]。 網站的 URL 會出現在 [**主要端點**] 欄位中。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

使用下列命令尋找靜態網站的公用 URL：

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

* 以您的資源組名取代 `<resource-group-name>` 的預留位置值。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

使用下列命令，藉以尋找靜態網站的公用 URL：

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* 以您的資源組名取代 `<resource-group-name>` 的預留位置值。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>在靜態網站頁面上啟用計量

啟用計量之後，[計量] 儀表板中會報告 **$web**容器中檔案的流量統計資料。

1. 按一下 [**設定**] > [**監視** > **計量**]。

   藉由連結到不同的計量 API 來產生計量資料。 入口網站只會顯示指定時間範圍內所使用的 API 成員，以便只專注於傳回資料的成員。 為了確保您能夠選取必要的 API 成員，第一個步驟是展開時間範圍。

2. 按一下 [時間範圍] 按鈕，並選取 [**過去24小時**]，然後按一下 [套用 **]。**

   ![Azure 儲存體靜態網站計量的時間範圍](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 從 [*命名空間*] 下拉式選選取 [ **Blob** ]。

   ![Azure 儲存體靜態網站計量的命名空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 然後選取 [輸出] 計量。

   ![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. 從 [彙總] 選取器中選取 [總和]。

   ![Azure 儲存體靜態網站計量的彙總](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 按一下 [**新增篩選**] 按鈕，然後從*屬性*選取器選擇 [ **API 名稱**]。

   ![Azure 儲存體靜態網站計量的 API 名稱](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 核取 [*值*] 選取器中 [ **GetWebContent** ] 旁的方塊，以填入計量報表。

   ![Azure 儲存體靜態網站計量的 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用您的靜態網站來設定自訂網域。 請參閱[將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)。

