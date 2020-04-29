---
title: 私人 Sku 和方案 |Azure Marketplace
description: 如何使用私人 SKU 來管理供應項目可用性。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
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

您可以重複使用其他 SKU 的磁碟，並修改定價或描述。 若要重複使用磁片，請選取 [**是]** 作為回應「此 SKU 會重複使用公用 SKU 中的映射」提示。

如果將 SKU 標記為私人，且供應項目有其他可重複使用磁碟的 SKU，您必須指出 SKU 會使用其他 SKU 中的磁碟。 您也必須指定私人 SKU 的目標對象。

>[!NOTE]
>發佈之後，公開 SKU 便不能再設為私人。

<a name="select-an-image"></a>選取映像
------------------

您可以為私人 SKU 提供新的磁碟，或是重複使用已在其他 SKU 中提供的相同磁碟，而只修改定價或描述。 若要重複使用磁片，請選取 [**是]** 作為回應「此 SKU 會從公用 SKU 重複使用映射」提示。

![指出映射重複使用](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

確認 SKU 重複使用影像之後，請選取映射的來源或*基底*SKU：

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

<a name="managing-private-audiences"></a>管理私用物件
-------------------------

**若要在不重新發佈整個供應專案的情況下更新物件，您可以讓觀眾變更您想要的專案（使用 UI 或 API），然後起始「同步私人物件」動作。**

如果您的物件是10個或更少的訂用帳戶，您可以使用 CPP UI 來完全管理。

如果您的物件超過10個訂用帳戶，您可以使用 CSV 檔案進行管理，您可以將它上傳至 CPP UI 或使用 API。

如果您使用 API，而不想要維護 CSV 檔案，您可以依照下列指示，使用 API 直接管理物件。

> [!NOTE]
> 使用 Azure 訂用帳戶識別碼（方案和 Sku）或租使用者識別碼（僅限方案）將物件新增至您的私人供應專案。

###  <a name="managing-subscriptions-with-the-api"></a>使用 API 管理訂用帳戶

您可以使用 API 來上傳 CSV，或直接管理您的物件（不使用 CSV）。 一般而言，您只需要取得您的`restrictedAudience`供應專案、更新物件，然後將這些變更提交回您的供應專案，以便新增或移除觀眾成員。

以下說明如何以程式設計方式更新您的物件清單：

1. [取出您的供應](cloud-partner-portal-api-retrieve-specific-offer.md)專案資料：

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. 使用此 JPath 查詢，在供應專案的每個 SKU 中尋找受限制的觀眾物件：

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. 為您的供應專案更新受限制的物件物件。

    **如果您最初是從 CSV 檔案上傳私人供應專案的訂用帳戶清單：**

    您的*restrictedAudience*物件看起來會像這樣。
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    針對每個受限制的觀眾物件：

    a. 下載的內容`restrictedAudience.uploadedCsvUri`。 內容只是具有標頭的 CSV 檔案。 例如：

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. 視需要在下載的 CSV 檔案中新增或刪除訂閱。

    c. 將更新的 CSV 檔案上傳到位置（例如[Azure Blob 儲存體](../../storage/blobs/storage-blobs-overview.md)或[OneDrive](https://onedrive.live.com)），並建立檔案的唯讀連結。 這會是您的新*SasUrl*。

    d. 使用您`restrictedAudience.uploadedCsvUri`的新*SasUrl*來更新金鑰。

    **如果您從 Cloud Partner 入口網站手動輸入私人供應專案的原始訂用帳戶清單：**

    您的*restrictedAudience*物件看起來會像這樣：

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

    a. 針對每個受限制的觀眾物件，視需要在`restrictedAudience.manualEntries`清單中新增或刪除專案。

4. 完成更新私人供應專案的每個 SKU 的所有*restrictedAudience*物件時，請[更新供應](cloud-partner-portal-api-creating-offer.md)專案：

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    如此一來，您更新的物件清單現在就會生效。

<a name="previewing-private-offers"></a>預覽私人供應專案
-------------------------

在預覽/預備步驟期間，只有供應項目層級的預覽訂用帳戶能夠存取 SKU。 在此測試階段中，您可以預覽供應專案對目標客戶的表現。

存取預備供應項目的供應項目層級預覽訂用帳戶：

![預覽訂用帳戶識別碼](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

在供應項目上線後，只有有限的目標訂用帳戶 (透過手動輸入或 CSV 輸入) 能夠檢視和部署私人 SKU。 為了進行驗證，建議您**一律將您自己的訂用帳戶包含在私人 SKU 的有限目標中**。

>[!NOTE]
>為了進行偵錯，Microsoft 支援和工程小組也會有這些私人供應項目的存取權。
