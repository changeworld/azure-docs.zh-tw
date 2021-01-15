---
title: 在 Azure Vm 中新增或移除網路介面
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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 2b45bd2e2b9ad971891cbcb944286f40b4f66883
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216944"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>在虛擬機器中新增網路介面或移除網路介面

瞭解如何在建立 Azure 虛擬機器 (VM) 時新增現有的網路介面。 此外，瞭解如何在已停止 (解除配置) 狀態的現有 VM 中新增或移除網路介面。 網路介面可讓 Azure VM 與網際網路、Azure 和內部部署資源進行通訊。 VM 有一或多個網路介面。 

如果您需要新增、變更或移除網路介面的 IP 位址，請參閱[管理網路介面 IP 位址](virtual-network-network-interface-addresses.md)。 若要建立、變更或刪除網路介面，請參閱 [管理網路介面](virtual-network-network-interface.md)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您沒有帳戶，請使用有效的訂用帳戶來設定 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 開始本文的其餘部分之前，請先完成下列其中一項工作：

- **入口網站使用者**：使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

- **PowerShell 使用者**：請在 [Azure Cloud Shell](https://shell.azure.com/powershell)中執行命令，或從您的電腦執行 powershell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 在 Azure Cloud Shell 瀏覽器] 索引標籤中，尋找 [ **選取環境** ] 下拉式清單，然後挑選 **PowerShell** （若尚未選取）。

    如果您在本機執行 PowerShell，請使用 Azure PowerShell 模組1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az.Network` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 執行 `Connect-AzAccount` 來建立與 Azure 的連線。

- **Azure 命令列介面 (CLI) 使用者**：請在 [Azure Cloud Shell](https://shell.azure.com/bash)中執行命令，或從您的電腦執行 cli。 如果您是在本機執行 Azure CLI，請使用 Azure CLI 版本2.0.26 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 執行 `az login` 來建立與 Azure 的連線。

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>將現有的網路介面新增至新的 VM

當您透過入口網站建立虛擬機器時，入口網站會建立具有預設設定的網路介面，並為您將網路介面連結至 VM。 您無法使用入口網站將現有的網路介面新增至新的 VM，或建立具有多個網路介面的 VM。 您可以使用 CLI 或 PowerShell 來進行這兩項作業。 請務必熟悉這些 [條件約束](#constraints)。 如果您建立具有多個網路介面的 VM，則也必須設定作業系統，才能在建立 VM 之後正常使用它們。 了解如何設定 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 以供使用多個網路介面。

### <a name="commands"></a>命令

建立 VM 之前，請先 [建立網路介面](virtual-network-network-interface.md#create-a-network-interface)。

|工具|命令|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>將網路介面新增至現有的 VM

若要將網路介面新增至您的虛擬機器：

1. 移至 [Azure 入口網站](https://portal.azure.com) 尋找現有的虛擬機器。 搜尋並選取 [虛擬機器]。

2. 選取您的 VM 名稱。 VM 必須支援您想要新增的網路介面數目。 若要瞭解每個 VM 大小所支援的網路介面數目，請參閱 Azure 中適用于 [Linux vm](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows vm](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的大小。

3. 在 VM 命令列中，選取 [ **停止**]，然後在確認對話方塊中選取 **[確定** ]。 然後等到 VM 的 **狀態** 變更為 [ **已停止]， (解除配置)**。

4. 從 VM 功能表列中，選擇 [**網路**  >  **連接網路介面**]。 然後，在 [ **連接現有的網路介面**] 中，選擇您想要連結的網路介面，然後選取 **[確定]**。

    >[!NOTE]
    >您選取的網路介面無法啟用加速網路、無法指派 IPv6 位址，而且必須存在於與目前連接至 VM 的網路介面相同的虛擬網路中。

    如果您沒有現有的網路介面，則必須先建立一個。 若要這樣做，請選取 [建立網路介面]。 若要深入了解如何建立網路介面的詳細資訊，請參閱[建立網路介面](virtual-network-network-interface.md#create-a-network-interface)。 若要深入了解將網路介面新增至虛擬機器時的其他條件約束，請參閱[條件約束](#constraints)。

5. 從 VM 功能表列中，選擇 [**總覽**  >  **開始**] 以重新開機虛擬機器。

現在您可以設定 VM 作業系統，以正確地使用多個網路介面。 了解如何設定 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 以供使用多個網路介面。

### <a name="commands"></a>命令

|工具|命令|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (reference) ; [詳細步驟](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[新增->add-azvmnetworkinterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (參考) ; [詳細步驟](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>檢視 VM 網路介面

您可以檢視目前連接至 VM 的網路介面，以了解每個網路介面的組態，以及指派給每個網路介面的 IP 位址。 

1. 移至 [Azure 入口網站](https://portal.azure.com) 尋找現有的虛擬機器。 搜尋並選取 [虛擬機器]。

    >[!NOTE]
    >使用已獲指派訂用帳戶擁有者、參與者或網路參與者角色的帳戶登入。 若要深入了解如何將角色指派給帳戶的詳細資訊，請參閱 [Azure 角色型存取控制的內建角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。

2. 選取您要查看其連接網路介面的 VM 名稱。

3. 在 VM 功能表列中，選取 [ **網路**]。

若要了解網路介面設定以及如何變更它們，請參閱[管理網路介面](virtual-network-network-interface.md)。 若要了解如何新增、變更或移除指派給網路介面的 IP 位址，請參閱[管理網路介面 IP 位址](virtual-network-network-interface-addresses.md)。

### <a name="commands"></a>命令

|工具|命令|
|---|---|
|CLI|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>從 VM 移除網路介面

1. 移至 [Azure 入口網站](https://portal.azure.com) 尋找現有的虛擬機器。 搜尋並選取 [虛擬機器]。

2. 選取您要查看其連接網路介面的 VM 名稱。

3. 在 VM 工具列中，選擇 [ **停止**]。

4. 等到 VM 的 [狀態] 變成 [已停止 (已取消配置)] 為止。

5. 從 VM 功能表列中，選擇 [**網路** 卸  >  **離網路介面**]。

6. 在 [卸 **離網路介面** ] 對話方塊中，選取您要卸離的網路介面。 然後選取 [確定]  。

    >[!NOTE]
    >如果只列出一個網路介面，您就無法卸離它，因為虛擬機器至少必須有一個連接的網路介面。

### <a name="commands"></a>命令

|工具|命令|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (參考) ; [詳細步驟](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[移除->add-azvmnetworkinterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (參考) ; [詳細步驟](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>條件約束

- VM 至少必須擁有一個連結的網路介面。

- VM 可連結的網路介面數量受限於 VM 大小所支援的數量。 若要深入瞭解每個 VM 大小所支援的網路介面數目，請參閱 Azure 中適用于 [Linux vm](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows vm](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的大小。 所有大小都至少支援兩個網路介面。

- 您新增至 VM 的網路介面目前無法連接至另一個 VM。 若要深入了解如何建立網路介面的詳細資訊，請參閱[建立網路介面](virtual-network-network-interface.md#create-a-network-interface)。

- 在過去，您只能將網路介面新增至支援多個網路介面的 Vm，並使用至少兩個網路介面來建立。 您無法將網路介面新增至使用一個網路介面建立的 VM，即使 VM 大小支援一個以上的網路介面。 相反地，由於以至少兩個網路介面所建立的 VM 一定要有至少兩個網路介面，因此您只能從至少具有三個網路介面的 VM 中移除網路介面。 這些條件約束不再適用。 您現在可以建立具有數量不拘 (以 VM 大小可支援的數目為限) 網路介面的 VM。

- 依預設，連接至 VM 的第一個網路介面是 *主要* 網路介面。 VM 中的所有其他網路介面均為「次要」網路介面。

- 您可以控制要將輸出流量傳送至哪個網路介面。 不過，VM 預設會將所有輸出流量傳送到指派給主要網路介面之主要 IP 設定的 IP 位址。

- 在過去，相同可用性設定組中的所有 VM 都必須具有單一或多個網路介面。 具有任意多個 (最多可達 VM 大小所支援的數目) 網路介面的 VM 現在可存在於相同的可用性設定組中。 您只有在建立 VM 時，才能將 VM 新增到可用性設定組。 若要深入了解可用性設定組，請參閱[在 Azure 中管理 VM 的可用性](../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。

- 您可以將相同 VM 中的網路介面連線到虛擬網路內的不同子網。 不過，網路介面必須全都連接到相同的虛擬網路。

- 您可以將任何主要或次要網路介面之任何 IP 組態的任何 IP 位址新增至 Azure Load Balancer 後端集區。 在過去，只能將主要網路介面的主要 IP 位址新增到後端集區。 若要深入了解 IP 位址和設定，請參閱[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)。

- 刪除 VM 並不會刪除與其連結的網路介面。 當您刪除 VM 時，就會中斷網路介面與該 VM 的連結。 您可以將這些網路介面新增至不同的 Vm，或將它們刪除。

- 所記載的最佳效能需要加速網路。 在某些情況下，您必須明確啟用 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虛擬機器的加速網路。

## <a name="next-steps"></a>後續步驟

若要建立具有多個網路介面或 IP 位址的 VM，請參閱：

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IPv4 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|建立具有私人 IPv6 位址的單一 NIC VM (在 Azure Load Balancer 後端)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager 範本](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|