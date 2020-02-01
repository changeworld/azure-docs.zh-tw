---
title: 整合靜態網站與 Azure CDN-Azure 儲存體
description: 瞭解如何使用 Azure 內容傳遞網路（CDN）從 Azure 儲存體帳戶快取靜態網站內容。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: aaf61ccbb3577036c614aa6196d2af57124550fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908549"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>整合靜態網站與 Azure CDN

您可以啟用[azure 內容傳遞網路（CDN）](../../cdn/cdn-overview.md) ，從裝載于 Azure 儲存體帳戶的[靜態網站](storage-blob-static-website.md)快取內容。 您可以使用 Azure CDN 來設定靜態網站的自訂網域端點、布建自訂 SSL 憑證，以及設定自訂重寫規則。 設定 Azure CDN 會產生額外的費用，但可為從世界各地到您網站的連線提供一致的低延遲。 Azure CDN 也可利用您自己的憑證提供 SSL 加密。 

如需有關 Azure CDN 的定價資訊，請參閱 [Azure CDN 定價](https://azure.microsoft.com/pricing/details/cdn/)。

## <a name="enable-azure-cdn-for-your-static-website"></a>為您的靜態網站啟用 Azure CDN

您可以直接從儲存體帳戶為您的靜態網站啟用 Azure CDN。 如果想要指定 CDN 端點的進階組態設定 (例如[大型檔案下載最佳化](../../cdn/cdn-optimization-overview.md#large-file-download))，您可以改用 [Azure CDN 擴充功能](../../cdn/cdn-create-new-endpoint.md)建立 CDN 設定檔或端點。

1. 在 Azure 入口網站中找出您的儲存體帳戶，然後顯示帳戶概觀。

2. 選取 [Blob 服務] 功能表底下的 [Azure CDN] 以設定 Azure CDN。

    [Azure CDN] 頁面隨即顯示。

    ![建立 CDN 端點](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. 在 [CDN 設定檔] 區段中，指定新的或現有的 CDN 設定檔。 

4. 指定 CDN 端點的定價層。 若要深入瞭解定價，請參閱[內容傳遞網路定價](https://azure.microsoft.com/pricing/details/cdn/)。 如需每個層級可用功能的詳細資訊，請參閱[比較 AZURE CDN 產品功能](../../cdn/cdn-features.md)。

5. 在 [CDN 端點名稱] 欄位中，指定 CDN 端點的名稱。 CDN 端點在整個 Azure 中必須是唯一的。

6. 在 [原始主機名稱] 欄位中指定靜態網站端點。 

   若要尋找靜態網站端點，請瀏覽至儲存體帳戶的 [靜態網站] 設定。  複製主要端點，並將它貼入 CDN 設定。

   > [!IMPORTANT]
   > 請務必移除 URL 中的通訊協定識別碼（*例如*，HTTPS）和尾端斜線。 例如，如果靜態網站端點是 `https://mystorageaccount.z5.web.core.windows.net/`，則您會在 [**原始主機名稱**] 欄位中指定 `mystorageaccount.z5.web.core.windows.net`。

   下圖顯示端點設定範例：

   ![螢幕擷取畫面：顯示 CDN 端點設定範例](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. 選取 [**建立**]，然後等候它傳播。 端點建立之後，即會出現在端點清單中。

8. 為了確認 CDN 端點是否已正確設定，請按一下端點以瀏覽至其設定。 從儲存體帳戶的 CDN 概觀，找出端點主機名稱，並瀏覽到端點，如下圖所示。 CDN 端點的格式會類似 `https://staticwebsitesamples.azureedge.net`。

    ![螢幕擷取畫面：顯示 CDN 端點概觀](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. CDN 端點傳播完成後，瀏覽至 CDN 端點便會顯示您先前上傳至靜態網站的 index.html 檔案內容。

10. 若要檢閱 CDN 端點的原始設定，請瀏覽至 CDN 端點 [設定] 區段底下的 [原始來源]。 您會看到 [原始來源類型] 欄位設定為 [自訂原始來源] 且 [原始主機名稱] 欄位會顯示靜態網站端點。

    ![螢幕擷取畫面：顯示 CDN 端點的原始來源設定](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>從 Azure CDN 移除內容

如果不想繼續在 Azure CDN 中快取物件，您可以採取下列其中一個步驟：

* 將容器設為私人而非公用。 如需詳細資訊，請參閱 [管理對容器與 Blob 的匿名讀取權限](storage-manage-access-to-resources.md)。
* 使用 Azure 入口網站來停用或刪除 CDN 端點。
* 修改託管服務，使其不再回應物件的要求。

已在 Azure CDN 中快取的物件會保持快取狀態，直到物件的有效存留期已滿或端點已[清除](../../cdn/cdn-purge-endpoint.md)為止。 有效存留期已滿時，Azure CDN 會判斷 CDN 端點是否仍然有效，以及物件是否仍可匿名存取。 如果不是的話，將不再快取物件。

## <a name="next-steps"></a>後續步驟

選擇性將自訂網域新增至您的 Azure CDN 端點。 請參閱[教學課程：將自訂網域新增至您的 AZURE CDN 端點](../../cdn/cdn-map-content-to-custom-domain.md)。
