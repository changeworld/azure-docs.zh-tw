---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 196dfdc045fd60e4a253857087177f478f50ea24
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050513"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>透過範本標記虛擬機器
首先，我們來看一下透過範本進行標記。 [此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) 將標記置於下列資源上：運算 (虛擬機器)、儲存體 (儲存體帳戶) 和網路 (公用 IP 位址、虛擬網路和網路介面)。 這個範本適用於 Windows VM，但也可改寫成適用於 Linux VM。

按一下[範本連結](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)中的 [部署至 Azure]**** 按鈕。 這會瀏覽至 [Azure 入口網站](https://portal.azure.com/) ，以便您部署此範本。

![使用標記的簡單部署](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

此範本包含下列標記：「部門」**、「應用程式」** 以及「建立者」**。 如果您想要不同的標記名稱，您可以直接在範本中新增/編輯這些標記。

![範本中的 Azure 標記](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

如您所見，標記會定義為成對的「索引鍵/值」，並會以冒號 (:) 分隔。 標記必須以這種格式定義：

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

在您使用您所選擇的標籤完成編輯後，儲存範本檔案。

接著，在 [編輯參數 **** ] 區段中，您可以填寫標記的值。

![在 Azure 入口網站中編輯標記](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

按一下 [建立 **** ]，使用您的標記值來部署此範本。

## <a name="tagging-through-the-portal"></a>透過入口網站進行標記
使用標記建立您的資源之後，您可以在入口網站中檢視、新增和刪除標記。

選取標記圖示以檢視您的標記：

![Azure 入口網站中的標記圖示](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

透過入口網站定義自己的索引鍵/值組，以加入新標記並加以儲存。

![在 Azure 入口網站中新增標記](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

新標籤現在應出現在您的資源的標記清單中。

![Azure 入口網站中儲存的新標記](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

