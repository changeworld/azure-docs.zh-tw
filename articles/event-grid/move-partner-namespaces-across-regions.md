---
title: 將 Azure 事件方格夥伴命名空間移至另一個區域
description: 本文說明如何將 Azure 事件方格夥伴命名空間從某個區域移至另一個區域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083632"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>將 Azure 事件方格夥伴命名空間移至另一個區域
您可能會因為許多原因而想要將資源移到另一個區域。 例如，若要利用新的 Azure 區域，以符合內部原則和治理需求，或為了回應容量規劃需求。 

以下是本文所涵蓋的高階步驟： 

- 將**夥伴命名空間資源匯出**至 Azure Resource Manager 範本。 刪除範本中事件通道資源的定義。 事件通道可以參考客戶所擁有之夥伴主題的 Azure Resource Manager 識別碼。 因此，您無法在目的地區域中使用範本來建立它們。  
- **使用範本將夥伴命名空間部署** 至目的地區域。 然後，在目的地區域的新夥伴命名空間中建立事件通道。 
- 若要 **完成移動**，請從來源區域中刪除夥伴命名空間。 

    > [!NOTE]
    > - 不支援將 **夥伴主題** 匯出至 Azure Resource Manager 範本，因為客戶無法直接建立夥伴主題。 
    > - **夥伴註冊** 是全域資源 (未系結至任何特定區域) ，因此將其從一個區域移至另一個區域並不適用。 

## <a name="prerequisites"></a>必要條件
- 確定事件方格服務可在目的地區域中使用。 請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>準備
若要開始使用，請匯出夥伴命名空間的 Resource Manager 範本。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端的搜尋列中，輸入 **事件方格夥伴命名空間**，然後從結果清單中選取 **事件方格夥伴命名空間** 。 
3. 選取您要匯出至 Resource Manager 範本的 **夥伴命名空間** 。 
4. 在 [**事件方格夥伴命名空間**] 頁面上，選取左側功能表的 [**設定**] 底下的 [**匯出範本**]，然後在工具列上選取 [**下載**]。 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="匯出範本-> 下載" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. 找出您從入口網站下載的 **.zip** 檔案，並將該檔案解壓縮至您選擇的資料夾。 此 zip 檔案包含範本和參數的 JSON 檔案。 
1. 在您選擇的編輯器中開啟 **template.js** 。 
8. 將 `location` **主題** 資源更新至目的地區域或位置。 若要取得位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是沒有空格的區功能變數名稱稱，例如， `West US` 等於 `westus` 。

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **儲存** 範本。 

## <a name="recreate"></a>重建 
部署範本，在目的地區域中建立夥伴命名空間。 

1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。
3. 選取 **範本部署**。
4. 選取 [建立]****。
5. 選取 [在編輯器中組建您自己的範本]。
6. 選取 [ **載入**檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。
7. 選取 [ **儲存** ] 以儲存範本。 
8. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行： 
    1. 選取 Azure **訂**用帳戶。 
    1. 選取目的地區域中的現有 **資源群組** ，或建立一個。 
    1. 針對 [ **位置**]，選取目的地區域。 如果您選取現有的資源群組，這項設定是唯讀的。 
    1. 針對 [ **夥伴命名空間名稱**]，輸入新夥伴命名空間的名稱。 
    1. 針對合作夥伴註冊的外部識別碼，請以下列格式輸入夥伴註冊的資源識別碼： `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` 。
    1. 選取 [我同意上方所述的條款及條件]**** 核取方塊。     
    1. 選取 [ **審核 + 建立** ] 以開始部署程式。 
    1. 在 [ **審核 + 建立** ] 頁面上，檢查 [設定]，然後選取 [ **建立**]。 

## <a name="discard-or-clean-up"></a>捨棄或清除
若要完成移動，請刪除來源區域中的夥伴命名空間。  

如果您想要重新開始，請刪除目的地區域中的夥伴命名空間，並重複本文的「 [準備](#prepare) 和 [重新](#recreate) 建立」各節中的步驟。

使用 Azure 入口網站來刪除夥伴命名空間：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **事件方格夥伴命名空間**，然後從搜尋結果中選取 **事件方格夥伴命名空間** 。 
2. 選取要刪除的夥伴命名空間，然後從工具列中選取 [ **刪除** ]。 
3. **確認** 刪除以刪除夥伴命名空間。 

## <a name="next-steps"></a>後續步驟
您已瞭解如何將事件方格夥伴命名空間從某個區域移至另一個區域。 請參閱下列文章，以瞭解如何跨區域移動系統主題、自訂主題和網域。

- [跨區域移動系統主題](move-system-topics-across-regions.md)。 
- [跨區域移動自訂主題](move-custom-topics-across-regions.md)。 
- [跨區域移動網域](move-domains-across-regions.md)。

若要深入瞭解如何在 Azure 中的區域與災難復原之間移動資源，請參閱下列文章： [將資源移至新的資源群組或訂](../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶。