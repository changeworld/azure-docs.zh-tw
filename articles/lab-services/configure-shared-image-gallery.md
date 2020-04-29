---
title: 在 Azure DevTest Labs 中設定共用映射資源庫 |Microsoft Docs
description: 瞭解如何在 Azure DevTest Labs 中設定共用映射資源庫
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589312"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定共用映像庫
DevTest Labs 現在支援[共用映射庫](../virtual-machines/windows/shared-image-galleries.md)功能。 它可讓實驗室使用者在建立實驗室資源時，從共用位置存取影像。 它也可協助您根據自訂管理的 VM 映射來建立結構和組織。 共用映射庫功能支援：

- 受管理的映射全域複寫
- 版本控制和群組映射以方便管理
- 在支援可用性區域的區域中，使用區域冗余儲存體（ZRS）帳戶，讓您的映射具有高度可用性。 ZRS 針對區域性失敗提供更佳的復原能力。
- 使用角色型存取控制（RBAC），跨訂用帳戶，甚至是在租使用者之間共用。

如需詳細資訊，請參閱[共用映射庫檔](../virtual-machines/windows/shared-image-galleries.md)。 
 
如果您有大量需要維護的受控映像，而且想要提供給全公司使用時，便可使用共用映像庫作為存放庫，以便輕鬆地更新及共用映像。 身為實驗室擁有者，您可以將現有的共用映射資源庫連結至您的實驗室。 附加此元件庫之後，實驗室使用者可以從這些最新的映射建立電腦。 這項功能的主要優點是，DevTest Labs 現在可以利用跨實驗室、跨訂用帳戶和跨區域共用映射的優勢。 

> [!NOTE]
> 若要瞭解與共享映射資源庫服務相關聯的成本，請參閱[共用映射資源庫的計費](../virtual-machines/windows/shared-image-galleries.md#billing)。

## <a name="considerations"></a>考量
- 您一次只能將一個共用映射資源庫附加至實驗室。 如果您想要附加另一個圖庫，您必須卸離現有的資源庫並附加另一個。 
- DevTest Labs 目前僅支援共用映射資源庫一般化映射。
- DevTest Labs 目前不支援透過實驗室將映射上傳至圖庫。 
- 使用共用映射資源庫映射建立虛擬機器時，DevTest Labs 一律會使用此映射的最新發佈版本。 不過，如果映射具有多個版本，使用者可以在建立虛擬機器期間前往 [Advanced settings] 索引標籤，選擇從舊版建立電腦。  
- 雖然 DevTest Labs 會自動嘗試確保共用映射資源庫將映射複寫到實驗室所在的區域，但不一定都是可行的做法。 若要避免使用者在從這些映射建立 Vm 時遇到問題，請確定映射已複寫至實驗室的區域。」

## <a name="use-azure-portal"></a>使用 Azure 入口網站
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側導覽功能表上的 [**所有服務**]。
1. 從清單中選取 [ **DevTest Labs** ]。
1. 從實驗室清單中，選取您的**實驗室**。
1. 在左側功能表的 [設定] 區段中，選取 [**設定****和原則**]。
1. 在左側功能表上，選取 [**虛擬機器基底**] 底下的 [**共用映射資源庫**]。

    ![共用映射資源庫功能表](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 按一下 [**附加**] 按鈕，然後在下拉式清單中選取您的資源庫，以將現有的共用映射資源庫連結至您的實驗室。

    ![附加](./media/configure-shared-image-gallery/attach-options.png)
1. 移至連結的資源庫，並設定您的資源庫，以**啟用或停**用建立 VM 的共用映射。 從清單中選取映射庫來加以設定。 

    根據預設，[**允許所有映射使用] 作為 [虛擬機器基底**] 設定為 **[是]**。 這表示在建立新的實驗室 VM 時，實驗室使用者可以使用附加的共用映射資源庫中的所有可用映射。 如果必須限制對特定映射的存取，請將 [**允許所有映射作為虛擬機器基底**] 變更為 [**否**]，然後選取您想要在建立 vm 時允許的映射，然後選取 [**儲存**] 按鈕。

    ![啟用或停用](./media/configure-shared-image-gallery/enable-disable.png)
1. 然後，實驗室使用者可以按一下 [ **+ 新增**]，然後在 [**選擇您的基礎**] 頁面中尋找影像，以使用已啟用的映射建立虛擬機器。

    ![實驗室使用者](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>將共用映射資源庫連結至您的實驗室
如果您使用 Azure Resource Manager 範本將共用映射資源庫連結至您的實驗室，您需要將它新增至 Resource Manager 範本的 resources 區段底下，如下列範例所示：

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

如需完整的 Resource Manager 範本範例，請參閱我們的公用 GitHub 存放庫中的下列 Resource Manager 範本範例：[建立實驗室時設定共用映射資源庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

## <a name="use-api"></a>使用 API

### <a name="shared-image-galleries---create-or-update"></a>共用映射資源庫-建立或更新

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

### <a name="shared-image-galleries-images---list"></a>共用映射庫影像-清單 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>後續步驟
請參閱下列文章：使用連結的共用映射資源庫中的映射建立 VM ..[使用資源庫中的共用映射建立 vm](add-vm-use-shared-image.md)
