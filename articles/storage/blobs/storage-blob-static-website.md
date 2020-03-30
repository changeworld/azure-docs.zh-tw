---
title: Azure 儲存體中的靜態網站代管
description: Azure 儲存體靜態網站代管，提供符合成本效益且可延展的解決方案來代管新式 Web 應用程式。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370486"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 儲存體中的靜態網站代管

可以直接從名為 *$web*的存儲容器提供靜態內容（HTML、CSS、JavaScript 和影像檔）。 在 Azure 存儲中託管內容使您能夠使用包含[Azure 函數](/azure/azure-functions/functions-overview)和其他平臺即服務 （PaaS） 服務的無伺服器體系結構。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> 如果網站依賴于伺服器端代碼，請改用[Azure 應用服務](/azure/app-service/overview)。

## <a name="setting-up-a-static-website"></a>設置靜態網站

靜態網站託管是您必須在存儲帳戶上啟用的功能。

要啟用靜態網站託管，請選擇預設檔的名稱，然後選擇提供自訂 404 頁的路徑。 如果帳戶中不存在名為 **$web**的 blob 存儲容器，則為您創建一個 blob 存儲容器。 將網站的檔添加到此容器。

有關分步指導，請參閱[在 Azure 存儲中託管靜態網站](storage-blob-static-website-how-to.md)。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web**容器中的檔區分大小寫，通過匿名存取請求提供，只能通過讀取操作提供。

## <a name="uploading-content"></a>上傳內容

您可以使用以下任一工具將內容上載到 **$web**容器：

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 模組](storage-blob-static-website-how-to.md#powershell)
> * [阿茲比貝](../common/storage-use-azcopy-v10.md)
> * [Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [視覺化工作室代碼擴展](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>查看內容

使用者可以使用網站的公共 URL 從瀏覽器查看網站內容。 您可以使用 Azure 門戶、Azure CLI 或 PowerShell 查找 URL。 請使用下表作為指南。

|工具| 指引 |
|----|----|
|**Azure 門戶** | [使用 Azure 門戶查找網站 URL](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [使用 Azure CLI 查找網站 URL](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 模組** | [使用 PowerShell 查找網站網址](storage-blob-static-website-how-to.md#powershell-find-url) |

網站的 URL 包含區功能變數代碼。 例如，URL`https://contosoblobaccount.z22.web.core.windows.net/`包含區功能變數代碼`z22`。

雖然該代碼必須保留在 URL 中，但它僅供內部使用，您不必以任何其他方式使用該代碼。

啟用靜態網站託管時指定的索引文檔，在使用者打開網站時顯示，並且不指定特定檔（例如： `https://contosoblobaccount.z22.web.core.windows.net`  

如果伺服器返回 404 錯誤，並且您在啟用網站時未指定錯誤文檔，則預設 404 頁將返回給使用者。

> [!NOTE]
> 靜態網站不支援[CORS。](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>設置 Web 容器的公共存取層級的影響

您可以修改 **$web**容器的公共存取層級，但這不會影響主靜態網站終結點，因為這些檔是通過匿名存取請求提供的。 這意味著對所有檔的公共（唯讀）訪問。

以下螢幕截圖顯示了 Azure 門戶中的公共存取層級設置：

![演示如何在門戶中設置公共存取層級的螢幕截圖](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

雖然主靜態網站終結點不受影響，但公共存取層級的更改確實會影響主 Blob 服務終結點。

例如，如果將 **$web**容器的公共存取層級從**專用（無匿名存取）** 更改為**Blob（僅限 Blob 的匿名讀取存取），** 則對主靜態網站終結點`https://contosoblobaccount.z22.web.core.windows.net/index.html`的公共存取層級不會更改。

但是，對主 Blob 服務終結點`https://contosoblobaccount.blob.core.windows.net/$web/index.html`的公共訪問確實會從私有更改為公共。 現在，使用者可以使用這兩個終結點之一打開該檔。

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>將自訂域映射到靜態網站 URL

您可以通過自訂域使靜態網站可用。 

對於自訂域啟用 HTTP 訪問更容易，因為 Azure 存儲本機支援它。 要啟用 HTTPS，必須使用 Azure CDN，因為 Azure 存儲尚未在本機上支援具有自訂域的 HTTPS。 請參閱[將自訂域映射到 Azure Blob 存儲終結點](storage-custom-domain-name.md)，以便獲得分步指導。

如果存儲帳戶配置為需要通過 HTTPS[安全傳輸](../common/storage-require-secure-transfer.md)，則使用者必須使用 HTTPS 終結點。 

> [!TIP]
> 請考慮在 Azure 上託管域。 有關詳細資訊，請參閱在[Azure DNS 中託管域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="adding-http-headers"></a>添加 HTTP 標頭

無法將標頭配置為靜態網站功能的一部分。 但是，可以使用 Azure CDN 添加標頭和追加（或覆蓋）標頭值。 請參閱[Azure CDN 的標準規則引擎引用](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference)。

如果要使用標頭來控制緩存，請參閱[使用緩存規則控制 Azure CDN 緩存行為](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)。

## <a name="pricing"></a>定價

您可以免費啟用靜態網站託管。 您僅對網站使用的 Blob 存儲和運營成本收費。 如需 Azure Blob 儲存體價格的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>計量

您可以在靜態網站頁面上啟用指標。 啟用指標後 **，$web**容器中檔的流量統計資訊將報告在指標儀表板中。

要在靜態網站頁面上啟用指標，請參閱[在靜態網站頁面上啟用指標](storage-blob-static-website-how-to.md#metrics)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 存儲中託管靜態網站](storage-blob-static-website-how-to.md)
* [將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [建置您的第一個無伺服器 Web 應用程式](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教學課程：在 Azure DNS 中裝載您的網域](../../dns/dns-delegate-domain-azure-dns.md)
