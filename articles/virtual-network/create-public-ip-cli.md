---
title: 建立公用 IP-Azure CLI
description: 瞭解如何使用 Azure CLI 建立公用 IP
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 4d0934dd30f9738e2a67d4aff23c96a48aaaa61b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302001"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>快速入門：使用 Azure CLI 建立公用 IP 位址

本文說明如何使用 Azure CLI 建立公用 IP 位址資源。 如需有關這可以關聯哪些資源的詳細資訊，基本和標準 SKU 之間的差異，以及其他相關資訊，請參閱 [公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)。  在此範例中，我們只會將焦點放在 IPv4 位址;如需 IPv6 位址的詳細資訊，請參閱 [Azure VNet 的 ipv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)。

## <a name="prerequisites"></a>必要條件

- 已在本機安裝 Azure CLI 或 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果您選擇在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

在**eastus2**位置建立名為**myResourceGroup**的[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)資源群組。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**標準 SKU-使用區域**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>下列命令適用于 API 2020-08-01 版或更新版本。  如需目前所使用的 API 版本的詳細資訊，請參閱 [資源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)。

使用[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) ，在**myResourceGroup**中建立名為**myStandardZRPublicIP**的標準區域冗余公用 ip 位址。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> 針對早于2020-08-01 的 API 版本，請執行上述命令，而不指定區域參數來建立區域冗余 IP 位址。 
>

若要在**myResourceGroup**中建立名為**myStandardZonalPublicIP**區域2的標準區域性公用 IP 位址，請使用下列命令：

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

請注意，上述適用于區域的選項僅適用于具有 [可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)的區域中的有效選項。

# <a name="standard-sku---no-zones"></a>[**標準 SKU-沒有區域**](#tab/option-create-public-ip-standard)

>[!NOTE]
>下列命令適用于 API 2020-08-01 版或更新版本。  如需目前所使用的 API 版本的詳細資訊，請參閱 [資源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)。

使用[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)來建立標準公用 ip 位址，作為**myResourceGroup**中名為**myStandardPublicIP**的非區域性資源。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
此選項在所有區域中都是有效的，而且是區域中標準公用 IP 位址的預設選擇，而不 [可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) ，在**myResourceGroup**中建立名為**MYBASICPUBLICIP**的基本靜態公用 ip 位址。  基本公用 Ip 沒有可用性區域的概念。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard
    --allocation-method Static
```
如果 IP 位址在一段時間內可接受變更，則可以藉由將配置方法變更為「動態」來選取 **動態** IP 指派。

---

## <a name="additional-information"></a>其他資訊 

如需上列個別變數的詳細資訊，請參閱 [管理公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)。

## <a name="next-steps"></a>接下來的步驟
- 將 [公用 IP 位址與虛擬機器](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)產生關聯。
- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 深入了解所有[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)。