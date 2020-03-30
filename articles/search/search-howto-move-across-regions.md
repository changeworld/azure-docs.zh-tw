---
title: 如何跨區域移動服務資源
titleSuffix: Azure Cognitive Search
description: 本文將介紹如何在 Azure 雲中將 Azure 認知搜索資源從一個區域移動到另一個區域。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246296"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>將 Azure 認知搜索服務移動到其他 Azure 區域

有時，客戶會詢問如何將搜索服務移動到其他區域。 目前，沒有內置機制或工具來説明完成該任務，但本文可以説明您瞭解實現相同結果的手動步驟。

> [!NOTE]
> 在 Azure 門戶中，所有服務都有**匯出範本**命令。 在 Azure 認知搜索中，此命令生成服務的基本定義（名稱、位置、層、副本和分區計數），但無法識別服務的內容，也不會傳遞金鑰、角色或日誌。 儘管該命令存在，但我們不建議使用它來移動搜索服務。

## <a name="guidance-for-moving-a-service"></a>移動服務指南

1. 確定依賴項和相關服務，以瞭解重新置放服務的全部影響，以防需要移動的不僅僅是 Azure 認知搜索。

   Azure 存儲用於日誌記錄、創建知識存儲，是用於 AI 充實和索引的常用外部資料源。 認知服務是 AI 豐富的依賴項。 如果使用 AI 擴充，認知服務和搜索服務都必須位於同一區域。

1. 創建服務上所有物件的清單，以便知道要移動的內容：索引、同義字映射、索引子、資料來源、技能集。 如果啟用了日誌記錄，請創建並存檔歷史記錄可能需要的任何報表。

1. 檢查新區域中的定價和可用性，以確保 Azure 認知搜索以及新區域中的任何相關服務的可用性。 大多數功能在所有區域都可用，但某些預覽功能的可用性有限。

1. 在新區域中創建服務，並從原始程式碼中重新發佈任何現有索引、同義字映射、索引子、資料來源和技能集。 請記住，服務名稱必須是唯一的，因此不能重用現有名稱。 檢查每個技能集，查看與認知服務的連接是否仍然在同一區域要求方面有效。 此外，如果創建了知識存儲，則檢查 Azure 存儲的連接字串（如果您使用的是其他服務）。

1. 重新載入索引和知識存儲（如果適用）。 您將使用應用程式代碼將 JSON 資料推送到索引中，或者重新運行索引子從外部源拉入文檔。 

1. 啟用日誌記錄，如果您正在使用它們，請重新創建安全形色。

1. 更新用戶端應用程式和測試套件以使用新的服務名稱和 API 金鑰，並測試所有應用程式。

1. 新服務經過全面測試並運行後，將刪除舊服務。

## <a name="next-steps"></a>後續步驟

以下連結可説明您在完成上述步驟時找到更多資訊。

+ [Azure 認知搜索定價和區域](https://azure.microsoft.com/pricing/details/search/)
+ [選擇階層。](search-sku-tier.md)
+ [創建搜索服務](search-create-service-portal.md)
+ [載入搜索文檔](search-what-is-data-import.md)
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

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

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

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->