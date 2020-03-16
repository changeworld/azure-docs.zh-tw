---
title: 教學課程：建立和測試 NAT 閘道 - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: 本教學課程將說明如何使用 Azure PowerShell 建立 NAT 閘道，並測試 NAT 服務
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: e0fb78be363795bbb09fb0f14bd018fceea34a48
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897889"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>教學課程：使用 Azure PowerShell 建立 NAT 閘道並測試 NAT 服務

在本教學課程中，您將建立 NAT 閘道，為 Azure 中的虛擬機器提供輸出連線能力。 若要測試 NAT 閘道，請部署來源和目的地虛擬機器。 您將會藉由對公用 IP 位址進行輸出連線，來測試 NAT 閘道。 這些連線會從來源虛擬機器連結到目的地虛擬機器。 為了簡單起見，本教學課程會將來源和目的地部署在相同資源群組中的兩個不同虛擬網路中。

>[!NOTE] 
>Azure 虛擬網路 NAT 目前以公開預覽形式提供，可在一組有限的[區域](./nat-overview.md#region-availability)中取得。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 完成本教學課程，或在本機執行個別命令。  如果您從未使用過 Azure Cloud Shell，應該[立即登入](https://shell.azure.com)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 **eastus2** 位置建立名為 myResourceGroupNAT  的資源群組：


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>建立 NAT 閘道

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

若要存取網際網路，您需要有一個或多個適用於 NAT 閘道的公用 IP 位址。 使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myPublicIPsource** 的公用 IP 位址資源。 此命令的結果將會儲存在名為 **$publicIPsource** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>建立公用 IP 首碼

 使用 [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myPublicIPprefixsource** 的公用 IP 首碼資源。  此命令的結果將會儲存在名為 **$publicIPPrefixsource** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>建立 NAT 閘道資源

本節將詳細說明如何使用 NAT 閘道資源建立及設定下列 NAT 服務元件：
  - 公用 IP 集區和公用 IP 前置詞，將用於 NAT 閘道資源所轉譯的輸出流量。
  - 將閒置逾時時間從預設的 4 分鐘變更為 10 分鐘。

使用 [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) 建立全域 Azure NAT 閘道。 此命令的結果會建立名為 **myNATgateway** 的閘道資源，並且使用公用 IP 位址 **myPublicIPsource** 和公用 IP 首碼 **myPublicIPprefixsource**。 將閒置逾時時間設定為 10 分鐘。  此命令的結果將會儲存在名為 **$natGateway** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

此時，NAT 閘道可正常運作，而現在唯一缺少的就是設定虛擬網路的哪些子網路應使用此閘道。

## <a name="prepare-the-source-for-outbound-traffic"></a>準備輸出流量的來源

我們會引導您完成完整的測試環境設定。 您將使用開放原始碼工具來設定測試，以驗證 NAT 閘道。 我們將從來源開始，來源會使用我們先前建立的 NAT 閘道。

### <a name="configure-virtual-network-for-source"></a>設定來源的虛擬網路

建立虛擬網路，並將子網路與閘道建立關聯。

使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myVnetsource** 的虛擬網路，並使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 建立名為 **mySubnetsource**的子網路。 虛擬網路的 IP 位址空間為 **192.168.0.0/16**。 虛擬網路內的子網路為 **192.168.0.0/24**。  命令的結果將會儲存在名為 **$subnetsource** 和 **$vnetsource** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

所有連結到網際網路目的地的輸出流量現在都會使用 NAT 服務。  您不需要設定 UDR。

在開始測試 NAT 閘道之前，我們必須先建立來源 VM。  我們會將公用 IP 位址資源指派為執行個體層級的公用 IP，以從外部存取此 VM。 此位址只會用來存取此 VM 以進行測試。  我們將示範如何讓 NAT 服務的優先順序高於其他輸出選項。

您也可以在不使用公用 IP 的情況下建立此 VM，並建立另一個 VM 作為 Jumpbox，而不需要公用 IP 來進行練習。

### <a name="create-public-ip-for-source-vm"></a>建立來源 VM 的公用 IP

我們會建立用來存取 VM 的公用 IP。  使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myPublicIPVM** 的公用 IP 位址資源。  此命令的結果將會儲存在名為 **$publicIpsourceVM** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>建立 NSG 並公開 VM 的 SSH 端點

因為標準公用 IP 位址屬於「預設保護」，所以我們會建立 NSG 來允許 SSH 的輸入存取。 NAT 服務可察覺到流程方向。 如果 NAT 閘道設定在相同子網路上，則此 NSG 將不會用於輸出。 使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) 建立名為 **myNSGsource** 的 NSG 資源。 使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest)，為 **myResourceGroupNAT** 中名為 **ssh** 的 SSH 存取建立 NSG 規則。 此命令的結果將會儲存在名為 **$nsgsource** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>建立來源 VM 的 NIC

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) 建立名為 **myNicsource** 的網路介面。 此命令會將公用 IP 位址和網路安全性群組建立關聯。 此命令的結果將會儲存在名為 **$nicsource** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>建立來源 VM

#### <a name="create-ssh-key-pair"></a>建立 SSH 金鑰組

您必須要有 SSH 金鑰組，才能完成本快速入門的操作。 如果您已經擁有 SSH 金鑰組，則可略過此步驟。

使用 ssh-keygen 來建立 SSH 金鑰組。

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
如需如何建立 SSH 金鑰的詳細資訊 (包括 PuTTy 的用法)，請參閱[對 Windows 使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

如果您使用 Cloud Shell 建立 SSH 金鑰組，金鑰組會儲存在容器映像中。 此[儲存體帳戶會自動建立](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。 在您取得金鑰之前，請勿刪除儲存體帳戶或其中的檔案共用。

#### <a name="create-vm-configuration"></a>建立 VM 組態

若要在 PowerShell 中建立 VM，您可以建立具有以下設定的組態：要使用的映像、大小和驗證選項等。 然後使用此組態來建置 VM。

定義 SSH 認證、OS 資訊與 VM 大小。 在此範例中，SSH 金鑰會儲存在 ~/.ssh/id_rsa.pub。

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
使用 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) 結合組態定義在 **myResourceGroupNAT** 中建立名為 **myVMsource** 的 VM。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

雖然命令會立即傳回，但可能需要幾分鐘的時間來部署 VM。

## <a name="prepare-destination-for-outbound-traffic"></a>準備輸出流量的目的地

我們現在會針對 NAT 服務所轉譯的輸出流量建立目的地，以便您進行測試。

### <a name="configure-virtual-network-for-destination"></a>設定目的地的虛擬網路

我們需要建立將放置目的地虛擬機器的虛擬網路。  這些命令與來源 VM 的步驟相同。 我們作了些微變更來公開目的地端點。

使用 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myVnetdestination** 的虛擬網路，並使用 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 建立名為 **mySubnetdestination**的子網路。 虛擬網路的 IP 位址空間為 **192.168.0.0/16**。 虛擬網路內的子網路為 **192.168.0.0/24**。  命令的結果將會儲存在名為 **$subnetdestination** 和 **$vnetdestination** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>建立目的地 VM 的公用 IP

我們會建立用來存取目的地 VM 的公用 IP。  使用 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) 在 **myResourceGroupNAT** 中建立名為 **myPublicIPdestinationVM** 的公用 IP 位址資源。  此命令的結果將會儲存在名為 **$publicIpdestinationVM** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>建立 NSG 並公開 VM 的 SSH 端點和 HTTP 端點

標準公用 IP 位址屬於「預設保護」，因此我們會建立 NSG 來允許 SSH 的輸入存取。 使用 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) 建立名為 **myNSGdestination** 的 NSG 資源。 使用 [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest)，為名為 **ssh** 的 SSH 存取建立 NSG 規則。  使用 [New-aznetworksecurityruleconfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) 為 HTTP 存取建立名為 **http** 的 NSG 規則。 這兩個規則都會建立在 **myResourceGroupNAT** 中。 此命令的結果將會儲存在名為 **$nsgdestination** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>建立目的地 VM 的 NIC

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) 建立名為 **myNicdestination** 的網路介面。 此命令會將公用 IP 位址和網路安全性群組建立關聯。 此命令的結果將會儲存在名為 **$nicdestination** 的變數中，以供稍後使用。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>建立目的地 VM

#### <a name="create-vm-configuration"></a>建立 VM 組態

若要在 PowerShell 中建立 VM，您可以建立具有以下設定的組態：要使用的映像、大小和驗證選項等。 然後使用此組態來建置 VM。

定義 SSH 認證、OS 資訊與 VM 大小。 在此範例中，SSH 金鑰會儲存在 ~/.ssh/id_rsa.pub。

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
使用 [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) 結合組態定義在 **myResourceGroupNAT** 中建立名為 **myVMdestination** 的 VM。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

雖然命令會立即傳回，但可能需要幾分鐘的時間來部署 VM。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>在目的地 VM 上準備 Web 伺服器和測試承載

首先，我們需要探索目的地 VM 的 IP 位址。  若要取得 VM 的公用 IP 位址，請使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)。 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便在後續步驟中使用。 指出這是目的地虛擬機器。

### <a name="sign-in-to-destination-vm"></a>登入目的地 VM

在上一個作業中，SSH 認證應該已儲存在您的 Cloud Shell 中。  在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。 

```bash
ssh azureuser@<ip-address-destination>
```

當您登入之後，請複製並貼上下列命令。  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

這些命令會更新您的虛擬機器、安裝 nginx，並建立 100 Kb 的檔案。 系統會使用 NAT 服務從來源 VM 擷取此檔案。

關閉目的地 VM 的 SSH 工作階段。

## <a name="prepare-test-on-source-vm"></a>在來源 VM 上準備測試

首先，我們需要探索來源 VM 的 IP 位址。  若要取得 VM 的公用 IP 位址，請使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)。 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便在後續步驟中使用。 指出這是來源虛擬機器。

### <a name="log-into-source-vm"></a>登入來源 VM

同樣地，SSH 認證會儲存在 Cloud Shell 中。 在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com) 的新索引標籤。  使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。 

```bash
ssh azureuser@<ip-address-source>
```

複製並貼上下列命令，以準備測試 NAT 服務。

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

這些命令會更新您的虛擬機器、安裝 go、從 GitHub 安裝 [hey](https://github.com/rakyll/hey)，以及更新您的殼層環境。

您現在已經準備好測試 NAT 服務。

## <a name="validate-nat-service"></a>驗證 NAT 服務

登入來源 VM 時，您可以使用 **curl** 和 **hey** 來產生目的地 IP 位址的要求。

使用 curl 來擷取 100 KB 的檔案。  以您先前複製的目的地 IP 位址取代下列範例中的 **\<ip-address-destination>** 。  **--output** 參數表示將會捨棄擷取的檔案。

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

您也可以使用 **hey** 來產生一系列的要求。 同樣地，使用您先前複製的目的地 IP 位址來取代 **\<ip-address-destination>** 。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

此命令將會產生 100 個要求 (一次 10 個)，並將逾期時間設為 30 秒。 TCP 連線不會重複使用。  每個要求都會擷取 100 KB。  在回合結束時，**hey** 會回報 NAT 服務執行效能的相關統計資料。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) 命令來移除資源群組和其中所有資源。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立 NAT 閘道、建立來源和目的地 VM，然後測試了 NAT 閘道。

接著請檢閱 Azure 監視器中的計量，以了解 NAT 服務的運作。 診斷可用 SNAT 連接埠的資源耗盡相關問題。  新增額外的公用 IP 位址資源或公用 IP 前置詞資源 (或兩者)，都可以輕鬆地解決 SNAT 連接埠的資源耗盡問題。

- 了解[虛擬網路 NAT](./nat-overview.md)
- 深入了解 [NAT 閘道資源](./nat-gateway-resource.md)。
- [使用 Azure CLI 部署 NAT 閘道資源](./quickstart-create-nat-gateway-cli.md)的快速入門。
- [使用 Azure PowerShell 部署 NAT 閘道資源](./quickstart-create-nat-gateway-powershell.md)的快速入門。
- [使用 Azure 入口網站部署 NAT 閘道資源](./quickstart-create-nat-gateway-portal.md)的快速入門。
- [提供有關公開預覽版的意見反應](https://aka.ms/natfeedback)。

> [!div class="nextstepaction"]

