---
title: 如何跨區域移動您的服務資源
titleSuffix: Azure Cognitive Search
description: 本文將說明如何在 Azure 雲端中，將您的 Azure 認知搜尋資源從一個區域移至另一個區域。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599297"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>將您的 Azure 認知搜尋服務移至另一個 Azure 區域

若要將您的 Azure 認知服務帳戶從一個區域移至另一個區域，您將建立匯出範本來移動您的訂用帳戶。 移動您的訂用帳戶之後，您將需要移動資料，並重新建立您的服務。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 匯出範本。
> * 修改範本：新增目的地區域、搜尋和儲存體帳戶名稱。
> * 部署範本以建立新的搜尋和儲存體帳戶。
> * 在新區域中驗證您的服務狀態
> * 清除來源區域中的資源。

## <a name="prerequisites"></a>Prerequisites

- 確定目的地區域中支援您的帳戶所使用的服務和功能。

- 針對預覽功能，請確定您的訂用帳戶已列入目的地區域的允許清單中。 如需預覽功能的詳細資訊，請參閱[知識存放區](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro)、累加[擴充](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)和[私用端點](https://docs.microsoft.com/azure/search/service-create-private-endpoint)。

## <a name="assessment-and-planning"></a>評估與規劃

當您將搜尋服務移至新區域時，您必須將[您的資料移至新的儲存體服務](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account)，然後重建您的索引、技能集和知識存放區。 您應該記錄目前的設定並複製 json 檔案，讓您更輕鬆且更快速地重建您的服務。

## <a name="moving-your-search-services-resources"></a>移動搜尋服務的資源

若要開始，您將會匯出 Resource Manager 範本，然後加以修改。

### <a name="export-a-template"></a>匯出資料庫

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 移至您的資源群組頁面。

> [!div class="mx-imgBorder"]
> ![資源群組 頁面範例](./media/search-move-resource/export-template-sample.png)

3. 選取 [所有資源]。

3. 在左側導覽功能表中，選取 [**匯出範本**]。

4. 在 [**匯出範本**] 頁面中選擇 [**下載**]。

5. 找出您從入口網站下載的 .zip 檔案，並將該檔案解壓縮至您選擇的資料夾。

Zip 檔案包含組成範本的 json 檔案，以及用來部署範本的腳本。

### <a name="modify-the-template"></a>修改範本

您將會藉由變更搜尋和儲存體帳戶名稱和區域來修改範本。 這些名稱必須遵循每個服務和區域命名慣例的規則。 

若要取得區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的程式碼是不含空格的區功能變數名稱稱、**美國中部** = **centralus**。

1. 在 Azure 入口網站中，選取 [建立資源]。

2. 在 [搜尋 Marketplace] 中，輸入**範本部署**，然後按 **ENTER**。

3. 選取 [範本部署]。

4. 選取 [建立]。

5. 選取 [在編輯器中組建您自己的範本]。

6. 選取 [**載入**檔案]，然後依照指示載入您在上一節中下載並解壓縮的**範本. json**檔案。

7. 在**範本. json**檔案中，藉由設定搜尋和儲存體帳戶名稱的預設值來命名目標搜尋和儲存體帳戶。 

8. 將**範本. json**檔案中的**location**屬性編輯為您的搜尋和儲存體服務的目的地區域。 這個範例會將目的地區域設定為 `centralus`。

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
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

### <a name="deploy-the-template"></a>部署範本

1. 儲存**範本. json**檔案。

2. 輸入或選取屬性值：

- **訂用帳戶**：選取 Azure 訂用帳戶。

- **資源群組**：選取 [新建] 並指定資源群組名稱。

- **位置**：選取 Azure 位置。

3. 按一下 [**我同意上方所述的條款及條件**] 核取方塊，然後按一下 [**選取購買**] 按鈕。

## <a name="verifying-your-services-status-in-new-region"></a>正在驗證您的服務在新區域中的狀態

若要確認移動，請開啟新的資源群組，您的服務將會以新的區域列出。

若要將您的資料從來源區域移至目的地區域，請參閱這篇文章將[您的資料移至新的儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account)的指導方針。

## <a name="clean-up-resources-in-your-original-region"></a>清除原始區域中的資源

若要認可變更並完成移動您的服務帳戶，請刪除來源服務帳戶。

## <a name="next-steps"></a>後續步驟

[建立索引](https://docs.microsoft.com/azure/search/search-get-started-portal)

[建立技能集](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[建立知識存放區](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

