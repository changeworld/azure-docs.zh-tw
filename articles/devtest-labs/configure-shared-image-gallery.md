---
title: 在 Azure DevTest Labs 中設定共用映射庫 |Microsoft Docs
description: 瞭解如何在 Azure DevTest Labs 中設定共用映射庫，讓使用者在建立實驗室資源時，從共用位置存取映射。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: febcff640efc29eb4916250366641635f9d8721e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788416"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定共用映像庫
DevTest Labs 現在支援 [共用映射庫](../virtual-machines/shared-image-galleries.md) 功能。 它可讓實驗室使用者在建立實驗室資源時，從共用位置存取映射。 它也可協助您在自訂管理的 VM 映射周圍建立結構和組織。 共用映射庫功能支援：

- 受控于映射的全域複製
- 更方便管理的映射版本控制和群組
- 在支援可用性區域的區域中，使用區域多餘的儲存體 (ZRS) 帳戶，讓您的映射具有高度可用性。 ZRS 針對區域性失敗提供更佳的復原能力。
- 使用 Azure 角色型存取控制 (Azure RBAC) 在訂用帳戶之間，甚至是在租使用者之間共用。

如需詳細資訊，請參閱 [共用映射庫檔](../virtual-machines/shared-image-galleries.md)。 
 
如果您有大量需要維護的受控映像，而且想要提供給全公司使用時，便可使用共用映像庫作為存放庫，以便輕鬆地更新及共用映像。 作為實驗室擁有者，您可以將現有的共用映射庫連結至您的實驗室。 程式庫附加之後，實驗室使用者就可以從這些最新的映射建立電腦。 這項功能的主要優點是 DevTest Labs 現在可以利用跨實驗室、跨訂用帳戶和跨區域共用映射的優點。 

> [!NOTE]
> 若要瞭解與共享映射資源庫服務相關聯的成本，請參閱 [共用映射庫的計費](../virtual-machines/shared-image-galleries.md#billing)。

## <a name="considerations"></a>考量
- 您一次只能將一個共用映射庫連結到實驗室。 如果您想要附加另一個資源庫，您必須卸離現有的資源庫，並附加另一個資源庫。 
- DevTest Labs 目前不支援透過實驗室將映射上傳至資源庫。 
- 使用共用映射庫映射建立虛擬機器時，DevTest Labs 一律會使用此映射的最新已發佈版本。 但是，如果映射有多個版本，使用者可以選擇在虛擬機器建立期間前往 [Advanced settings] （Advanced settings）索引標籤，從舊版建立電腦。  
- 雖然 DevTest Labs 會自動嘗試確保共用映射資源庫將影像複寫至實驗室所在區域，但不一定都能這麼做。 為了避免使用者在從這些映射建立 Vm 時遇到問題，請確定映射已複寫至實驗室的區域。」

## <a name="use-azure-portal"></a>使用 Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側導覽功能表上的 [ **所有服務** ]。
1. 從清單中選取 **DevTest Labs** 。
1. 從實驗室清單中，選取您的 **實驗室**。
1. 在左側功能表的 [設定] 區段中，選取 [**設定****與原則**]。
1. 在左側功能表中，選取 [**虛擬機器基底**] 下的 [**共用映射資源庫**]。

    ![共用映射資源庫功能表](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 按一下 [ **附加** ] 按鈕並在下拉式清單中選取您的資源庫，以將現有的共用映射庫連結至您的實驗室。

    ![Attach](./media/configure-shared-image-gallery/attach-options.png)
1. 程式庫程式庫之後，請選取該映射庫以移至附加的資源庫。 設定您的資源庫，以 **啟用或停** 用建立 VM 的共用映射。 從清單中選取映射庫以進行設定。 

    預設會 **允許使用所有映射，因為虛擬機器的基底** 設定為 **[是]**。 這表示在建立新的實驗室 VM 時，實驗室使用者將可使用附加共用映射庫中的所有可用映射。 如果需要限制特定映射的存取權，請將 [ **允許所有映射以虛擬機器為基礎** ] 變更為 [ **否**]，並選取您要在建立 vm 時允許的映射，然後選取 [ **儲存** ] 按鈕。

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="啟用或停用映射":::

    > [!NOTE]
    > 共用映射庫中的一般化和特製化映射都受到支援。 
1. 實驗室使用者接著可以按一下 [ **新增** ]，然後在 [ **選擇您的基礎** ] 頁面中尋找映射，藉以使用啟用的映射建立虛擬機器。

    ![實驗室使用者](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>將共用映射庫連結至您的實驗室
如果您要使用 Azure Resource Manager 範本將共用映射庫連結至您的實驗室，您需要將它新增至 Resource Manager 範本的資源區段底下，如下列範例所示：

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

如需完整的 Resource Manager 範本範例，請參閱我們的公用 GitHub 存放庫中的下列 Resource Manager 範本範例：在 [建立實驗室時設定共用映射庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

## <a name="use-rest-api"></a>使用 REST API

### <a name="get-a-list-of-labs"></a>取得實驗室清單 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>取得與實驗室相關聯的共用映射資源庫清單

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>建立或更新共用映射庫

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="list-images-in-a-shared-image-gallery"></a>列出共用映射庫中的映射

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>後續步驟
請參閱下列文章，以瞭解如何使用連結的共用映射庫中的映射建立 VM：[使用資源庫中的共用映射建立 vm](add-vm-use-shared-image.md) 。