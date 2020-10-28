---
title: " (PowerShell) 設定可用性群組接聽程式和負載平衡器"
description: 使用具有一或多個 IP 位址的內部負載平衡器，在 Azure Resource Manager 模型上設定可用性群組接聽程式。
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: cb55274800b239cf0e1e942647ae0c65b321b862
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790044"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>設定一或多個 Always On 可用性群組接聽程式 - Resource Manager

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本檔說明如何使用 PowerShell 來執行下列其中一項工作：
- 建立負載平衡器
- 針對 SQL Server 可用性群組，將 IP 位址新增至現有的負載平衡器。

可用性群組接聽程式是用戶端連接以進行資料庫存取的虛擬網路名稱。 在 Azure 虛擬機器上，負載平衡器會保存接聽程式的 IP 位址。 負載平衡器會將流量路由傳送至在探查連接埠上進行接聽的 SQL Server 執行個體。 通常，可用性群組會使用內部負載平衡器。 Azure 內部負載平衡器可以裝載一或多個 IP 位址。 每個 IP 位址皆使用特定的探查連接埠。 

將多個 IP 位址指派給內部負載平衡器的能力是 Azure 的新功能，而且只能在 Resource Manager 模型中使用。 若要完成這項工作，您必須在 Resource Manager 模型中的 Azure 虛擬機器上部署 SQL Server 可用性群組。 這兩部 SQL Server 虛擬機器必須屬於相同的可用性設定組。 您可以使用 [Microsoft 範本](./availability-group-quickstart-template-configure.md) 在 Azure Resource Manager 中自動建立可用性群組。 此範本會自動為您建立可用性群組，包括內部負載平衡器。 如果您想要的話，也可以[手動設定 Always On 可用性群組](availability-group-manually-configure-tutorial.md)。

若要完成本文中的步驟，您必須已經設定可用性群組。  

相關主題包括：

* [在 Azure VM (GUI) 中設定 AlwaysOn 可用性群組](availability-group-manually-configure-tutorial.md)   
* [使用 Azure Resource Manager 和 PowerShell 來設定 VNet 對 VNet 連線](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>確認 PowerShell 版本

本文中的範例使用 Azure PowerShell 模組 5.4.1 版進行測試。

確認您的 PowerShell 模組是5.4.1 版或更新版本。

請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

## <a name="configure-the-windows-firewall"></a>設定 Windows 防火牆

設定 Windows 防火牆以允許 SQL Server 存取。 防火牆規則可允許透過 TCP 連線至 SQL Server 執行個體及接聽程式探查所使用的連接埠。 如需詳細的指示，請參閱[設定用於 Database Engine 存取的 Windows 防火牆](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1)。 為 SQL Server 連接埠和探查連接埠建立輸入規則。

如果您使用 Azure 網路安全性群組來限制存取，請確定允許規則包含後端 SQL Server VM IP 位址和 AG 接聽程式的負載平衡器浮動 IP 位址，以及叢集核心 IP 位址 (如果適用的話)。

## <a name="determine-the-load-balancer-sku-required"></a>判斷所需的負載平衡器 SKU

[Azure 負載平衡器](../../../load-balancer/load-balancer-overview.md) 提供兩種 Sku：基本 & 標準。 建議使用標準負載平衡器。 如果虛擬機器在可用性設定組中，則允許使用基本負載平衡器。 如果虛擬機器在可用性區域中，則必須使用標準負載平衡器。 標準負載平衡器會要求所有 VM 的 IP 位址使用標準的 IP 位址。

可用性群組的目前 [Microsoft 範本](./availability-group-quickstart-template-configure.md)會使用基本 IP 位址的基本負載平衡器。

   > [!NOTE]
   > 若將標準負載平衡器和 Azure 儲存體用於雲端見證，則需要設定[服務端點](../../../storage/common/storage-network-security.md?toc=%252fazure%252fvirtual-network%252ftoc.json#grant-access-from-a-virtual-network)。 
   > 

本文中的範例會指定標準負載平衡器。 在範例中，指令碼包含 `-sku Standard`。

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

若要建立基本負載平衡器，請從建立負載平衡器的該行中移除 `-sku Standard`。 例如：

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>範例指令碼：使用 PowerShell 建立內部負載平衡器

> [!NOTE]
> 如果您使用了 [Microsoft 範本](./availability-group-quickstart-template-configure.md)來建立可用性群組，則已經建立內部負載平衡器。

下列 PowerShell 腳本會建立內部負載平衡器、設定負載平衡規則，以及設定負載平衡器的 IP 位址。 若要執行腳本，請開啟 Windows PowerShell ISE，然後將腳本貼到腳本窗格中。 請使用 `Connect-AzAccount` 來登入 PowerShell。 如果您有多個 Azure 訂用帳戶，請使用 `Select-AzSubscription` 來設定訂用帳戶。 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>範例指令碼：使用 PowerShell 將 IP 位址新增至現有的負載平衡器

若要使用多個可用性群組，請將額外的 IP 位址新增至負載平衡器。 每個 IP 位址都需要有自己的負載平衡規則、探查埠和前端埠。

前端連接埠是應用程式用來連線到 SQL Server 執行個體的連接埠。 不同可用性群組的 IP 位址可以使用相同的前端連接埠。

> [!NOTE]
> 就 SQL Server 可用性群組而言，每個 IP 位址都需要一個特定的探查連接埠。 例如，如果負載平衡器上有一個 IP 位址使用探查連接埠 59999，該負載平衡器上的任何其他 IP 位址就不能使用探查連接埠 59999。

* 如需有關負載平衡器限制的資訊，請參閱[網路限制 - Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits) 底下的「每個負載平衡器的私人前端 IP」。
* 如需有關可用性群組限制的資訊，請參閱[限制 (可用性群組)](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG)。

下列指令碼會將新的 IP 位址新增至現有的負載平衡器。 ILB 會使用接聽程式埠作為負載平衡前端埠。 此連接埠可以是 SQL Server 正在接聽的連接埠。 就預設的 SQL Server 執行個體而言，連接埠是 1433。 可用性群組的負載平衡規則需要浮動 IP (伺服器直接回傳) 因此後端埠與前端埠相同。 請更新您環境的變數。 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>設定接聽程式

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中設定接聽程式連接埠

1. 啟動 SQL Server Management Studio，然後連接到主要複本。

1. 瀏覽至 [AlwaysOn 高可用性] > [可用性群組] > [可用性群組接聽程式]。 

1. 您現在應該會看到在容錯移轉叢集管理員中建立的接聽程式名稱。 以滑鼠右鍵按一下接聽程式名稱，然後選取 [屬性] 。

1. 在 [ **埠** ] 方塊中，使用您先前使用的 $EndpointPort 來指定可用性群組接聽程式的通訊埠編號 (1433 是預設) ，然後選取 **[確定]** 。

## <a name="test-the-connection-to-the-listener"></a>測試接聽程式的連線

若要測試連線︰

1. 使用遠端桌面通訊協定 (RDP) 連接到位於相同虛擬網路中，但未擁有複本的 SQL Server。 它可能是叢集中的其他 SQL Server。

1. 使用 **sqlcmd** 公用程式來測試連線。 例如，下列指令碼會透過接聽程式搭配 Windows 驗證，建立與主要複本的 **sqlcmd** 連線︰
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    如果接聽程式使用預設連接埠 (1433) 以外的連接埠，請在連接字串中指定該連接埠。 例如，下列 sqlcmd 命令會連線到位於連接埠 1435 的接聽程式︰ 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD 連線會自動連線到任何一個裝載主要複本的 SQL Server 執行個體。 

> [!NOTE]
> 確定您指定的連接埠在兩部 SQL Server 在防火牆上開啟。 這兩部伺服器需要您使用的 TCP 連接埠的輸入規則。 如需詳細資訊，請參閱[新增或編輯防火牆規則](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11))。 
> 

## <a name="guidelines-and-limitations"></a>指導方針和限制

請注意，下列關於 Azure 中使用內部負載平衡器之可用性群組接聽程式的指導方針：

* 使用內部負載平衡器時，您只會從相同的虛擬網路內存取接聽程式。

* 如果您要使用 Azure 網路安全性群組來限制存取，請確定允許的規則包括：
  - 後端 SQL Server VM IP 位址
  - AG 接聽程式的負載平衡器浮動 IP 位址
  - 叢集核心 IP 位址（如果適用）。

* 將標準負載平衡器與 Azure 儲存體用於雲端見證時，請建立服務端點。 如需詳細資訊，請參閱[授與虛擬網路存取權](../../../storage/common/storage-network-security.md?toc=%252fazure%252fvirtual-network%252ftoc.json#grant-access-from-a-virtual-network)。

## <a name="for-more-information"></a>取得詳細資訊

如需詳細資訊，請參閱[在 Azure VM 中手動設定 Always On 可用性群組](availability-group-manually-configure-tutorial.md)。

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

使用下列 PowerShell Cmdlet 來建立 Azure 虛擬機器的內部負載平衡器。

* [New-AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer) 會建立負載平衡器。 
* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig) 會建立負載平衡器的前端 IP 設定。 
* [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig) 會建立負載平衡器的規則設定。 
* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig) 會建立負載平衡器的後端位址集區設定。 
* [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig) 會建立負載平衡器的探查設定。
* [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer) 會從 Azure 資源群組中移除負載平衡器。