---
title: 在 Azure 中的租戶之間分享庫影像
description: 瞭解如何使用共享映射庫跨 Azure 租戶共用 VM 映射。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 4259ca01dbe45463b73cf1ec1c620c3921ab9459
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758477"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>跨 Azure 租戶分享庫 VM 映像

共用圖像庫允許您使用 RBAC 共用圖像。 您可以使用 RBAC 在租戶中共用圖像,甚至共用租戶外部的個人。 有關此簡單共享選項的詳細資訊,請參閱[共享函式庫](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)。

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 不能使用門戶從另一個 Azure 租戶中的映射部署 VM。 要從租戶之間共用的映像建立 VM,必須使用 Azure CLI 或[Powershell](../windows/share-images-across-tenants.md)。

## <a name="create-a-vm-using-azure-cli"></a>使用 Azure CLI 建立 VM

使用應用ID、應用金鑰和租戶1的ID登錄租戶1的服務主體。 如果需要`az account show --query "tenantId"`,可以使用 獲取租戶 ID。

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
使用 appID、應用金鑰和租戶 2 的 ID 登錄租戶 2 的服務主體:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

建立 VM。 將範例中的資訊取代為您自己的資訊。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>後續步驟

若遇到任何問題，您可以[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。