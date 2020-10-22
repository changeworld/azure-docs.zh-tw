---
title: 管理 Azure 虛擬機器擴展集中的容錯網域
description: 了解如何在建立虛擬機器擴展集時選擇正確的 FD 數目。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 70e1a131daf95a9b0f6ae75c022dec2fdbc394a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366647"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>為虛擬機器擴展集選擇正確的容錯網域數目
在沒有任何區域 (zone) 的 Azure 區域 (region) 中，預設會建立具有五個容錯網域的虛擬機器擴展集。 針對支援虛擬機器擴展集之區域部署的區域，選取此選項時，每個區域的容錯網域計數預設值為1。 在此情況下，FD = 1 表示屬於擴展集的 VM 執行個體會儘可能分散於許多機架。

您也可以考慮讓擴展集容錯網域數目與受控磁碟容錯網域數目保持一致。 如果整個受控磁碟容錯網域停止運作，此一致狀態有助於防止仲裁遺失。 您可將 FD 計數設定為小於或等於每個區域中可用的受控磁碟容錯網域數目。 請參考這份[文件](../virtual-machines/manage-availability.md)，了解各區域的受控磁碟容錯網域數目。

## <a name="rest-api"></a>REST API
`properties.platformFaultDomainCount`如果未指定) ，您可以將屬性設定為1、2或 3 (預設值3。 請參考[這裡](/rest/api/compute/virtualmachinescalesets/createorupdate)的 REST API 文件。

## <a name="azure-cli"></a>Azure CLI
`--platform-fault-domain-count`如果未指定) ，您可以將參數設定為1、2或 3 (預設值3。 請參考[這裡](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)的 Azure CLI 文件。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟
- 深入了解 Azure 環境的[可用性和備援功能](../virtual-machines/availability.md)。