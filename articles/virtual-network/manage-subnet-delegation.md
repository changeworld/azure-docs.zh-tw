---
title: 在 Azure 虛擬網路中新增或移除子網委派
titlesuffix: Azure Virtual Network
description: 瞭解如何在 Azure 中新增或移除服務的委派子網。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: d7fbb4c6f30754569b0aeea60f10d4a10e792ba7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933929"
---
# <a name="add-or-remove-a-subnet-delegation"></a>新增或移除子網委派

子網路委派提供明確的權限給服務以在部署服務時使用唯一識別碼在子網路中建立服務特定資源。 本文說明如何為 Azure 服務新增或移除委派的子網。

## <a name="portal"></a>入口網站

### <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

### <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您會建立虛擬網路和您稍後將委派給 Azure 服務的子網。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]。
1. 在 [建立虛擬網路] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 MyVirtualNetwork。 |
    | 位址空間 | 輸入 *10.0.0.0/16*。 |
    | 訂閱 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]，輸入 *myResourceGroup*，然後選取 [確定]。 |
    | 位置 | 選取 [ **EastUS**]。|
    | 子網路 - 名稱 | 輸入 mySubnet。 |
    | 子網路 - 位址範圍 | 輸入 *10.0.0.0/24*。 |
    |||
1. 將其餘部分保留為預設值，然後選取 [**建立**]。

### <a name="permissions"></a>使用權限

如果您未建立想要委派給 Azure 服務的子網，您需要下列許可權： `Microsoft.Network/virtualNetworks/subnets/write`。

內建[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必要的許可權。

### <a name="delegate-a-subnet-to-an-azure-service"></a>將子網委派給 Azure 服務

在本節中，您會將您在上一節中建立的子網委派給 Azure 服務。

1. 在入口網站的搜尋列中，輸入 *myVirtualNetwork*。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
2. 在搜尋結果中，選取 [ *myVirtualNetwork*]。
3. 選取 [**設定**] 底下的 [**子網**]，然後選取 [ **mySubnet**]。
4. 在 [ *mySubnet* ] 頁面的 [**子網委派**] 清單中，從 [**委派子網**] 底下所列的服務中選取服務（例如， **DBforPostgreSQL/serversv2**）。  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>從 Azure 服務移除子網委派

1. 在入口網站的搜尋列中，輸入 *myVirtualNetwork*。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
2. 在搜尋結果中，選取 [ *myVirtualNetwork*]。
3. 選取 [**設定**] 底下的 [**子網**]，然後選取 [ **mySubnet**]。
4. 在 [ *mySubnet* ] 頁面的 [**子網委派**] 清單中，從 [**委派子網至服務**] 底下所列的服務中選取 [**無**]。 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 Azure CLI，本文會要求您使用 Azure CLI 版本2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-a-resource-group"></a>建立資源群組
使用 [az group create](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>建立虛擬網路
使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet)，在 **myResourceGroup** 中建立名為 **myVNet**、具有子網路 **mySubnet** 的虛擬網路。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>使用權限

如果您未建立想要委派給 Azure 服務的子網，您需要下列許可權： `Microsoft.Network/virtualNetworks/subnets/write`。

內建[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必要的許可權。

### <a name="delegate-a-subnet-to-an-azure-service"></a>將子網委派給 Azure 服務

在本節中，您會將您在上一節中建立的子網委派給 Azure 服務。 

使用[az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) ，以 Azure 服務的委派來更新名為**mySubnet**的子網。  在此範例中，會使用**DBforPostgreSQL/serversv2**來進行範例委派：

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

若要確認已套用委派，請使用[az network vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)。 確認服務已委派給屬性為**serviceName**的子網：

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>從 Azure 服務移除子網委派

使用[az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) ，從名為**mySubnet**的子網移除委派：

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
若要確認已移除委派，請使用[az network vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)。 確認服務已從屬性**serviceName**的子網中移除：

```azurecli-interactive
  az network vnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
命令的輸出是空的括弧：
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>連線到 Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>建立資源群組
使用 [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>建立虛擬網路

使用**myResourceGroup**中的[new-azvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) ，建立名為**myVnet**的虛擬網路，其名稱為**MySubnet**的子網使用 new [-new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)。 虛擬網路的 IP 位址空間是**10.0.0.0/16**。 虛擬網路內的子網為**10.0.0.0/24**。  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>使用權限

如果您未建立想要委派給 Azure 服務的子網，您需要下列許可權： `Microsoft.Network/virtualNetworks/subnets/write`。

內建[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必要的許可權。

### <a name="delegate-a-subnet-to-an-azure-service"></a>將子網委派給 Azure 服務

在本節中，您會將您在上一節中建立的子網委派給 Azure 服務。 

使用[AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) ，將名為**mySubnet**的子網，使用名為**MyDelegation**的委派更新至 Azure 服務。  在此範例中，會使用**DBforPostgreSQL/serversv2**來進行範例委派：

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用[AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest)來驗證委派：

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>從 Azure 服務移除子網委派

使用[AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest)從名為**mySubnet**的子網移除委派：

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用[AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest)來確認已移除委派：

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>後續步驟
- 瞭解如何[管理 Azure 中的子網](virtual-network-manage-subnet.md)。
