---
title: 快速入門 - 使用 Azure PowerShell 建立 Linux VM
description: 在本快速入門中，您會了解如何使用 Azure PowerShell 來建立 Linux 虛擬機器
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 397fac7609d9527165a1a0a35215a2e2bac23c6d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81759211"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>快速入門：使用 PowerShell 在 Azure 中建立 Linux 虛擬機器

Azure PowerShell 模組用於從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 本快速入門說明如何使用 Azure PowerShell 模組，在 Azure 中部署 Linux 虛擬機器 (VM)。 本快速入門會從 Canonical 使用 Ubuntu 16.04 LTS 市集映像。 為了查看作用中的 VM，您還會以 SSH 連線至 VM，並安裝 NGINX 網頁伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="create-ssh-key-pair"></a>建立 SSH 金鑰組

您必須要有 SSH 金鑰組，才能完成本快速入門的操作。 如果您已經擁有 SSH 金鑰組，則可略過此步驟。

開啟 Bash 殼層，然後使用 [ssh-keygen](https://www.ssh.com/ssh/keygen/) 來建立 SSH 金鑰組。 如果本機電腦上沒有 Bash 殼層，則可以使用 [Azure Cloud Shell](https://shell.azure.com/bash)。  

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

如需如何建立 SSH 金鑰的詳細資訊 (包括 PuTTy 的用法)，請參閱[對 Windows 使用 SSH 金鑰](ssh-from-windows.md)。

如果您使用 Cloud Shell 建立 SSH 金鑰組，該金鑰組會儲存在容器映像中，而這映像會位在 [Cloud Shell 所自動建立的儲存體帳戶](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)內。 在擷取到金鑰前請勿刪除儲存體帳戶或其中的檔案共用，否則會無法存取 VM。 

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器：

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>建立虛擬網路資源

建立虛擬網路、子網路和公用 IP 位址。 這些資源可用來提供 VM 的網路連線能力，並可將它連線到網際網路：

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

建立 Azure 網路安全性群組及流量規則。 網路安全性群組可使用輸入和輸出規則來保護 VM。 下列範例會針對允許 SSH 連線的 TCP 通訊埠 22 建立輸入規則。 為了允許傳入的網路流量，還會針對 TCP 通訊埠 80 建立輸入規則。

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) 建立虛擬網路介面卡 (NIC)。 虛擬 NIC 會將 VM 連線至子網路、網路安全性群組和公用 IP 位址。

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

若要在 PowerShell 中建立 VM，您可以建立具有以下設定的組態：要使用的映像、大小和驗證選項等。 然後使用此組態來建置 VM。

定義 SSH 認證、OS 資訊與 VM 大小。 在此範例中，SSH 金鑰會儲存在 `~/.ssh/id_rsa.pub`。 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

現在，使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 合併上述要建立的組態定義：

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

可能需要幾分鐘的時間才能部署好 VM。 部署完成時，請前往下一節。

## <a name="connect-to-the-vm"></a>連接至 VM

使用公用 IP 位址對 VM 建立 SSH 連線。 若要查看 VM 的公用 IP 位址，請使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) Cmdlet：

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

使用和您建立 SSH 金鑰組時所用的同一個 Bash 殼層 (例如 [Azure Cloud Shell](https://shell.azure.com/bash) 或本機 Bash 殼層)，將 SSH 連線命令貼到殼層中，以建立 SSH 工作階段。

```bash
ssh azureuser@10.111.12.123
```

出現提示時，登入使用者名稱為 azureuser  。 如果 SSH 金鑰搭配使用複雜密碼，則需要在提示時輸入該複雜密碼。


## <a name="install-nginx"></a>安裝 NGINX

若要查看作用中的 VM，請安裝 NGINX 網頁伺服器。 從 SSH 工作階段更新套件來源，然後安裝最新的 NGINX 套件。

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

完成時，輸入 `exit` 來離開 SSH 工作階段。


## <a name="view-the-web-server-in-action"></a>檢視作用中的網頁伺服器

使用所選的網頁瀏覽器來查看預設 NGINX 歡迎使用頁面。 輸入 VM 的公用 IP 位址作為網址。 您可以在 VM 的 [概觀] 頁面找到公用 IP 位址，也可以在您稍早使用的 SSH 連接字串中找到。

![NGINX 預設的歡迎頁面](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、建立網路安全性群組和規則，並已安裝基本的 Web 伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Linux VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Linux 虛擬機器教學課程](./tutorial-manage-vm.md)
