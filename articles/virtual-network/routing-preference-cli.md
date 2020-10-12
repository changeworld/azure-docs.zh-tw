---
title: 使用 Azure CLI 設定公用 IP 位址的路由喜好設定
titlesuffix: Azure Virtual Network
description: 瞭解如何使用 Azure CLI，建立具有網際網路流量路由喜好設定的公用 IP。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 39514f8a34e85525e2a8900a8e6e3cc8656fb84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329290"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>使用 Azure CLI 設定公用 IP 位址的路由喜好設定

本文說明如何使用 Azure CLI，透過 ISP 網路 (**網際網路**選項) 為公用 IP 位址設定路由喜好設定。 建立公用 IP 位址之後，您可以將其與下列 Azure 資源建立關聯，以取得連至網際網路的輸入和輸出流量：

* 虛擬機器
* 虛擬機器擴展集
* Azure Kubernetes Service (AKS)
* 網際網路對向負載平衡器
* 應用程式閘道
* Azure 防火牆

根據預設，公用 IP 位址的路由喜好設定會設為所有 Azure 服務的 Microsoft 全域網路，而且可與任何 Azure 服務相關聯。

> [!IMPORTANT]
> 路由喜好設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.49 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="register-the-feature-for-your-subscription"></a>註冊訂用帳戶的功能
「路由喜好設定」功能目前為預覽狀態。 註冊訂用帳戶的功能，如下所示：
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>建立資源群組
使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 下列範例會在**美國東部** Azure 區域中建立一個資源群組：

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

使用 [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 命令，建立路由喜好設定為「網際網路」類型的公用 IP 位址，其格式如下所示。

下列命令會在「美國東部」 Azure 區域中建立一個路由喜好設定為「網際網路」的新公用 IP。

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

> [!NOTE]
>  目前，路由喜好設定僅支援 IPV4 公用 IP 位址。

您可以將上述建立的公用 IP 位址與 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器建立關聯。 使用教學課程頁面上的 CLI 章節：[將公用 IP 位址與虛擬機器建立關聯](associate-public-ip-address-vm.md#azure-cli)，以將公用 IP 與您的 VM 產生關聯。 您也可以將公用 IP 位址指派給負載平衡**前端**組態，將上述建立的公用 IP 位址與 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 建立關聯。 此公用 IP 位址作為負載平衡的虛擬 IP 位址 (VIP)。

## <a name="next-steps"></a>後續步驟

- 深入了解[公用 IP 位址中的路由喜好設定](routing-preference-overview.md)。 
- [使用 Azure CLI 設定 VM 的路由喜好設定](configure-routing-preference-virtual-machine-cli.md)。

