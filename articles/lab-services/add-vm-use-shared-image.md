---
title: 使用 Azure 開發人員測試實驗室中的共用映射添加 VM |微軟文檔
description: 瞭解如何使用 Azure DevTest Labs 中附加的共用映射庫中的映射添加虛擬機器 （VM）
services: devtest-lab,virtual-machines
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
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775587"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>使用附加的共用映射庫中的圖像添加 VM
Azure 開發人員測試實驗室允許您將共用圖像庫附加到實驗室，然後將庫中的圖像用作在實驗室中創建的 VM 的基礎。 要瞭解如何將共用圖像庫附加到實驗室，請參閱[配置共用圖像庫](configure-shared-image-gallery.md)。 本文演示如何使用附加的共用映射庫中的圖像作為基礎將 VM 添加到實驗室。 

## <a name="azure-portal"></a>Azure 入口網站
在本節中，您將瞭解如何使用 Azure 門戶根據附加的共用映射庫中的圖像將 VM 添加到實驗室。 本節不提供有關使用 Azure 門戶創建 VM 的詳細分步說明。 有關這些詳細資訊，請參閱[創建 VM - Azure 門戶](devtest-lab-add-vm.md)。 它僅突出顯示從附加的共用圖像庫中選擇圖像並選擇要使用的圖像版本的步驟。 

將 VM 添加到實驗室時，可以從附加的共用映射庫中選擇圖像作為基本映射： 

![為基庫選擇共用映射](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

然後，在 **"創建實驗室資源**"頁的 **"高級設置**"選項卡上，您可以選擇要使用的圖像版本作為基本映射：

![選擇圖像版本](./media/add-vm-use-shared-image/select-version-shared-image.png)

創建 VM 後，可以切換到使用映射的不同版本。 

## <a name="resource-manager-template"></a>Resource Manager 範本
如果使用 Azure 資源管理器範本使用共用映射庫映射創建虛擬機器，請在 **"屬性"** 部分中為**共用 ImageId**指定值。 請參閱下列範例： 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

有關完整的資源管理器範本示例，請參閱使用 GitHub 存儲庫中的[共用映射庫映射示例創建虛擬機器](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)。 

## <a name="rest-api"></a>REST API

1. 首先，您需要在共用映射庫中獲取映射的 ID。 一種方法是使用以下 GET 命令列出附加的共用映射庫中的所有圖像。 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. 通過將從上一個調用收到的共用映射的 ID 傳遞給 在`properties.SharedImageId`虛擬機器上調用 PUT 方法。

## <a name="next-steps"></a>後續步驟
要瞭解如何將共用映射庫附加到實驗室並對其進行配置，請參閱[配置共用映射庫](configure-shared-image-gallery.md)。