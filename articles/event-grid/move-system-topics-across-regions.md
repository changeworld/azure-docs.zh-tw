---
title: 將 Azure 事件方格系統主題移至另一個區域
description: 本文說明如何將 Azure 事件方格系統主題從某個區域移至另一個區域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083621"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>將 Azure 事件方格系統主題移至另一個區域
您可能會因為許多原因而想要將資源移到另一個區域。 例如，若要利用新的 Azure 區域，以符合內部原則和治理需求，或為了回應容量規劃需求。 

以下是本文所涵蓋的高階步驟： 

- 將包含 Azure 儲存體帳戶及其相關聯繫統主題**的資源群組匯出**至 Resource Manager 範本。 您也可以只針對系統主題匯出範本。 如果您移至此路由，請記得將 Azure 事件來源 (在此範例中，Azure 儲存體帳戶) 至另一個區域，再移動系統主題。 然後，在系統主題的匯出範本中，更新目的地區域中儲存體帳戶的外部識別碼。 
- **修改範本** 以新增 `endpointUrl` 屬性，以指向訂閱系統主題的 webhook。 匯出系統主題時，其訂用帳戶 (在此案例中，它是 webhook) 也會匯出至範本，但 `endpointUrl` 不包含該屬性。 因此，您需要更新它以指向訂閱主題的端點。 此外，請將屬性的值更新 `location` 為新的位置或區域。 針對其他類型的事件處理常式，您只需要更新位置。 
- **使用範本將資源部署** 至目的地區域。 您將指定要在目的地區域中建立之儲存體帳戶和系統主題的名稱。 
- **確認部署**。 當您將檔案上傳至目的地區域中的 blob 儲存體時，請確認已叫用 webhook。 
- 若要 **完成移動**，請從來源區域) 的事件來源和系統主題 (刪除資源。 

## <a name="prerequisites"></a>必要條件
- 完成 [快速入門：將 Blob 儲存體事件路由至來源區域中具有 Azure 入口網站的 web 端點](blob-event-quickstart-portal.md) 。 此為**選用**步驟。 請在本文中測試步驟。 將儲存體帳戶保留在 App Service 的個別資源群組中，並 App Service 方案。 
- 確定事件方格服務可在目的地區域中使用。 請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>準備
若要開始使用，請為包含系統事件來源的資源群組匯出 Resource Manager 範本， (Azure 儲存體帳戶) 與其相關聯的系統主題。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側功能表上的 [ **資源群組** ]。 然後，選取包含已建立系統主題之事件來源的資源群組。 在下列範例中，這是 **Azure 儲存體** 帳戶。 資源群組包含儲存體帳戶及其相關聯的系統主題。 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="資源群組頁面":::        
3. 在左側功能表中，選取 [**設定**] 下的 [**匯出範本**]，然後在工具列上選取 [**下載**]。 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="儲存體帳戶-匯出範本頁面":::        
5. 找出您從入口網站下載的 **.zip** 檔案，並將該檔案解壓縮至您選擇的資料夾。 此 zip 檔案包含範本和參數的 JSON 檔案。 
1. 在您選擇的編輯器中開啟 **template.js** 。 
1. Webhook 的 URL 不會匯出至範本。 因此，請執行下列步驟：
    1. 在範本檔案中，搜尋 **WebHook**。 
    1. 在 [ **屬性** ] 區段中，于最後一行的結尾加入逗號 (`,`) 字元。 在此範例中為 `"preferredBatchSizeInKilobytes": 64`。 
    1. 將 `endpointUrl` 設定值的屬性新增至您的 WEBHOOK URL，如下列範例所示。 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > 針對其他類型的事件處理常式，會將所有屬性匯出至範本。 您只需要將屬性更新 `location` 為目的地區域，如下一個步驟所示。 
7. 將 `location` **儲存體帳戶** 資源更新為目的地區域或位置。 若要取得位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是沒有空格的區功能變數名稱稱，例如， `West US` 等於 `westus` 。

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. `location`針對範本中的**系統主題**資源重複執行更新步驟。 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **儲存** 範本。 

## <a name="recreate"></a>重建 
部署範本以建立儲存體帳戶，並在目的地區域中建立儲存體帳戶的系統主題。 

1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。
3. 選取 **範本部署**。
4. 選取 [建立]。
5. 選取 [在編輯器中組建您自己的範本]。
6. 選取 [ **載入**檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。
7. 選取 [ **儲存** ] 以儲存範本。 
8. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行。 
    1. 選取 Azure **訂**用帳戶。 
    1. 選取目的地區域中的現有 **資源群組** ，或建立一個。 
    1. 針對 [ **區域**]，選取目的地區域。 如果您選取現有的資源群組，這項設定是唯讀的。
    1. 針對 [ **系統主題名稱**]，輸入將與儲存體帳戶相關聯之系統主題的名稱。  
    1. 針對 [ **儲存體帳戶名稱**]，輸入要在目的地區域中建立之儲存體帳戶的名稱。 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="部署 Resource Manager 範本":::
    5. 選取頁面底部的 [檢閱 + 建立]。 
    1. 在 [ **審核 + 建立** ] 頁面上，檢查 [設定]，然後選取 [ **建立**]。 

## <a name="verify"></a>Verify
1. 部署成功後，請選取 [移 **至資源群組**]。 
1. 在 [ **資源群組** ] 頁面上，確認此範例中的事件來源 (、Azure 儲存體帳戶) 和系統主題已建立。 
1. 將檔案上傳至 Azure Blob 儲存體中的容器，並確認 webhook 已收到事件。 如需詳細資訊，請參閱 [將事件傳送至您的端點](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)。

## <a name="discard-or-clean-up"></a>捨棄或清除
若要完成移動，請在來源區域中刪除包含儲存體帳戶及其相關聯繫統主題的資源群組。  

如果您想要重新開始，請刪除目的地區域中的資源群組，並重複本文的「 [準備](#prepare) 和 [重新](#recreate) 建立」各節中的步驟。

使用 Azure 入口網站來刪除資源群組 (來源或目標) ：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **資源群組**，然後從搜尋結果中選取 **資源群組** 。 
2. 選取要刪除的資源群組，然後從工具列中選取 [ **刪除** ]。 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="刪除資源群組":::
3. 在 [確認] 頁面上，輸入資源群組的名稱，然後選取 [ **刪除**]。  

## <a name="next-steps"></a>後續步驟
您已瞭解如何將 Azure 事件來源及其相關聯的系統主題從一個區域移至另一個區域。 請參閱下列文章，以跨區域移動自訂主題、網域及夥伴命名空間。

- [跨區域移動自訂主題](move-custom-topics-across-regions.md)。 
- [跨區域移動網域](move-domains-across-regions.md)。 
- [跨區域移動夥伴命名空間](move-partner-namespaces-across-regions.md)。 

若要深入瞭解如何在 Azure 中的區域與災難復原之間移動資源，請參閱下列文章： [將資源移至新的資源群組或訂](../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶。
