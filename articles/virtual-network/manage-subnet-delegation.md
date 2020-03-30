---
title: 在 Azure 虛擬網路中添加或刪除子網委派
titlesuffix: Azure Virtual Network
description: 瞭解如何在 Azure 中添加或刪除服務的委派子網。
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
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201861"
---
# <a name="add-or-remove-a-subnet-delegation"></a>添加或刪除子網委派

子網路委派提供明確的權限給服務以在部署服務時使用唯一識別碼在子網路中建立服務特定資源。 本文介紹如何為 Azure 服務添加或刪除委派子網。

## <a name="portal"></a>入口網站

### <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

### <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您將創建一個虛擬網路和子網，稍後將委託給 Azure 服務。

1. 在螢幕的左上角，選擇 **"創建資源** > **網路** > **虛擬網路**"。
1. 在 [建立虛擬網路]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入*我的虛擬網路*。 |
    | 位址空間 | 輸入 *10.0.0.0/16*。 |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]****，輸入 *myResourceGroup*，然後選取 [確定]****。 |
    | Location | 選擇**東美國**。|
    | 子網路 - 名稱 | 輸入 mySubnet**。 |
    | 子網路 - 位址範圍 | 輸入 *10.0.0.0/24*。 |
    |||
1. 將其餘部分保留為預設值，然後選擇 **"創建**"。

### <a name="permissions"></a>權限

如果未創建要委派到 Azure 服務的子網，則需要以下許可權： `Microsoft.Network/virtualNetworks/subnets/write`。

內置[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色還包含必要的許可權。

### <a name="delegate-a-subnet-to-an-azure-service"></a>將子網委派給 Azure 服務

在本節中，您將上一節中創建的子網委派給 Azure 服務。

1. 在入口網站的搜尋列中，輸入 *myVirtualNetwork*。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
2. 在搜尋結果中，選擇*我的虛擬網路*。
3. 選擇"**設置"** 下的**子網**，然後選擇 **"我的子網**"。
4. 在*mySubnet*頁上，對於**子網委派**清單，從**委託子網下列出的服務中選擇服務**（例如 **，Microsoft.DBforPostgreSQL/serverv2）。**  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>從 Azure 服務中刪除子網委派

1. 在入口網站的搜尋列中，輸入 *myVirtualNetwork*。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
2. 在搜尋結果中，選擇*我的虛擬網路*。
3. 選擇"**設置"** 下的**子網**，然後選擇 **"我的子網**"。
4. 在 *"我的子網*"頁中，對於**子網委派**清單，從**委託子網**下列出的服務中選擇 **"無**"到服務 。 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定在本地安裝和使用 Azure CLI，則本文要求您使用 Azure CLI 版本 2.0.28 或更高版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-a-resource-group"></a>建立資源群組
使用 [az group create](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下面的示例在**東部**位置創建名為**myResourceGroup**的資源組：

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>建立虛擬網路
使用 az 網路 vnet 創建，在**myResourceGroup**中創建名為**myVnet**的子網，創建名為**myVnet**的虛擬[網路](https://docs.microsoft.com/cli/azure/network/vnet)。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>權限

如果未創建要委派到 Azure 服務的子網，則需要以下許可權： `Microsoft.Network/virtualNetworks/subnets/write`。

內置[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色還包含必要的許可權。

### <a name="delegate-a-subnet-to-an-azure-service"></a>將子網委派給 Azure 服務

在本節中，您將上一節中創建的子網委派給 Azure 服務。 

使用[az 網路 vnet 子網更新](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)更新名為**mySubnet**的子網，並委派到 Azure 服務。  在此示例中 **，Microsoft.DBforPostgreSQL/serverv2**用於示例委派：

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

要驗證已應用委派，請使用[az 網路 vnet 子網顯示](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)。 驗證服務是否委託給屬性**服務名稱**下的子網：

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

### <a name="remove-subnet-delegation-from-an-azure-service"></a>從 Azure 服務中刪除子網委派

使用[az 網路 vnet 子網更新](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)從名為**mySubnet**的子網中刪除委派：

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
要驗證已刪除委派，請使用[az 網路 vnet 子網顯示](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)。 驗證服務從屬性**服務名稱**下的子網中刪除：

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
來自命令的輸出為空括弧：
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

下面的示例在*東部*位置創建名為*myResourceGroup*的資源組：

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>建立虛擬網路

使用["新-Az虛擬網路](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest)"在 **"新資源組**"中使用名為**mySubnet**的子網創建名為**myVnet** [的虛擬網路](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)。 虛擬網路的 IP 位址空間為**10.0.0.0/16**。 虛擬網路中的子網為**10.0.0.0/24**。  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>權限

如果未創建要委派到 Azure 服務的子網，則需要以下許可權： `Microsoft.Network/virtualNetworks/subnets/write`。

內置[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色還包含必要的許可權。

### <a name="delegate-a-subnet-to-an-azure-service"></a>將子網委派給 Azure 服務

在本節中，您將上一節中創建的子網委派給 Azure 服務。 

使用[Add-Az委派](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest)將名為**mySubnet**的子網與名為 **"i委派"** 的委派更新到 Azure 服務。  在此示例中 **，Microsoft.DBforPostgreSQL/serverv2**用於示例委派：

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用[Get-Az委派](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest)驗證委派：

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
### <a name="remove-subnet-delegation-from-an-azure-service"></a>從 Azure 服務中刪除子網委派

使用[Remove-Az委派](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest)從名為**mySubnet**的子網中刪除委派：

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
使用[Get-Az委派](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest)驗證已刪除委派：

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>後續步驟
- 瞭解如何在[Azure 中管理子網](virtual-network-manage-subnet.md)。
