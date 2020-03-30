---
title: 私有 SKU 和計畫 |Azure 應用商店
description: 如何使用私人 SKU 來管理供應項目可用性。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280383"
---
<a name="private-skus-and-plans"></a>私人 SKU 和方案
============

私人 SKU 讓您可以限制 SKU 僅供特定客戶使用。 當 SKU 標記為私人時，代表該 SKU 不在包含 [Azure Marketplace](https://azuremarketplace.microsoft.com) 及 [Azure 入口網站](https://portal.azure.com)在內的任何公開目錄上開放使用。 在 Azure 入口網站上，只有擁有 SKU 存取權的客戶可以看見它。 此外，系統會提示這些客戶，他們擁有私人供應項目的存取權。

>[!NOTE]
>私人 SKU 必須有新的唯一 SKU/方案識別碼，來避免與您的公用 SKU 發生任何衝突。

您可以使用私人 SKU 來處理以下案例：

1.  發佈您只想開放給特定客戶使用，而不想公開的軟體。
2.  以提供給特定客戶的自訂價格，發佈公開軟體的變化。
3.  發佈含有自訂描述及條款的公開軟體變化 (透過新的供應項目)。

如果您只想要變更價格，您可以重複使用同一個供應項目中其他 SKU 的磁碟。 使用私人 SKU，您就不必重新提交各個 SKU 的磁碟。

<a name="mark-a-sku-private"></a>將 SKU 標記為私人
---------------------

若要將 SKU 標記為私人，請切換詢問 SKU 是否為私人的選項：

![將 SKU 標記為私人](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

您可以重複使用其他 SKU 的磁碟，並修改定價或描述。 要重用磁片，請選擇 **"是**"作為回應"此 SKU 是否重用來自公共 SKU 的圖像"提示。

如果將 SKU 標記為私人，且供應項目有其他可重複使用磁碟的 SKU，您必須指出 SKU 會使用其他 SKU 中的磁碟。 您也必須指定私人 SKU 的目標對象。

>[!NOTE]
>發佈之後，公開 SKU 便不能再設為私人。

<a name="select-an-image"></a>選取映像
------------------

您可以為私人 SKU 提供新的磁碟，或是重複使用已在其他 SKU 中提供的相同磁碟，而只修改定價或描述。 要重用磁片，請選擇 **"是**"作為回應"此 SKU 是否從公共 SKU 重用映射"提示。

![指示映射重用](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

確認 SKU 重用映射後，為映射選擇源或*基礎*SKU：

![選取映像](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

當您發佈供應項目時，所選 SKU 的映像會在私人 SKU 識別碼下以自訂費率/條款開放使用。 私人 SKU 只有目標對象可以看見。

如需映像更新，您只需要更新基礎的 SKU 映像。 在幕後，私人 SKU 的映像也會自動更新。 同樣地，如果您從基礎 SKU 刪除了映像，這個映像也會從私人 SKU 中移除。

<a name="restricting-the-audience"></a>限制對象
------------------------

只有目標使用者能夠找到和部署私人供應項目。
我們目前支援使用訂用帳戶識別碼來設定目標使用者。

如果輸入這些訂用帳戶的方式是手動輸入表單，**最多可以輸入 10 個訂用帳戶**，若透過上傳 CSV 檔案，則允許**最多 20,000 個訂用帳戶**。

有限對象的手動輸入：

![手動限制對象](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

有限對象的 CSV 上傳：

![使用 CSV 來限制對象](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

範例 CSV 檔案內容：

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

當您從手動輸入切換至 CSV 上傳檢視，或是從 CSV 切換至手動輸入時，系統不會保留具有 SKU 存取權的訂用帳戶識別碼舊清單。 系統會顯示警告，而清單只會在儲存供應項目時覆寫。

<a name="managing-private-audiences"></a>管理私人受眾
-------------------------

**為了在不重新發佈整個產品/服務的情況下更新受眾，您可以對所需的受眾進行更改（使用 UI 或 API），然後啟動"同步專用受眾"操作。**

如果受眾為 10 個或更少的訂閱，則可以完全使用 CPP UI 對其進行管理。

如果訪問群體超過 10 個訂閱，則可以使用 CSV 檔對其進行管理，該檔可以上載到 CPP UI 或使用 API。

如果您使用的是 API，並且不想維護 CSV 檔，則可以根據以下說明直接使用 API 管理訪問群體。

> [!NOTE]
> 使用 Azure 訂閱 ID（計畫和 SKU）或租戶 ID（僅限計畫）向私人產品/服務添加訪問群體。

###  <a name="managing-subscriptions-with-the-api"></a>使用 API 管理訂閱

您可以使用 API 上傳 CSV 或直接管理受眾（不使用 CSV）。 通常，您只需檢索產品/服務，更新`restrictedAudience`物件，然後將這些更改提交回您的產品/服務，以便添加或刪除受眾成員。

以下是以程式設計方式更新訪問群體清單的方式：

1. [檢索您的產品/服務](cloud-partner-portal-api-retrieve-specific-offer.md)資料：

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. 使用此 JPath 查詢查找產品/服務的每個 SKU 中的受限訪問物件：

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. 更新優惠的受限受眾物件。

    **如果您最初從 CSV 檔上傳了私人優惠的訂閱清單：**

    受限*訪問物件*如下所示。
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    對於每個受限訪問物件：

    a. 下載 的內容`restrictedAudience.uploadedCsvUri`。 內容只是帶有標頭的 CSV 檔。 例如：

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. 根據需要在下載的 CSV 檔中添加或刪除訂閱。

    c. 將更新的 CSV 檔上載到位置（如[Azure Blob 存儲](../../storage/blobs/storage-blobs-overview.md)或[OneDrive），](https://onedrive.live.com)並創建指向檔的唯讀連結。 這將是你的新*SasUrl。*

    d. 使用新的`restrictedAudience.uploadedCsvUri` *SasUrl*更新金鑰。

    **如果您從雲合作夥伴門戶手動輸入了私人產品/服務的原始訂閱清單：**

    受限*訪問物件*如下所示：

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. 對於每個受限訪問物件，根據需要添加或刪除清單中的`restrictedAudience.manualEntries`條目。

4. 更新完私人產品/服務的每個 SKU 的所有*受限訪問物件*後，[請更新產品/服務](cloud-partner-portal-api-creating-offer.md)：

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    這樣，您更新的受眾清單現在生效。

<a name="previewing-private-offers"></a>預覽私人優惠
-------------------------

在預覽/預備步驟期間，只有供應項目層級的預覽訂用帳戶能夠存取 SKU。 在此測試階段，您可以預覽產品/服務，因為它會顯示在目標客戶中。

存取預備供應項目的供應項目層級預覽訂用帳戶：

![預覽訂用帳戶識別碼](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

在供應項目上線後，只有有限的目標訂用帳戶 (透過手動輸入或 CSV 輸入) 能夠檢視和部署私人 SKU。 為了進行驗證，建議您**一律將您自己的訂用帳戶包含在私人 SKU 的有限目標中**。

>[!NOTE]
>為了進行偵錯，Microsoft 支援和工程小組也會有這些私人供應項目的存取權。
