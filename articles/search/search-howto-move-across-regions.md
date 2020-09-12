---
title: 如何跨區域移動您的服務資源
titleSuffix: Azure Cognitive Search
description: 本文將說明如何在 Azure 雲端中將 Azure 認知搜尋資源從某個區域移至另一個區域。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/10/2020
ms.openlocfilehash: 7383d9f95435775448f7322d9eb7707f676e9952
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007038"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>將您的 Azure 認知搜尋服務移至另一個 Azure 區域

有時候，客戶會詢問如何將搜尋服務移至另一個區域。 目前沒有任何內建機制或工具可協助進行這項工作，但這篇文章可協助您瞭解達到相同結果的手動步驟。

> [!NOTE]
> 在 Azure 入口網站中，所有服務都有 [ **匯出範本** ] 命令。 在 Azure 認知搜尋的情況下，此命令會產生服務的基本定義 (名稱、位置、層級、複本和分割區計數) ，但無法辨識您服務的內容，也不會對金鑰、角色或記錄檔執行。 雖然命令存在，但不建議您使用它來移動搜尋服務。

## <a name="prerequisites"></a>必要條件

+ 確定目的地區域中支援您帳戶所使用的服務和功能。

+ 對於預覽功能，確定您的訂用帳戶已列入目標區域的允許清單中。

## <a name="prepare-and-move"></a>準備及移動

1. 識別相依性和相關服務，以瞭解重新放置服務的完整影響，以防您需要移動不只是 Azure 認知搜尋。

   Azure 儲存體用於記錄、建立知識存放區，而且是用於 AI 擴充和編制索引的常用外部資料源。 認知服務是 AI 擴充的相依性。 如果您使用 AI 擴充，認知服務和搜尋服務都必須位於相同的區域中。

1. 建立服務上所有物件的清查，讓您知道要移動的內容：索引、同義字地圖、索引子、資料來源、技能集。 如果您已啟用記錄功能，請建立並封存歷史記錄所需的任何報表。

1. 檢查新區域中的定價和可用性，以確保 Azure 認知搜尋的可用性及新區域中的任何相關服務。 大部分的功能都可在所有區域中使用，但某些預覽功能的可用性有限。

1. 在新區域中建立服務，並從原始程式碼重新發行任何現有的索引、同義字地圖、索引子、資料來源和技能集。 請記住，服務名稱必須是唯一的，因此您無法重複使用現有的名稱。 檢查每個技能集，以查看與認知服務的連線是否仍在相同區域需求方面有效。 此外，如果已建立知識存放區，請檢查 Azure 儲存體的連接字串（如果您使用不同的服務）。

1. 重載索引和知識存放區（如果適用）。 您將使用應用程式程式碼將 JSON 資料推送至索引，或重新執行索引子，以便從外部來源提取檔。 

1. 啟用記錄功能，如果您正在使用記錄，請重新建立安全性角色。

1. 更新用戶端應用程式和測試套件，以使用新的服務名稱和 API 金鑰，並測試所有應用程式。

## <a name="discard-or-clean-up"></a>捨棄或清除

在新服務經過完整測試和運作之後，刪除舊的服務。 刪除服務會自動刪除與服務相關聯的所有內容。

## <a name="next-steps"></a>接下來的步驟

下列連結可協助您在完成上述步驟時找出詳細資訊。

+ [Azure 認知搜尋定價和區域](https://azure.microsoft.com/pricing/details/search/)
+ [選擇階層。](search-sku-tier.md)
+ [建立搜尋服務](search-create-service-portal.md)
+ [載入搜尋檔](search-what-is-data-import.md)
+ [啟用記錄](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->