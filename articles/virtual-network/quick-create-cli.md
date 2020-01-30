---
title: 建立虛擬網路 -快速入門 - Azure CLI
titlesuffix: Azure Virtual Network
description: 在此快速入門中，您將了解如何使用 Azure CLI 建立虛擬網路。 虛擬網路可讓 Azure 資源 (例如虛擬機器) 彼此及與網際網路進行私密通訊。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.openlocfilehash: 3cbfee90997c6b7cd9df1ec76543d77a4402100f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774527"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立虛擬網路

虛擬網路可讓 Azure 資源 (例如虛擬機器 (VM)) 彼此及與網際網路進行私密通訊。 在此快速入門中，您將了解如何建立虛擬網路。 建立虛擬網路之後，您需將兩個 VM 部署至虛擬網路中。 接著，您需從網際網路連線至其中一個 VM，然後透過新的虛擬網路進行私密通訊。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group-and-a-virtual-network"></a>建立資源群組和虛擬網路

建立虛擬網路之前，您必須先建立資源群組來裝載虛擬網路。 使用 [az group create](/cli/azure/group) 來建立資源群組。 此範例會在 *eastus* 位置中建立名為 *myResourceGroup* 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet) 建立虛擬網路。 此範例會建立一個名為 *myVirtualNetwork* 的預設虛擬網路，其中含有一個名為 *default* 的子網路：

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>建立虛擬機器

在虛擬網路內建立兩個 VM。

### <a name="create-the-first-vm"></a>建立第一個 VM

使用 [az vm create](/cli/azure/vm) 建立 VM。 如果預設金鑰位置中還沒有 SSH 金鑰，此命令將會建立這些金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。 `--no-wait` 選項會在背景建立 VM，以便您繼續進行下一步。 此範例會建立一個名為 *myVm1* 的 VM：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>建立第二個 VM

因為您在上一個步驟中使用 `--no-wait` 選項，您可以繼續並建立名為 *myVm2* 的第二個 VM。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI 輸出訊息

可能需要數分鐘才會建立虛擬機器。 在 Azure 建立 VM 之後，Azure CLI 會傳回如下列的輸出：

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

請記下 **publicIpAddress**。 在下一個步驟中，您將使用此位址來從網際網路連線至 VM。

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

在此命令中，將 `<publicIpAddress>` 取代為 *myVm2* VM 的公用 IP 位址：

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>虛擬機器之間的通訊

若要確認 *myVm2* 與 *myVm1* VM 之間的私密通訊，請輸入此命令：

```bash
ping myVm1 -c 4
```

您將會從 *10.0.0.4* 收到四個回覆。

結束與 *myVm2* VM 的 SSH 工作階段。

## <a name="clean-up-resources"></a>清除資源

您可以使用 [az group delete](/cli/azure/group) 來移除不再需要的資源群組，以及其所具有的所有資源：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您建立了一個預設的虛擬網路和兩個 VM。 您從網際網路連線至其中一個 VM，然後在兩個 VM 之間進行私密通訊。 若要深入了解虛擬網路設定，請參閱[管理虛擬網路](manage-virtual-network.md)。

Azure 可讓 VM 之間進行無限制的私密通訊。 根據預設，Azure 只會讓 Windows VM 的輸入遠端桌面連線從網際網路連線。 若要深入了解設定不同類型的 VM 網路通訊，請移至[篩選網路流量](tutorial-filter-network-traffic.md)教學課程。
