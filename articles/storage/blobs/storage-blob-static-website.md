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
ms.openlocfilehash: a35239354d23f75361d5577d6b7efc8254943147
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906585"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 儲存體中的靜態網站代管

您可以直接從名為 *$web*的儲存體容器提供靜態內容（HTML、CSS、JavaScript 和影像檔案）。 在 Azure 儲存體中裝載內容可讓您使用包括[Azure Functions](/azure/azure-functions/functions-overview)和其他平臺即服務（PaaS）服務的無伺服器架構。

> [!NOTE]
> 如果您的網站相依于伺服器端程式碼，請改用[Azure App Service](/azure/app-service/overview) 。

## <a name="setting-up-a-static-website"></a>設定靜態網站

靜態網站裝載是您必須在儲存體帳戶上啟用的功能。

若要啟用靜態網站裝載，請選取預設檔案的名稱，然後選擇性地提供自訂404頁面的路徑。 如果名為 **$web**的 blob 儲存體容器尚未存在於帳戶中，系統就會為您建立一個。 將您網站的檔案新增至此容器。

如需逐步指引，請參閱[在 Azure 儲存體中裝載靜態網站](storage-blob-static-website-how-to.md)。

![Azure 儲存體靜態網站計量的計量](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$Web**容器中的檔案會區分大小寫，並透過匿名存取要求提供服務，而且只能透過讀取作業來使用。

## <a name="uploading-content"></a>上傳內容

您可以使用這些工具中的任何一項，將內容上傳至 **$web**容器：

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 模組](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 擴充功能](/azure/javascript/tutorial-vscode-static-website-node-01) \(英文\)

## <a name="viewing-content"></a>正在查看內容

使用者可以使用網站的公用 URL，從瀏覽器中查看網站內容。 您可以使用 Azure 入口網站、Azure CLI 或 PowerShell 來尋找 URL。 請使用下表作為指南。

|工具| 指導方針 |
|----|----|
|**Azure 入口網站** | [使用 Azure 入口網站來尋找網站 URL](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [使用 Azure CLI 來尋找網站 URL](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 模組** | [使用 PowerShell 尋找網站 URL](storage-blob-static-website-how-to.md#powershell-find-url) |

網站的 URL 包含地區代碼。 例如，URL `https://contosoblobaccount.z22.web.core.windows.net/` 包含區域程式碼 `z22`。

雖然該程式碼必須留在 URL 中，但僅供內部使用，而且您不需要以任何其他方式使用該程式碼。

當您啟用靜態網站裝載時所指定的索引檔，會在使用者開啟網站但未指定特定檔案（例如： `https://contosoblobaccount.z22.web.core.windows.net`）時出現。  

如果伺服器傳回404錯誤，而且您在啟用網站時未指定錯誤檔，則會將預設的404頁面傳回給使用者。

> [!NOTE]
> 靜態網站不支援[CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>設定 web 容器的公用存取層級的影響

您可以修改 **$web**容器的公用存取層級，但這不會影響主要靜態網站端點，因為這些檔案是透過匿名存取要求來提供。 這表示公用（唯讀）存取所有檔案。

下列螢幕擷取畫面顯示 Azure 入口網站中的 公用存取層級 設定：

![顯示如何在入口網站中設定公用存取層級的螢幕擷取畫面](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

雖然主要靜態網站端點不受影響，但對公用存取層級的變更會影響主要 blob 服務端點。

例如，如果您將 **$web**容器的公用存取層級從 [**私人（沒有匿名存取）** ] 變更為 [ **blob （僅限 blob 的匿名讀取權限）** ]，則主要靜態網站端點的公用存取層級 `https://contosoblobaccount.z22.web.core.windows.net/index.html` 不會變更。

不過，主要 blob 服務端點 `https://contosoblobaccount.blob.core.windows.net/$web/index.html` 的公用存取權會從 [私用] 變更為 [公用]。 現在使用者可以使用這兩個端點的其中一個來開啟該檔案。

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>將自訂網域對應至靜態網站 URL

您可以透過自訂網域，讓您的靜態網站可供使用。 

針對您的自訂網域啟用 HTTP 存取比較容易，因為 Azure 儲存體原本就支援它。 若要啟用 HTTPS，您必須使用 Azure CDN，因為 Azure 儲存體尚未以原生方式支援使用自訂網域的 HTTPS。 如需逐步指引，請參閱[將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)。

如果儲存體帳戶設定為需要透過 HTTPS 進行[安全傳輸](../common/storage-require-secure-transfer.md)，則使用者必須使用 HTTPs 端點。 

> [!TIP]
> 請考慮將您的網域裝載在 Azure 上。 如需詳細資訊，請參閱[在 Azure DNS 中託管您的網域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="pricing"></a>定價

您可以免費啟用靜態網站裝載。 您只需支付您的網站利用的 blob 儲存體和營運成本。 如需 Azure Blob 儲存體價格的詳細資訊，請參閱 [Azure Blob 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>計量

您可以在靜態網站頁面上啟用計量。 啟用計量之後，[計量] 儀表板中會報告 **$web**容器中檔案的流量統計資料。

若要在您的靜態網站頁面上啟用計量，請參閱[在靜態網站頁面上啟用計量](storage-blob-static-website-how-to.md#metrics)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 儲存體中裝載靜態網站](storage-blob-static-website-how-to.md)
* [將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [建置您的第一個無伺服器 Web 應用程式](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教學課程：在 Azure DNS 中裝載您的網域](../../dns/dns-delegate-domain-azure-dns.md)
