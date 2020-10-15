---
title: 教學課程 - 建立和管理適用於 Windows VM 的 Azure 虛擬網路
description: 在本教學課程中，了解如何使用 Azure PowerShell 來建立及管理 Windows 虛擬機器的 Azure 虛擬網路
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 84da38fe71446c54f17c4d4329c7294c5e5176d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87800189"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>教學課程：使用 Azure PowerShell 來建立及管理 Windows 虛擬機器的 Azure 虛擬網路

Azure 虛擬機器會使用 Azure 網路進行內部和外部的網路通訊。 本教學課程會逐步部署兩部虛擬機器 (VM)，並設定這兩部 VM 的 Azure 網路功能。 本教學課程中的範例假設 VM 已裝載 Web 應用程式與資料庫後端，不過應用程式的部署不在本教學課程範圍中。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路和子網路
> * 建立公用 IP 位址
> * 建立前端 VM
> * 保護網路流量
> * 建立後端 VM


## <a name="vm-networking-overview"></a>VM 網路概觀

Azure 虛擬網路可以讓虛擬機器、網際網路與其他 Azure 服務 (例如 Azure SQL Database) 之間的網路連線更安全。 虛擬網路會被分割成邏輯區段，稱為子網路。 子網路是用來控制網路流量，並作為安全性界限。 在部署 VM 時，通常會包含連結至子網路的虛擬網路介面。

完成本教學課程時，您可以看到這些建立的資源：

![具有兩個子網路的虛擬網路](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM 彼此進行通訊以及與網際網路進行通訊使用的虛擬網路。
- *myFrontendSubnet* - 前端資源所使用之 *myVNet* 中的子網路。
- *myPublicIPAddress* - 從網際網路存取 *myFrontendVM* 所使用的公用 IP 位址。
- *myFrontendNic* - *myFrontendVM* 與 *myBackendVM* 進行通訊所使用的網路介面。
- *myFrontendVM* - VM 在網際網路和 *myBackendVM* 之間進行通訊所使用的 VM。
- *myBackendNSG* - 控制 *myFrontendVM* 和 *myBackendVM* 之間通訊的網路安全性群組。
- *myBackendSubnet* - 與 *myBackendNSG* 相關聯且由後端資源所使用的子網路。
- *myBackendNic* - *myBackendVM* 與 *myFrontendVM* 進行通訊所使用的網路介面。
- *myBackendVM* - 使用連接埠 1433 與 *myFrontendVM* 進行通訊的 VM。


## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。


## <a name="create-subnet"></a>建立子網路 

在本教學課程中，會建立一個具有兩個子網路的虛擬網路。 一個是裝載 Web 應用程式的前端子網路，一個是裝載資料庫伺服器的後端子網路。

建立虛擬網路之前，請先使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 建立資源群組。 下列範例會在 EastUS 位置建立名為 myRGNetwork 的資源群組。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 建立一個名為 *myFrontendSubnet* 的子網路設定：

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

此外，建立一個名為 *myBackendSubnet* 的子網路設定：

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>建立虛擬網路

使用 *myFrontendSubnet* 和 *myBackendSubnet* 搭配 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)，建立名為 *myVNet* 的 VNET：

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

此時，已建立一個網路並分割成兩個子網路，一個用於前端的服務，另一個用於後端服務。 在下一節會建立虛擬機器，並將其連線到這些子網路。

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

公用 IP 位址讓您能夠存取網際網路上的 Azure 資源。 公用 IP 位址的配置方法可以設定為動態或靜態。 預設是以動態方式配置公用 IP 位址。 VM 解除配置時，就會釋放動態 IP 位址。 在任何包括 VM 解除配置的作業期間，這個行為會導致 IP 位址變更。

配置方法可以設定為靜態，這可確保即使在已取消配置的狀態下，依然將 IP 位址指派給 VM。 如果您使用靜態 IP 位址，則無法指定 IP 位址本身， 而是從可用位址集區配置一個給它。

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立一個名為 *myPublicIPAddress* 的公用 IP 位址：

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

您可以將 -AllocationMethod 參數變更為 `Static`，以指派靜態公用 IP 位址。

## <a name="create-a-front-end-vm"></a>建立前端 VM

若要讓 VM 在虛擬網路中進行通訊，它需要一個虛擬網路介面 (NIC)。 使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 建立 NIC：

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) \(英文\) 設定 VM 上系統管理員帳戶所需的使用者名稱和密碼。 您需要執行其他步驟以使用這些認證來連線至 VM：

```azurepowershell-interactive
$cred = Get-Credential
```

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 VM。

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>保護網路流量

網路安全性群組 (NSG) 包含安全性規則的清單，可允許或拒絕已連線至 Azure 虛擬網路 (VNet) 之資源的網路流量。 NSG 可與子網路或個別網路介面建立關聯。 與網路介面相關聯的 NSG 只會套用到相關聯的 VM。 當 NSG 與子網路相關聯時，系統會將規則套用至已連線至子網路的所有資源。

### <a name="network-security-group-rules"></a>網路安全性群組規則

NSG 規則定義允許或拒絕流量的網路連接埠。 規則可以包含來源和目的地 IP 位址範圍，以便控制特定系統或子網路之間的流量。 NSG 規則也包含優先順序 (介於 1 和 4096)。 系統會依照優先順序評估規則。 優先順序 100 的規則會比優先順序 200 的規則優先評估。

所有 NSG 都包含一組預設規則。 預設規則無法刪除，但因為其會指派為最低優先順序，因此可以由您所建立的規則覆寫預設規則。

- **虛擬網路** - 虛擬網路中的流量起始和結束同時允許輸入和輸出方向。
- **網際網路** - 允許輸出流量，但會封鎖輸入流量。
- **負載平衡器** - 允許 Azure 的負載平衡器探查 VM 和角色執行個體的健康狀態。 如果您不使用負載平衡的集合，則可以覆寫此規則。

### <a name="create-network-security-groups"></a>建立網路安全性群組

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立一個名為 *myFrontendNSGRule* 的輸入規則，以允許 *myFrontendVM* 上的傳入網路流量：

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

您可以為後端子網路建立 NSG，以僅允許從 myFrontendVM 傳送內部流量給 myBackendVM。 下列範例會建立一個名為 *myBackendNSGRule* 的 NSG 規則：

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 新增一個名為 *myFrontendNSG* 的網路安全性群組：

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

使用 New-AzNetworkSecurityGroup 新增一個名為 *myBackendNSG* 的網路安全性群組：

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

將網路安全性群組新增至子網路：

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>建立後端 VM

在本教學課程中，建立後端 VM 的最簡單方式是使用 SQL Server 映像。 本教學課程只會建立具有資料庫伺服器的 VM，而不會提供有關存取該資料庫的資訊。

建立 myBackendNic：

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

使用 Get-Credential 來設定 VM 上系統管理員帳戶所需的使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

建立 *myBackendVM*。

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

此範例中的映像已安裝 SQL Server，但不會在本教學課程中使用。 包含它是為了示範如何設定一個 VM 來處理 Web 流量、一個 VM 來處理資料庫管理。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立並保護與虛擬機器相關的 Azure 網路。 

> [!div class="checklist"]
> * 建立虛擬網路和子網路
> * 建立公用 IP 位址
> * 建立前端 VM
> * 保護網路流量
> * 建立後端 VM

請前進到下一個教學課程，了解如何使用 Azure 備份監視保護虛擬機器上的資料。

> [!div class="nextstepaction"]
> [備份 Azure 中的 Windows 虛擬機器](./tutorial-backup-vms.md)
