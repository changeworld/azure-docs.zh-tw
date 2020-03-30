---
title: 在 Azure 開發人員測試實驗室中配置共用映射庫 |微軟文檔
description: 瞭解如何在 Azure 開發人員測試實驗室中配置共用映射庫
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589312"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定共用映像庫
DevTest 實驗室現在支援[共用圖像庫](../virtual-machines/windows/shared-image-galleries.md)功能。 它使實驗室使用者能夠在創建實驗室資源的同時從共用位置訪問圖像。 它還可以説明您圍繞自訂管理的 VM 映射構建結構和組織。 共用圖像庫功能支援：

- 映射的託管全域複製
- 圖像的版本和分組，便於管理
- 在支援可用性區域的區域區域中，通過區域冗余存儲 （ZRS） 帳戶使映射高度可用。 ZRS 具有更好的抵禦區域故障的彈性。
- 使用基於角色的存取控制 （RBAC） 在訂閱之間，甚至租戶之間共用。

有關詳細資訊，請參閱[共用圖像庫文檔](../virtual-machines/windows/shared-image-galleries.md)。 
 
如果您有大量需要維護的受控映像，而且想要提供給全公司使用時，便可使用共用映像庫作為存放庫，以便輕鬆地更新及共用映像。 作為實驗室擁有者，您可以將現有的共用圖像庫附加到實驗室。 連接此庫後，實驗室使用者可以從這些最新圖像創建電腦。 此功能的一個主要好處是，DevTest Labs 現在可以利用跨實驗室、跨訂閱和區域共用圖像的優勢。 

> [!NOTE]
> 要瞭解與共享圖像庫服務關聯的成本，請參閱[共用圖像庫的計費](../virtual-machines/windows/shared-image-galleries.md#billing)。

## <a name="considerations"></a>考量
- 一次只能將一個共用圖像庫附加到實驗室。 如果要附加另一個庫，則需要分離現有庫並附加另一個庫。 
- DevTest Labs 目前僅支援共用圖像庫通用圖像。
- DevTest 實驗室目前不支援通過實驗室將圖像上載到庫。 
- 使用共用映射庫映射創建虛擬機器時，DevTest Labs 始終使用此映射的最新發佈版本。 但是，如果映射有多個版本，使用者可以選擇在虛擬機器創建期間使用"高級設置"選項卡從早期版本創建電腦。  
- 儘管 DevTest Labs 會自動做出最佳嘗試，以確保共用圖像庫將圖像複製到實驗室存在的區域，但並不總是可能的。 為了避免使用者在這些映射中創建 VM 時出現問題，請確保圖像已複製到實驗室的區域。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選擇左側導航功能表上**的所有服務**。
1. 從清單中選擇**開發人員測試實驗室**。
1. 從實驗室清單中，選擇您的**實驗室**。
1. 在左側功能表的 **"設置"** 部分中選擇 **"配置"和"策略**"。
1. 在左側功能表的 **"虛擬機器"底座**下選擇**共用圖像庫**。

    ![共用圖像庫功能表](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 按一下"**附加"** 按鈕並在下拉清單中選擇庫，將現有共用圖像庫附加到實驗室。

    ![連結](./media/configure-shared-image-gallery/attach-options.png)
1. 轉到附加的庫並配置庫以**啟用或禁用**共用映射以進行 VM 創建。 從清單中選擇一個映射庫來配置它。 

    預設情況下，**允許將所有映射用作虛擬機器庫**設置為 **"是**"。 這意味著在創建新的實驗室 VM 時，實驗室使用者可以使用附加的共用映射庫中的所有映射。 如果需要限制對某些映射的訪問，請更改 **"允許將所有映射用作虛擬機器庫**"更改為 **"否**"，然後選擇創建 VM 時要允許的圖像，然後選擇"**保存**"按鈕。

    ![啟用或禁用](./media/configure-shared-image-gallery/enable-disable.png)
1. 然後，實驗室使用者可以通過按一下 **+Add**並在**選擇基**頁上查找圖像來使用啟用的圖像創建虛擬機器。

    ![實驗室使用者](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>將共用圖像庫附加到實驗室
如果使用 Azure 資源管理器範本將共用映射庫附加到實驗室，則需要將其添加到資源管理器範本的資源部分下，如以下示例所示：

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

有關完整的資源管理器範本示例，請參閱我們的公共 GitHub 存儲庫中的這些資源管理器範本示例：[在創建實驗室時配置共用映射庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

## <a name="use-api"></a>使用 API

### <a name="shared-image-galleries---create-or-update"></a>共用圖像庫 - 創建或更新

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

### <a name="shared-image-galleries-images---list"></a>共用圖像庫圖像 - 清單 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>後續步驟
請參閱以下有關使用附加的共用映射庫中的映射創建 VM的文章：[使用庫中的共用映射創建 VM](add-vm-use-shared-image.md)
