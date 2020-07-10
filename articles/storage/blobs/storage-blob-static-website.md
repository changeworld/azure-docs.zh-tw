---
title: Azure 儲存體中的靜態網站代管
description: Azure 儲存體靜態網站代管，提供符合成本效益且可延展的解決方案來代管新式 Web 應用程式。
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/14/2020
ms.subservice: blobs
ms.openlocfilehash: ccad51d18a5e76f68633103af64e9ba6cc3f19c0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203388"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 儲存體中的靜態網站代管

您可以直接從名為 *$web* 的儲存體容器提供靜態內容 (HTML、CSS、JavaScript 和影像檔)。 在 Azure 儲存體中託管內容可讓您使用無伺服器架構，其中包括 [Azure Functions](/azure/azure-functions/functions-overview)，以及其他平台即服務 (PaaS) 服務。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> 如果您的網站相依於伺服器端程式碼，請改用 [Azure App Service](/azure/app-service/overview)。
請務必建立一般用途 v2 標準儲存體帳戶。 靜態網站無法在任何其他類型的儲存體帳戶中使用。

## <a name="setting-up-a-static-website"></a>設定靜態網站

靜態網站代管是您必須在儲存體帳戶上啟用的功能。

若要啟用靜態網站代管，請選取您的預設檔案名稱，然後選擇性地提供自訂 404 頁面的路徑。 如果名為 **$web** 的 Blob 儲存體容器尚未存在於帳戶中，系統會為您建立一個。 將您的網站檔案新增至此容器。

如需逐步指引，請參閱[在 Azure 儲存體中託管靜態網站](storage-blob-static-website-how-to.md)。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web** 容器中的檔案會區分大小寫，透過匿名存取要求提供，而且只能透過讀取作業才能提供。

## <a name="uploading-content"></a>上傳內容

您可以使用這些工具的其中一項，將內容上傳至 **$web** 容器：

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell 模組](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 擴充功能](/azure/developer/javascript/tutorial-vscode-static-website-node-01) \(英文\)

## <a name="viewing-content"></a>檢視內容

使用者可以使用網站的公用 URL，從瀏覽器檢視網站內容。 您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來尋找 URL。 請參閱[尋找網站 URL](storage-blob-static-website-how-to.md#portal-find-url)。

如果伺服器傳回 404 錯誤，而且您在啟用網站時未指定錯誤文件，則會將預設的 404 頁面傳回給使用者。

> [!NOTE]
> 靜態網站不支援 [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。

### <a name="regional-codes"></a>區域代碼

您的網站 URL 包含區域代碼。 例如，URL `https://contosoblobaccount.z22.web.core.windows.net/` 包含區域代碼 `z22`。

雖然該代碼必須留在 URL 中，但僅供內部使用，而且您不需要以任何其他方式使用該代碼。

當您啟用靜態網站代管時指定的索引文件，會在使用者開啟網站但未指定特定檔案時顯示 (例如：`https://contosoblobaccount.z22.web.core.windows.net`)。

### <a name="secondary-endpoints"></a>次要端點

如果您已設定[次要地區的備援](../common/storage-redundancy.md#redundancy-in-a-secondary-region)，您也可以使用次要端點存取網站內容。 由於資料會以非同步方式複寫到次要地區，因此次要端點上可用的檔案不一定會與主要端點上可用的檔案同步。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>設定 Web 容器的公用存取層級的影響

您可以修改 **$web** 容器的公用存取層級，但這不會影響主要的靜態網站端點，因為這些檔案是透過匿名存取要求來提供。 這表示可公用 (唯讀) 存取所有檔案。

以下螢幕擷取畫面顯示 Azure 入口網站上中的公用存取層級設定：

![螢幕擷取畫面顯示如何在入口網站中設定公用存取層級](./media/anonymous-read-access-configure/configure-public-access-container.png)

雖然主要靜態網站端點不受影響，但對公用存取層級的變更會影響主要 Blob 服務端點。

例如，如果您將 **$web** 容器的公用存取層級從**私人 (無匿名存取)** 變更為 **Blob (僅限 Blob 的匿名讀取存取)** ，則主要靜態網站端點的公用存取層級`https://contosoblobaccount.z22.web.core.windows.net/index.html`不會變更。

不過，主要 Blob 服務端點的公用存取`https://contosoblobaccount.blob.core.windows.net/$web/index.html`會從私人變更至公用。 現在，使用者可以使用這兩個端點的其中一個來開啟該檔案。

在儲存體帳戶上停用公用存取並不會影響裝載在該儲存體帳戶中的靜態網站。 如需詳細資訊，請參閱[設定容器和 blob 的匿名公用讀取權限](anonymous-read-access-configure.md)。

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>將自訂網域對應至靜態網站 URL

您可以透過自訂網域，讓您的靜態網站可供使用。

為您的自訂網域啟用 HTTP 存取比較容易，因為 Azure 儲存體原本就支援該存取。 若要啟用 HTTPS，您必須使用 Azure CDN，因為 Azure 儲存體原本就不支援使用自訂網域的 HTTPS。 如需逐步指引，請參閱[將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)。

如果儲存體帳戶設為[需要透過 HTTPS 的安全傳輸](../common/storage-require-secure-transfer.md)，則使用者必須使用 HTTPS 端點。

> [!TIP]
> 請考慮在 Azure 上託管您的網域。 如需詳細資訊，請參閱[在 Azure DNS 中託管您的網域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="adding-http-headers"></a>新增 HTTP 標頭

沒有任何方法可以將標頭設為靜態網站功能的一部分。 不過，您可以使用 Azure CDN 以新增標頭和附加 (或覆寫) 標頭值。 請參閱 [Azure CDN 的標準規則引擎參考](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference)。

如需使用標頭以控制快取的詳細資訊，請參閱[使用快取規則控制 Azure CDN 快取行為](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)。

## <a name="pricing"></a>定價

您可以免費啟用靜態網站代管。 您只需要支付您的網站所使用的 Blob 儲存體和作業成本。 如需 Azure Blob 儲存體價格的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>計量

您可以在靜態網站頁面上啟用計量。 啟用計量之後，計量儀表板會報告與 **$web** 容器中檔案有關的流量統計資料。

若要在您的靜態網站頁面上啟用計量，請參閱[在靜態網站頁面上啟用計量](storage-blob-static-website-how-to.md#metrics)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 儲存體中託管靜態網站](storage-blob-static-website-how-to.md)
* [將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [建置您的第一個無伺服器 Web 應用程式](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教學課程：在 Azure DNS 上託管您的網域](../../dns/dns-delegate-domain-azure-dns.md)
