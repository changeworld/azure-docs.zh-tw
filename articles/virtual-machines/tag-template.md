---
title: 如何使用範本標記 VM
description: 瞭解如何使用範本標記虛擬機器。
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: d1acbe82a086574a102e7897bbd3b99683c1185e
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594925"
---
# <a name="tagging-a-vm-using-a-template"></a>使用範本來標記 VM


本文說明如何使用 Resource Manager 範本，在 Azure 中標記 VM。 標記是使用者定義的成對「索引鍵/值」，可直接置於資源或資源群組。 Azure 目前最多支援每個資源和資源群組50個標記。 標記可在建立或加入至現有資源時置於資源上。

[此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) 將標記置於下列資源上：運算 (虛擬機器)、儲存體 (儲存體帳戶) 和網路 (公用 IP 位址、虛擬網路和網路介面)。 這個範本適用於 Windows VM，但也可改寫成適用於 Linux VM。

按一下[範本連結](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)中的 [部署至 Azure] 按鈕。 這會瀏覽至 [Azure 入口網站](https://portal.azure.com/) ，以便您部署此範本。

![使用標記的簡單部署](./media/tag/deploy-to-azure-tags.png)

此範本包含下列標記：「部門」、「應用程式」以及「建立者」。 如果您想要不同的標記名稱，您可以直接在範本中新增/編輯這些標記。

![範本中的 Azure 標記](./media/tag/azure-tags-in-a-template.png)

如您所見，標記會定義為成對的「索引鍵/值」，並會以冒號 (:) 分隔。 標記必須以這種格式定義：

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

在您使用您所選擇的標籤完成編輯後，儲存範本檔案。

接著，在 [編輯參數  ] 區段中，您可以填寫標記的值。

![在 Azure 入口網站中編輯標記](./media/tag/edit-tags-in-azure-portal.png)

按一下 [建立  ]，使用您的標記值來部署此範本。


**後續步驟**

- 如需深入了解如何標記您的 Azure 資源，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/management/overview.md)與[使用標記來組織您的 Azure 資源](../azure-resource-manager/management/tag-resources.md)。
- 如需查看標記如何協助您管理使用 Azure 資源，請參閱[了解 Azure 帳單](../cost-management-billing/understand/review-individual-bill.md)與[深入了解 Microsoft Azure 資源耗用量](../cost-management-billing/manage/usage-rate-card-overview.md)。
