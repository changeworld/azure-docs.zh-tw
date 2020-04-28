---
title: 在 Azure 中跨租使用者共用資源庫映射
description: 瞭解如何使用共用映射資源庫跨 Azure 租使用者共用 VM 映射。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276347"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>跨 Azure 租使用者共用資源庫 VM 映射

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>使用 Azure CLI 建立擴展集

使用 appID、應用程式金鑰和租使用者1的識別碼，登入租使用者1的服務主體。 如有需要`az account show --query "tenantId"` ，您可以使用來取得租使用者識別碼。

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
使用 appID、應用程式金鑰和租使用者2的識別碼，登入租使用者2的服務主體：

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

建立擴展集。 以您自己的方式取代範例中的資訊。

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>後續步驟

若遇到任何問題，您可以[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。
