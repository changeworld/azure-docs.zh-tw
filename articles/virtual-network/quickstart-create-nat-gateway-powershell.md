---
title: 快速入門：建立 NAT 閘道 - Azure Powershell
titlesuffix: Azure Virtual Network NAT
description: 本快速入門說明如何使用 Azure PowerShell 建立 NAT 閘道。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1d5f8d6e0b2499bbecd32e7cb3fda2cd2cad4d19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202214"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 NAT 閘道

本快速入門說明如何使用 Azure 虛擬網路 NAT 服務。 您將建立 NAT 閘道，為 Azure 中的虛擬機器提供輸出連線能力。 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 完成本教學課程，或在本機執行命令。  如果您尚未使用 Azure Cloud Shell，請[立即登入](https://shell.azure.com)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 **eastus2** 位置建立名為 myResourceGroupNAT  的資源群組：

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>建立 NAT 閘道

NAT 閘道的公用 IP 選項如下：

* **公用 IP 位址**
* **公用 IP 前置詞**

兩者都可以搭配 NAT 閘道使用。

我們將在此案例中新增公用 IP 位址和公用 IP 前置詞進行示範。

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

若要存取網際網路，您需要有一個或多個適用於 NAT 閘道的公用 IP 位址。 使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myPublicIP** 的公用 IP 位址資源。 此命令的結果將會儲存在名為 **$publicIP** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>建立公用 IP 前置詞

使用 [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myPublicIPprefix** 的公用 IP 前置詞資源。  此命令的結果將會儲存在名為 **$publicIPPrefix** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>建立 NAT 閘道資源

本節將詳細說明如何使用 NAT 閘道資源建立及設定下列 NAT 服務元件：
  - 公用 IP 集區和公用 IP 前置詞，將用於 NAT 閘道資源所轉譯的輸出流量。
  - 將閒置逾時時間從預設的 4 分鐘變更為 10 分鐘。

使用 [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) 建立全域 Azure NAT 閘道。 此命令的結果會建立名為 **myNATgateway** 的閘道資源，並且使用公用 IP 位址 **myPublicIP** 和公用 IP 前置詞 **myPublicIPprefix**。 將閒置逾時時間設定為 10 分鐘。  此命令的結果將會儲存在名為 **$natGateway** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

此時，NAT 閘道可正常運作，而現在唯一缺少的就是設定虛擬網路的哪些子網路應使用此閘道。

## <a name="configure-virtual-network"></a>設定虛擬網路

建立虛擬網路，並將子網路與閘道建立關聯。

使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)，在 **myResourceGroup** 中建立名為 **myVnet** 的虛擬網路，並使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 建立名為 **mySubnet** 的子網路。 虛擬網路的 IP 位址空間為 **192.168.0.0/16**。 虛擬網路內的子網路為 **192.168.0.0/24**。  命令的結果將會儲存在名為 **$subnet** 和 **$vnet** 的變數中，以供稍後使用。

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

所有連結到網際網路目的地的輸出流量現在都會使用 NAT 服務。  您不需要設定 UDR。

## <a name="create-a-vm-to-use-the-nat-service"></a>建立 VM 以使用 NAT 服務

我們現在會建立 VM 以使用 NAT 服務。  此 VM 具有公用 IP，可作為執行個體層級的公用 IP，讓您能夠存取 VM。  NAT 服務可感知流量方向，並將取代您子網路中的預設網際網路目的地。 VM 的公用 IP 位址不會用於輸出連線。

### <a name="create-public-ip-for-source-vm"></a>建立來源 VM 的公用 IP

我們會建立用來存取 VM 的公用 IP。  使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myPublicIPVM** 的公用 IP 位址資源。  此命令的結果將會儲存在名為 **$publicIpVM** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>建立 NSG 並公開 VM 的 SSH 端點

標準公用 IP 位址屬於「預設保護」，因此我們需要建立 NSG 來允許 SSH 的輸入存取。 使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) 建立名為 **myNSG** 的 NSG 資源。 使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest)，為 **myResourceGroupNAT** 中名為 **ssh** 的 SSH 存取建立 NSG 規則。  此命令的結果將會儲存在名為 **$nsg** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>建立 VM 的 NIC

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) 建立名為 **myNic** 的網路介面。 此命令會將公用 IP 位址和網路安全性群組建立關聯。 此命令的結果將會儲存在名為 **$nic** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>建立 VM

#### <a name="create-ssh-key-pair"></a>建立 SSH 金鑰組

您必須要有 SSH 金鑰組，才能完成本快速入門的操作。 如果您已經擁有 SSH 金鑰組，則可略過此步驟。

使用 ssh-keygen 來建立 SSH 金鑰組。

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
如需如何建立 SSH 金鑰的詳細資訊 (包括 PuTTy 的用法)，請參閱[對 Windows 使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

如果您使用 Cloud Shell 建立 SSH 金鑰組，金鑰組會儲存在容器映像中。 此[儲存體帳戶會自動建立](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。 在您取得金鑰之前，請勿刪除儲存體帳戶或其中的檔案共用。

#### <a name="create-vm-configuration"></a>建立 VM 組態

若要在 PowerShell 中建立 VM，您可建立具有以下設定的組態：要使用的映像、大小和驗證選項。 使用此組態來建置 VM。

定義 SSH 認證、OS 資訊與 VM 大小。 在此範例中，SSH 金鑰會儲存在 ~/.ssh/id_rsa.pub。

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
使用 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) 結合組態定義在 **myResourceGroupNAT** 中建立名為 **myVM** 的 VM。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

等待 VM 準備部署，然後繼續進行其餘的步驟。

## <a name="discover-the-ip-address-of-the-vm"></a>探索 VM 的 IP 位址

首先，我們需要探索您所建立 VM 的 IP 位址。 若要取得 VM 的公用 IP 位址，請使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)。 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便將其用來存取 VM。

### <a name="sign-in-to-vm"></a>登入 VM

在上一個作業中，SSH 認證應該已儲存在您的 Cloud Shell 中。  在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。

```bash
ssh azureuser@<ip-address-destination>
```

您現在已經準備好使用 NAT 服務。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) 命令來移除資源群組和其中所有資源。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 NAT 閘道以及要使用該閘道的 VM。 

接著請檢閱 Azure 監視器中的計量，以了解 NAT 服務的運作。 診斷可用 SNAT 連接埠的資源耗盡相關問題。  新增額外的公用 IP 位址資源或公用 IP 前置詞資源 (或兩者)，都可以解決 SNAT 連接埠的資源耗盡問題。


- 了解 [Azure 虛擬網路 NAT](./nat-overview.md)
- 深入了解 [NAT 閘道資源](./nat-gateway-resource.md)。
- [使用 Azure CLI 部署 NAT 閘道資源](./quickstart-create-nat-gateway-cli.md)的快速入門。
- [使用 Azure PowerShell 部署 NAT 閘道資源](./quickstart-create-nat-gateway-powershell.md)的快速入門。
- [使用 Azure 入口網站部署 NAT 閘道資源](./quickstart-create-nat-gateway-portal.md)的快速入門。
> [!div class="nextstepaction"]


