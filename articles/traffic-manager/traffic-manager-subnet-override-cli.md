---
title: 使用 Azure CLI 進行 Azure 流量管理器子網覆蓋 |微軟文檔
description: 本文將説明您瞭解如何使用流量管理器子網覆蓋來覆蓋流量管理器設定檔的路由方法，以便通過預定義的 IP 範圍將流量定向到基於最終使用者 IP 位址的終結點，以及通過預定義的 IP 範圍到終結點映射。
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938479"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>使用 Azure CLI 進行流量管理器子網覆蓋

流量管理器子網覆蓋允許您更改設定檔的路由方法。  添加覆蓋將基於最終使用者的 IP 位址（具有預定義的 IP 範圍）將流量定向到終結點映射。 

## <a name="how-subnet-override-works"></a>子網覆蓋的工作原理

將子網覆蓋添加到流量管理器設定檔時，流量管理器將首先檢查最終使用者的 IP 位址是否有子網覆蓋。 如果找到一個，使用者的 DNS 查詢將定向到相應的終結點。  如果未找到映射，流量管理器將回落到設定檔的原始路由方法。 

IP 位址範圍可以指定為 CIDR 範圍（例如，1.2.3.0/24）或位址範圍（例如 1.2.3.4-5.6.7.8）。 與每個終結點關聯的 IP 範圍必須是唯一的。 不同終結點之間的 IP 範圍的任何重疊將導致流量管理器拒絕設定檔。

有兩種類型的路由設定檔支援子網覆蓋：

* **地理位置**- 如果流量管理器發現 DNS 查詢的 IP 位址的子網覆蓋，它將路由查詢到終結點，無論終結點的運行狀況是什麼。
* **性能**- 如果流量管理器發現 DNS 查詢的 IP 位址的子網覆蓋，則僅當流量正常時，它才會將流量路由到終結點。  如果子網重寫終結點不正常，流量管理器將回落到性能路由啟發式。

## <a name="create-a-traffic-manager-subnet-override"></a>創建流量管理器子網覆蓋

要創建流量管理器子網覆蓋，可以使用 Azure CLI 將覆蓋的子網添加到流量管理器終結點。

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，在執行本教學課程時，您必須執行 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>使用子網覆蓋更新流量管理器終結點。
使用 Azure CLI 使用[az 網路流量管理器終結點更新](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)終結點。

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

您可以通過使用 **--remove**選項運行[az 網路流量管理器終結點更新](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)來刪除 IP 位址範圍。

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>後續步驟
深入了解「流量管理員」的 [流量路由方法](traffic-manager-routing-methods.md)。

瞭解[子網流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)