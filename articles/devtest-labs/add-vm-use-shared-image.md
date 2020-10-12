---
title: 使用 Azure DevTest Labs 中的共用映射新增 VM |Microsoft Docs
description: '瞭解如何在 Azure DevTest Labs 中使用連接的共用映射庫中的映射，將虛擬機器新增 (VM) '
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483851"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>使用附加的共用映射庫中的映射來新增 VM
Azure DevTest Labs 可讓您將共用映射庫連結至您的實驗室，然後使用資源庫中的映射做為您在實驗室中建立之 Vm 的基底。 若要瞭解如何將共用映射庫連結至您的實驗室，請參閱 [設定共用映射庫](configure-shared-image-gallery.md)。 本文說明如何使用連結的共用映射庫中的映射作為基底，將 VM 新增至您的實驗室。 

## <a name="azure-portal"></a>Azure 入口網站
在本節中，您會瞭解如何使用 Azure 入口網站，根據連結的共用映射庫中的映射，將 VM 新增至您的實驗室。 本節不會提供使用 Azure 入口網站建立 VM 的詳細逐步指示。 如需詳細資料，請參閱 [建立 VM-Azure 入口網站](devtest-lab-add-vm.md)。 它只會強調您從連結的共用映射庫中選取映射的步驟，並選取您要使用的映射版本。 

將 VM 新增至實驗室時，您可以從連結的共用映射庫選取映射作為基底映射： 

![選取基底的共用映射](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

然後，在 [**建立實驗室資源**] 頁面的 [高階**設定**] 索引標籤上，您可以選取要作為基礎映射的映射版本：

![選取映射版本](./media/add-vm-use-shared-image/select-version-shared-image.png)

您可以在建立 VM 之後切換至使用不同版本的映射。 

## <a name="resource-manager-template"></a>Resource Manager 範本
如果您要使用 Azure Resource Manager 範本來建立使用共用映射庫映射的虛擬機器，請在 [**屬性**] 區段中指定**sharedImageId**的值。 請參閱下列範例： 

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

如需完整的 Resource Manager 範本範例，請參閱我們的 GitHub 存放庫中 [的使用共用映射庫映射範例來建立虛擬機器](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) 。 

## <a name="rest-api"></a>REST API

1. 首先，您需要取得共用映射庫中映射的識別碼。 其中一種方式是使用下列 GET 命令，列出連接共用映射庫中的所有映射。 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. 在虛擬機器上叫用 PUT 方法，方法是將您從先前呼叫所收到的共用映射識別碼傳遞給 `properties.SharedImageId` 。

## <a name="next-steps"></a>接下來的步驟
若要瞭解如何將共用映射庫連結到實驗室並加以設定，請參閱 [設定共用映射庫](configure-shared-image-gallery.md)。