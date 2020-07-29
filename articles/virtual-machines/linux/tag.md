---
title: 如何標記 Azure Linux 虛擬機器
description: 了解如何標記在 Azure 中以 Resource Manager 部署模型建立的 Azure Linux 虛擬機器。
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: bddb45be9232ff33629a4df0a5976e7b3c2e30b0
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369912"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中標記 Linux 虛擬機器
本文說明在 Azure 中透過 Resource Manager 部署模型標記 Linux 虛擬機器的各種不同方式。 標記是使用者定義的成對「索引鍵/值」，可直接置於資源或資源群組。 Azure 目前最多支援每個資源和資源群組50個標記。 標記可在建立或加入至現有資源時置於資源上。 請注意，標記只支援透過 Resource Manager 部署模型建立的資源。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>透過 Azure CLI 進行標記

若要開始，您需要安裝最新的 [Azure CLI](/cli/azure/install-azure-cli)，並使用 [az login](/cli/azure/reference-index#az-login) 來登入 Azure 帳戶。

您可以使用這個命令來檢視指定之虛擬機器的所有屬性，包括標記：

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

若要透過 Azure CLI 新增 VM 標記，您可以搭配使用 `azure vm update` 命令搭配標記參數 **--set**：

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

若要移除標記，您可以在 `azure vm update` 命令中使用 **--remove** 參數。

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

既然我們已將標記套用至我們的資源 Azure CLI 和入口網站，就讓我們來看一下使用量詳細資料，以在計費入口網站中查看標記。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>後續步驟
* 如需深入了解如何標記您的 Azure 資源，請參閱 [Azure Resource Manager 概觀][Azure Resource Manager Overview]與[使用標記來組織您的 Azure 資源][Using Tags to organize your Azure Resources]。
* 如需查看標記如何協助您管理使用 Azure 資源，請參閱[了解 Azure 帳單][Understanding your Azure Bill]與[深入了解 Microsoft Azure 資源耗用量][Gain insights into your Microsoft Azure resource consumption]。

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
