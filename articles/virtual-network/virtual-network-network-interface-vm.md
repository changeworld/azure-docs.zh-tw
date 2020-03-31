---
title: 將網路介面添加到 Azure VM 或從 Azure VM 中刪除
description: 了解如何在虛擬機器中新增網路介面或移除網路介面。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060314"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>在虛擬機器中新增網路介面或移除網路介面

瞭解如何在創建 Azure 虛擬機器 （VM） 時添加現有網路介面。 還學習添加或刪除網路介面從現有 VM 處於已停止（交易）狀態。 網路介面使 Azure VM 能夠與 Internet、Azure 和本地資源進行通信。 VM 具有一個或多個網路介面。 

如果您需要新增、變更或移除網路介面的 IP 位址，請參閱[管理網路介面 IP 位址](virtual-network-network-interface-addresses.md)。 要創建、更改或刪除網路介面，請參閱[管理網路介面](virtual-network-network-interface.md)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果沒有，則使用活動訂閱設置 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 在開始本文的其餘部分之前完成這些任務之一：

- **門戶使用者**：使用 Azure 帳戶登錄到[Azure 門戶](https://portal.azure.com)。

- **PowerShell 使用者**：在[Azure 雲殼](https://shell.azure.com/powershell)中運行命令，或者從電腦運行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure 雲殼瀏覽器選項卡中，查找 **"選擇環境**"下拉清單，如果尚未選擇**PowerShell，請選擇 PowerShell。**

    如果在本地運行 PowerShell，請使用 Azure PowerShell 模組版本 1.0.0 或更高版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 執行 `Connect-AzAccount` 來建立與 Azure 的連線。

- **Azure 命令列介面 （CLI） 使用者**：在 Azure[雲外殼](https://shell.azure.com/bash)中運行命令，或從電腦運行 CLI。 如果您在本地運行 Azure CLI，請使用 Azure CLI 版本 2.0.26 或更高版本。 執行 `az --version` 來了解安裝的版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 執行 `az login` 來建立與 Azure 的連線。

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>將現有的網路介面新增至新的 VM

通過門戶創建虛擬機器時，門戶會創建具有預設設置的網路介面，並將網路介面附加到 VM。 不能使用門戶將現有網路介面添加到新 VM，也不能創建具有多個網路介面的 VM。 您可以使用 CLI 或 PowerShell 執行這兩種操作。 一定要熟悉[這些限制](#constraints)。 如果您建立具有多個網路介面的 VM，則也必須設定作業系統，才能在建立 VM 之後正常使用它們。 了解如何設定 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 以供使用多個網路介面。

### <a name="commands"></a>命令

在創建 VM 之前，[請創建網路介面](virtual-network-network-interface.md#create-a-network-interface)。

|工具|Command|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>將網路介面新增至現有的 VM

要向虛擬機器添加網路介面，請進行：

1. 轉到[Azure 門戶](https://portal.azure.com)以查找現有虛擬機器。 搜索並選擇**虛擬機器**。

2. 選擇 VM 的名稱。 VM 必須支援您想要新增的網路介面數目。 要瞭解每個 VM 大小支援的網路介面數，請參閱 Azure 中[針對 Linux VM](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 Windows [VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的大小。

3. 在 VM 命令列中，在確認對話方塊中選擇 **"停止**"，然後**選擇"確定**"。 然後等待，直到 VM**的狀態**更改為 **"已停止（已處理）"。**

4. 在 VM 功能表列中，選擇**網路** > **附加網路介面**。 然後在 **"連接現有網路介面"** 中，選擇要附加的網路介面，然後選擇 **"確定**"。

    >[!NOTE]
    >您選擇的網路介面不能啟用加速網路，不能為其分配 IPv6 位址，並且必須存在於當前連接到 VM 的網路介面的同一虛擬網路中。

    如果您沒有現有的網路介面，則必須先建立一個。 若要這樣做，請選取 [建立網路介面]****。 若要深入了解如何建立網路介面的詳細資訊，請參閱[建立網路介面](virtual-network-network-interface.md#create-a-network-interface)。 若要深入了解將網路介面新增至虛擬機器時的其他條件約束，請參閱[條件約束](#constraints)。

5. 在 VM 功能表列中，選擇 **"開始概述** > **"** 以重新開機虛擬機器。

現在，您可以將 VM 作業系統配置為正確使用多個網路介面。 了解如何設定 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 以供使用多個網路介面。

### <a name="commands"></a>命令

|工具|Command|
|---|---|
|CLI|[az vm nic 添加](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add)（參考）;[詳細步驟](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[添加-AzVM網路介面](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)（參考）;[詳細步驟](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>檢視 VM 網路介面

您可以檢視目前連接至 VM 的網路介面，以了解每個網路介面的組態，以及指派給每個網路介面的 IP 位址。 

1. 轉到[Azure 門戶](https://portal.azure.com)以查找現有虛擬機器。 搜索並選擇**虛擬機器**。

    >[!NOTE]
    >使用為訂閱分配了"擁有者、參與者"或"網路參與者"角色的帳戶登錄。 若要深入了解如何將角色指派給帳戶的詳細資訊，請參閱 [Azure 角色型存取控制的內建角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。

2. 選擇要為其查看附加網路介面的 VM 的名稱。

3. 在 VM 功能表列中，選擇 **"網路**"。

若要了解網路介面設定以及如何變更它們，請參閱[管理網路介面](virtual-network-network-interface.md)。 若要了解如何新增、變更或移除指派給網路介面的 IP 位址，請參閱[管理網路介面 IP 位址](virtual-network-network-interface-addresses.md)。

### <a name="commands"></a>命令

|工具|Command|
|---|---|
|CLI|[az vm nic 清單](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>從 VM 移除網路介面

1. 轉到[Azure 門戶](https://portal.azure.com)以查找現有虛擬機器。 搜索並選擇**虛擬機器**。

2. 選擇要為其查看附加網路介面的 VM 的名稱。

3. 在 VM 工具列中，選擇 **"停止**"。

4. 等到 VM 的 [狀態]**** 變成 [已停止 (已取消配置)]**** 為止。

5. 在 VM 功能表列中，選擇**網路** > **分離網路介面**。

6. 在 **"分離網路介面**"對話方塊中，選擇要分離的網路介面。 然後選擇 **"確定**"。

    >[!NOTE]
    >如果只列出一個網路介面，則無法將其分離，因為虛擬機器必須始終至少附加一個網路介面。

### <a name="commands"></a>命令

|工具|Command|
|---|---|
|CLI|[az vm nic 刪除](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove)（參考）;[詳細步驟](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[刪除-AzVM網路介面](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)（參考）;[詳細步驟](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>條件約束

- VM 至少必須擁有一個連結的網路介面。

- VM 可連結的網路介面數量受限於 VM 大小所支援的數量。 要瞭解有關每個 VM 大小支援的網路介面數的詳細資訊，請參閱 Azure 中[針對 Linux VM](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 Windows [VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的大小。 所有大小都至少支援兩個網路介面。

- 添加到 VM 的網路介面當前無法附加到其他 VM。 若要深入了解如何建立網路介面的詳細資訊，請參閱[建立網路介面](virtual-network-network-interface.md#create-a-network-interface)。

- 過去，您只能將網路介面添加到支援多個網路介面且至少使用兩個網路介面創建的 VM。 無法將網路介面添加到使用一個網路介面創建的 VM，即使 VM 大小支援多個網路介面也是如此。 相反地，由於以至少兩個網路介面所建立的 VM 一定要有至少兩個網路介面，因此您只能從至少具有三個網路介面的 VM 中移除網路介面。 這些限制不再適用。 您現在可以建立具有數量不拘 (以 VM 大小可支援的數目為限) 網路介面的 VM。

- 預設情況下，連接到 VM 的第一個網路介面是*主*網路介面。 VM 中的所有其他網路介面均為「次要」** 網路介面。

- 您可以控制向哪個網路介面發送出站流量。 但是，預設情況下，VM 將所有出站流量發送到分配給主網路介面的主 IP 配置的 IP 位址。

- 在過去，相同可用性設定組中的所有 VM 都必須具有單一或多個網路介面。 具有任意多個 (最多可達 VM 大小所支援的數目) 網路介面的 VM 現在可存在於相同的可用性設定組中。 您只有在建立 VM 時，才能將 VM 新增到可用性設定組。 若要深入了解可用性設定組，請參閱[在 Azure 中管理 VM 的可用性](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。

- 您可以將同一 VM 中的網路介面連接到虛擬網路中的不同子網。 但是，網路介面必須全部連接到同一虛擬網路。

- 您可以將任何主要或次要網路介面之任何 IP 組態的任何 IP 位址新增至 Azure Load Balancer 後端集區。 在過去，只能將主要網路介面的主要 IP 位址新增到後端集區。 若要深入了解 IP 位址和設定，請參閱[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)。

- 刪除 VM 不會刪除附加到它的網路介面。 當您刪除 VM 時，就會中斷網路介面與該 VM 的連結。 您可以將這些網路介面添加到不同的 VM 或刪除它們。

- 與 IPv6 一樣，創建加速網路後無法將網路介面連接到 VM。 此外，若要利用加速網路，您也必須在 VM 作業系統內完成一些步驟。 深入了解加速網路，以及在 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虛擬機器使用時的其他條件約束。

## <a name="next-steps"></a>後續步驟

要創建具有多個網路介面或 IP 位址的 VM，請參閱：

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IPv4 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|建立具有私人 IPv6 位址的單一 NIC VM (在 Azure Load Balancer 後端)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager 範本](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
