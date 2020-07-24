---
title: 建立具有加速網路功能的 Windows VM-Azure PowerShell
description: 瞭解如何建立具有加速網路功能的 Linux 虛擬機器。
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: 582553675284e88e4707812b1f6b459a4e67f14a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088184"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>使用 Azure PowerShell 建立具有加速網路的 Windows VM

在本教學課程中，您將瞭解如何建立具有加速網路功能的 Windows 虛擬機器（VM）。

> [!NOTE]
> 若要搭配 Linux 虛擬機器使用加速網路，請參閱[建立具有加速網路的 LINUX VM](create-vm-accelerated-networking-cli.md)。

加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 此高效能路徑會從資料路徑略過主機，這可減少支援的 VM 類型上最嚴苛的網路工作負載的延遲、抖動和 CPU 使用率。 下圖說明兩個 Vm 如何與和沒有加速網路通訊：

![Azure 虛擬機器之間的通訊，但不含加速網路](./media/create-vm-accelerated-networking/accelerated-networking.png)

如果沒有加速網路，進出 VM 的所有網路流量都必須周遊主機和虛擬交換器。 虛擬交換器對網路流量提供所有原則強制執行，例如網路安全性群組、存取控制清單、隔離性以及其他網路虛擬化服務。

> [!NOTE]
> 若要深入瞭解虛擬交換器，請參閱[Hyper-v 虛擬交換器](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)。

使用加速網路，網路流量會抵達 VM 的網路介面（NIC），然後轉送到 VM。 虛擬交換器套用的所有網路原則現在皆已卸載，並在硬體中套用。 由於原則會套用至硬體，因此 NIC 可以將網路流量直接轉送到 VM。 NIC 會略過主機和虛擬交換器，同時它會維護主機中所套用的所有原則。

加速網路的優點只適用于已啟用此功能的 VM。 為獲得最佳結果，請在至少兩部連線到相同 Azure 虛擬網路的 Vm 上啟用此功能。 跨虛擬網路通訊或連接內部部署時，此功能對整體延遲的影響最小。

## <a name="benefits"></a>優點

- **較低的延遲/較高的每秒封包數（pps）**：從資料路徑中排除虛擬交換器，會移除主機中用於原則處理的時間封包。 它也會增加可在 VM 內處理的封包數目。

- **減少抖動**：虛擬交換器處理視需要套用的原則數量而定。 它也取決於正在進行處理的 CPU 工作負載。 將原則強制執行卸載到硬體會藉由將封包直接傳遞到 VM 來移除該變異。 卸載也會移除主機對 VM 的通訊、所有的軟體插斷，以及所有內容切換。

- **降低 CPU 使用率**：略過主機中的虛擬交換器會導致處理網路流量的 CPU 使用率降低。

## <a name="supported-operating-systems"></a>支援的作業系統

下列是直接從 Azure 資源庫支援的散發套件：

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>限制和條件約束

### <a name="supported-vm-instances"></a>支援的 VM 執行個體

在具有兩個或多個虛擬 Cpu （個 vcpu）的一般用途和計算優化實例大小上，支援加速網路。  這些支援的系列為： Dv2/DSv2 和 F/Fs。

在支援超執行緒的實例上，具有四個或更多個 vcpu 的 VM 實例支援加速網路。 支援的系列為： D/Dsv3、D/Dsv4、Da/Dasv4、E/Esv3、Ea/Easv4、Fsv2、Lsv2、Ms/Mms 和 Ms/Mmsv2。

如需 VM 實例的詳細資訊，請參閱[Azure 中的 Windows 虛擬機器大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="custom-images"></a>自訂映像

如果您使用自訂映射，而且您的映射支援加速網路，請確定您有在 Azure 上搭配 Mellanox ConnectX-3 和 ConnectX-4 Lx Nic 使用的必要驅動程式。

### <a name="regions"></a>區域

加速網路功能適用于所有全域 Azure 區域和 Azure Government 雲端。

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>在執行中的 VM 上啟用加速網路

支援的 VM 大小若未啟用加速網路，則只有在停止並解除配置功能時，才會啟用此功能。

### <a name="deployment-through-azure-resource-manager"></a>透過 Azure Resource Manager 進行部署

無法使用加速網路部署虛擬機器（傳統）。

## <a name="vm-creation-using-the-portal"></a>使用入口網站建立 VM

雖然本文提供的步驟可讓您使用 Azure PowerShell 來建立具有加速網路的 VM，但您也可以[使用 Azure 入口網站來建立](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)可啟用加速網路的虛擬機器。 當您在入口網站中建立 VM 時，請在 [**建立虛擬機器**] 頁面中，選擇 [**網路**] 索引標籤。此索引標籤有**加速網路**的選項。 如果您已選擇[支援的作業系統](#supported-operating-systems)和[VM 大小](#supported-vm-instances)，此選項會自動設為 [**開啟**]。 否則，此選項會設定為 [**關閉**]，而 Azure 會顯示無法啟用的原因。

> [!NOTE]
> 只有支援的作業系統可以透過入口網站啟用。 如果您使用自訂映射，而且您的映射支援加速網路，請使用 CLI 或 PowerShell 來建立 VM。 

建立 VM 之後，您可以確認是否已啟用加速網路。 遵循下列指示：

1. 請移至[Azure 入口網站](https://portal.azure.com)來管理您的 vm。 搜尋並選取 [虛擬機器]。

2. 在 [虛擬機器] 清單中，選擇您的新 VM。

3. 在 VM 功能表列中，選擇 [**網路**]。

在 [網路介面資訊] 的 [**加速網路**] 標籤旁，入口網站會顯示 [**已停用**] 或 [**已啟用**] 以取得加速網路狀態。

## <a name="vm-creation-using-powershell"></a>使用 PowerShell 建立 VM

在繼續之前，請先安裝[Azure PowerShell](/powershell/azure/install-az-ps) 1.0.0 版或更新版本。 若要尋找您目前安裝的版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要安裝或升級，請從[PowerShell 資源庫](https://www.powershellgallery.com/packages/Az)安裝最新版本的 Az 模組。 在 PowerShell 會話中，使用[[Disconnect-azaccount]](/powershell/module/az.accounts/connect-azaccount)登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含*myResourceGroup*、 *myNic*和*myVM*。

### <a name="create-a-virtual-network"></a>建立虛擬網路

1. 使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 來建立資源群組。 下列命令會在*centralus*位置中建立名為*myResourceGroup*的資源群組：

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. 使用[new-azvirtualnetworksubnetconfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig)建立子網設定。 下列命令會建立名為*mySubnet*的子網：

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. 使用[new-azvirtualnetwork](/powershell/module/az.Network/New-azVirtualNetwork)建立具有*mySubnet*子網的虛擬網路。

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>建立網路安全性群組

1. 使用[new-aznetworksecurityruleconfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)建立網路安全性群組規則。

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. 使用[new-aznetworksecuritygroup](/powershell/module/az.Network/New-azNetworkSecurityGroup)建立網路安全性群組，並將 [*允許-RDP-所有*安全性] 規則指派給它。 除了 [*允許-RDP-全部*] 規則外，網路安全性群組包含數個預設規則。 一個預設規則會停用來自網際網路的所有輸入存取。 建立之後，[*允許-RDP-全部*] 規則會指派給網路安全性群組，讓您可以從遠端連線到 VM。

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. 將網路安全性群組與*mySubnet*子網建立關聯性[new-azvirtualnetworksubnetconfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig)。 網路安全性群組中的規則適用於子網路中部署的所有資源。

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>使用加速網路建立網路介面

1. 使用 [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) 建立公用 IP 位址。 如果您不打算從網際網路存取 VM，就不需要公用 IP 位址。 不過，您必須完成本文中的步驟。

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. 使用已啟用加速網路的[new-aznetworkinterface](/powershell/module/az.Network/New-azNetworkInterface)建立網路介面，並將公用 IP 位址指派給網路介面。 下列範例會在*myVnet*虛擬網路的*mySubnet*子網中建立名為*myNic*的網路介面，並將*myPublicIp*公用 IP 位址指派給它：

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>建立 VM 並連接網路介面

1. 使用 [取得-認證] 將您的 VM 認證設定為 `$cred` 變數，這會提示您登入： [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)

    ```azurepowershell
    $cred = Get-Credential
    ```

2. 使用 [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) 來定義您的 VM。 下列命令會使用支援加速網路的 VM 大小（*Standard_DS4_v2*），來定義名為*myVM*的 vm：

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    如需所有 VM 大小和特性的清單，請參閱 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

3. 使用 [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) 和 [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) 建立其餘的 VM 組態。 下列命令會建立 Windows Server 2016 VM：

    ```azurepowershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
    ```

4. 附加您先前使用[add-azvmnetworkinterface](/powershell/module/az.compute/add-azvmnetworkinterface)建立的網路介面：

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. 使用[update-azvm](/powershell/module/az.compute/new-azvm)建立您的 VM。

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>確認已在 Windows VM 中安裝乙太網路控制站

在 Azure 中建立 VM 之後，請連線至 VM，並確認已在 Windows 中安裝乙太網路控制器。

1. 請移至[Azure 入口網站](https://portal.azure.com)來管理您的 vm。 搜尋並選取 [虛擬機器]。

2. 在 [虛擬機器] 清單中，選擇您的新 VM。

3. 在 VM [總覽] 頁面中，如果 VM 的**狀態**列為 [**建立**中]，請等到 Azure 完成 vm 的建立。 VM 建立完成後，**狀態**會變更為 [**執行中]** 。

4. 從 VM 總覽工具列，選取 [連線 RDP **] [**  >  **RDP**  >  **下載 rdp**檔案]。

5. 開啟 .rdp 檔案，然後使用您在[建立 vm 和附加網路介面](#create-a-vm-and-attach-the-network-interface)一節中輸入的認證來登入 VM。 如果您從未連線到 Azure 中的 Windows VM，請參閱[連線至虛擬機器](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)。

6. 在您 VM 的遠端桌面會話出現之後，以滑鼠右鍵按一下 Windows [開始] 按鈕，然後選擇 [ **Device Manager**]。

7. 在 [ **Device Manager** ] 視窗中，展開 [**網路介面卡**] 節點。

8. 確認 [ **Mellanox ConnectX-3 虛擬函式 Ethernet 介面卡**] 出現，如下圖所示：

    ![Mellanox ConnectX-3 虛擬函式 Ethernet 介面卡，用於加速網路的新網路介面卡，Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

現在已為您的 VM 啟用加速網路。

> [!NOTE]
> 如果 Mellanox 介面卡無法啟動，請在遠端桌面會話中開啟系統管理員提示，並輸入下列命令：
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>在現有的 Vm 上啟用加速網路

如果您已建立不含加速網路的 VM，您可以在現有的 VM 上啟用此功能。 VM 必須支援加速網路，方法是符合下列必要條件，也就是上面所述：

* VM 必須是加速網路的支援大小。
* VM 必須是支援的 Azure 資源庫映射（以及適用于 Linux 的核心版本）。
* 您必須先停止或解除配置可用性設定組或虛擬機器擴展集中的所有 Vm，才能在任何 NIC 上啟用加速網路。

### <a name="individual-vms-and-vms-in-an-availability-set"></a>可用性設定組中的個別 Vm 和 Vm

1. 停止或解除配置 VM，或如果可用性設定組，則設定中的所有 Vm：

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > 當您個別建立 VM 時，若沒有可用性設定組，您只需要停止或解除配置個別 VM 以啟用加速網路。 如果您的 VM 是使用可用性設定組建立的，您必須先停止或解除配置可用性設定組中包含的所有 Vm，才能在任何 Nic 上啟用加速網路，讓 Vm 位於支援加速網路的叢集上。 如果您停用加速網路，則不需要停止或解除配置需求，因為支援加速網路的叢集也適用于不使用加速網路的 Nic。

2. 在您 VM 的 NIC 上啟用加速網路：

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. 重新開機您的 VM，如果在可用性設定組中，則為集合中的所有 Vm，並確認已啟用加速網路：

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>虛擬機器擴展集

虛擬機器擴展集略有不同，但會遵循相同的工作流程。

1. 停止 Vm：

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. 更新網路介面底下的 [加速網路] 屬性：

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. 將套用的更新設定為 [自動]，以立即挑選變更：

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > 擴展集具有 VM 升級，可使用三種不同的設定來套用更新：自動、輪流和手動。 在這些指示中，原則設定為 [自動]，因此擴展集會在重新開機後立即收取變更。

4. 重新開機擴展集：

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

重新開機之後，請等候升級完成。 完成升級之後，虛擬函式（VF）就會出現在 VM 內。 請確定您使用的是支援的 OS 和 VM 大小。

### <a name="resizing-existing-vms-with-accelerated-networking"></a>使用加速網路調整現有 Vm 的大小

如果 VM 已啟用加速網路，您就只能將其大小調整為支援加速網路的 VM。  

已啟用加速網路的 VM 無法調整為不支援使用調整大小作業加速網路的 VM 實例。 相反的，若要為這些 VM 的其中一個調整大小，請執行下列動作：

1. 停止或解除配置 VM。 針對可用性設定組或擴展集，請停止或解除配置可用性設定組或擴展集中的所有 Vm。

2. 停用 VM 的 NIC 上的加速網路。 針對可用性設定組或擴展集，請停用可用性設定組或擴展集中所有 Vm 的 Nic 上的加速網路。

3. 停用加速網路之後，請將 VM、可用性設定組或擴展集移至不支援加速網路的新大小，然後重新開機它們。
