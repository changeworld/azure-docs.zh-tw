---
title: 設定 VM 的路由喜好設定 - Azure CLI
description: 了解如何使用 Azure 命令列介面 (CLI)，建立具有靜態公用 IP 位址和路由喜好設定選擇的 VM。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 59583d6d9e643c7b3f4c86198da04bde6b2db324
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595757"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>使用 Azure CLI 設定 VM 的路由喜好設定

本文說明如何設定虛擬機器的路由喜好設定。 當您選擇**網際網路**作為路由喜好設定選項時，來自 VM 的網際網路繫結流量將會透過 ISP 網路路由傳送。 預設路由是透過 Microsoft 全球網路。

本文說明如何使用 Azure CLI 來建立具有公用 IP 的虛擬機器，並將其設定為透過公用網際網路路由流量。

> [!IMPORTANT]
> 路由喜好設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-feature-for-your-subscription"></a>註冊訂用帳戶的功能
「路由喜好設定」功能目前為預覽狀態。 註冊訂用帳戶的功能，如下所示：
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```
## <a name="create-a-resource-group"></a>建立資源群組
1. 如果使用 Cloud Shell，請跳至步驟 2。 開啟命令工作階段，然後使用 `az login` 登入 Azure。
2. 使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 下列範例會在美國東部 Azure 區域中建立一個資源群組：

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址
您可以建立公用 IP 位址，透過網際網路存取您的虛擬機器。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立公用 IP 位址。 下列範例會在「美國東部」區域中，建立路由喜好設定類型「網際網路」 的公用 IP：

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>建立網路資源

您必須先建立支援的網路資源，包括網路安全性群組、虛擬網路和虛擬 NIC，才能部署 VM。

### <a name="create-a-network-security-group"></a>建立網路安全性群組

針對將使用 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) 在 VNet 中管理輸入和輸出通訊的規則，建立網路安全性群組

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) 建立虛擬網路。 下列範例會建立名為 myVNET 的虛擬網路和名為 mySubNet 的子網路：

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>建立 NIC

使用 [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) 建立 NIC 的虛擬 NIC。 下列範例會建立虛擬 NIC，以連接至 VM。

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立 VM。 下列範例會建立 Windows Server 2019 VM 及必要的虛擬網路元件 (如果尚未存在)。

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>清除資源

您可以使用 [az group delete](/cli/azure/group#az-group-delete) 來移除不再需要的資源群組，以及其所包含的所有資源：

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

- 深入了解[公用 IP 位址中的路由喜好設定](routing-preference-overview.md)。
- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 深入了解[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)。
