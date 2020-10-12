---
title: 將資料遭到外泄限制為 Azure 儲存體-Azure PowerShell
description: 在本文中，您將瞭解如何使用 Azure PowerShell，以虛擬網路服務端點原則限制和限制虛擬網路資料遭到外泄，以 Azure 儲存體資源。
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 1d4fcc280ba2e34d2fa81584846441ad6fe81431
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84708190"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>使用 Azure PowerShell 管理使用虛擬網路服務端點原則 Azure 儲存體帳戶的資料遭到外泄

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

虛擬網路服務端點原則可讓您從虛擬網路內的服務端點，將存取控制套用至 Azure 儲存體帳戶。 這是保護工作負載安全的關鍵，可管理允許的儲存體帳戶，以及允許資料遭到外泄的位置。
在本文中，您將學會如何：

* 建立虛擬網路。
* 新增子網，並啟用 Azure 儲存體的服務端點。
* 建立兩個 Azure 儲存體帳戶，並允許從上述建立的子網進行網路存取。
* 建立服務端點原則，只允許存取其中一個儲存體帳戶。
* 將虛擬機器 (VM) 部署至子網。
* 確認從子網存取允許的儲存體帳戶。
* 確認從子網拒絕存取非允許的儲存體帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-virtual-network"></a>建立虛擬網路

建立虛擬網路之前，您必須為虛擬網路以及在本文中建立的所有其他資源，建立資源群組。 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組。 下列範例會建立名為 myResourceGroup** 的資源群組： 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 myVirtualNetwork** 的虛擬網路，位址首碼為 10.0.0.0/16**。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>啟用服務端點

在虛擬網路中建立子網。 在此範例中，系統會建立名為「Private (私人)」** 的子網路，當中包含適用於 Microsoft.Storage 的服務端點**： 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>限制子網的網路存取

使用 [New->new-aznetworksecurityruleconfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)建立網路安全性群組安全性規則。 下列規則允許對指派給 Azure 儲存體服務之公用 IP 位址的輸出存取： 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

下列規則會拒絕對所有公用 IP 位址的存取。 上一個規則會因其具有較高優先順序而覆寫這項規則，從而允許對 Azure 儲存體之公用 IP 位址的存取。

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

下列規則允許從任何地方輸入子網路的遠端桌面通訊協定 (RDP) 流量。 允許遠端桌面對子網路的連線，因此您可以在稍後的步驟中確認對資源的網路存取。

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組。 下列範例會建立名為 myNsgPrivate** 的網路安全性群組。

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

使用[>new-azvirtualnetworksubnetconfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)將網路安全性群組關聯至*私人*子網，然後將子網設定寫入至虛擬網路。 下列範例會將 myNsgPrivate** 網路安全性群組與「私人」** 子網路建立關聯：

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>限制 Azure 儲存體帳戶的網路存取

如果資源是透過已針對服務端點啟用的 Azure 服務建立，則限制其網路存取的必要步驟會因為服務不同而有所差異。 請參閱個別服務的文件，以了解每個服務的特定步驟。 本文的其餘部分包含限制 Azure 儲存體帳戶網路存取的步驟 (以範例形式說明)。

### <a name="create-two-storage-accounts"></a>建立兩個儲存體帳戶

使用 [new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount)建立 Azure 儲存體帳戶。

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

建立儲存體帳戶之後，請使用 [>get-azstorageaccountkey](/powershell/module/az.storage/get-azstorageaccountkey)將儲存體帳戶的金鑰取出至變數中：

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

在稍後步驟中，會使用金鑰來建立檔案共用。 輸入 `$storageAcctKey` 並將值記下，因為當您在稍後步驟中將檔案共用對應至 VM 中的磁碟機時，也需要以手動方式將它輸入。

現在重複上述步驟來建立第二個儲存體帳戶。

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

此外，也請從這個帳戶取出儲存體帳戶金鑰，以便稍後用來建立檔案共用。

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>在每個儲存體帳戶中建立檔案共用

使用 [AzStorageCoNtext](/powershell/module/az.storage/new-AzStoragecontext)建立儲存體帳戶和金鑰的內容。 內容包含儲存體帳戶名稱和帳戶金鑰：

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

使用 [AzStorageShare](/powershell/module/az.storage/new-azstorageshare)建立檔案共用：

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>拒絕所有的儲存體帳戶網路存取

根據預設，儲存體帳戶會接受來自任何網路用戶端的網路連線。 若要限制對所選網路的存取，請將預設動作變更為「 *拒絕* 」（ [AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)）。 一旦網路存取遭到拒絕後，就無法從任何網路存取儲存體帳戶。

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>僅從 VNet 子網啟用網路存取

使用 [new-azvirtualnetwork](/powershell/module/az.network/get-azvirtualnetwork) 取出已建立的虛擬網路，然後使用 [>new-azvirtualnetworksubnetconfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)將私人子網物件取出至變數：

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

允許使用[AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)從*私人*子網存取儲存體帳戶的網路。

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>套用原則以允許存取有效的儲存體帳戶

若要確定虛擬網路中的使用者只能存取安全且允許的 Azure 儲存體帳戶，您可以使用定義中允許的儲存體帳戶清單來建立服務端點原則。 這項原則接著會套用至透過服務端點連線到儲存體的虛擬網路子網。

### <a name="create-a-service-endpoint-policy"></a>建立服務端點原則

此區段會使用允許的資源清單來存取服務端點，以建立原則定義

取得) 儲存體帳戶所允許的第一個 (的資源識別碼 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

建立原則定義以允許上述資源

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

使用上面建立的原則定義來建立服務端點原則

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>將服務端點原則與虛擬網路子網產生關聯

建立服務端點原則之後，您會將它與目標子網建立關聯，以 Azure 儲存體的服務端點設定。

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>驗證 Azure 儲存體帳戶的存取限制

### <a name="deploy-the-virtual-machine"></a>部署虛擬機器

若要測試對儲存體帳戶的網路存取，請在子網中部署 VM。

使用[new-azvm](/powershell/module/az.compute/new-azvm)在*私人*子網中建立虛擬機器。 執行接下來的命令時，系統會提示您輸入認證。 您輸入的值會設定為 VM 的使用者名稱和密碼。 `-AsJob` 選項會在背景建立 VM，以便您繼續進行下一步。

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

系統將傳回與下列範例輸出類似的輸出：

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>確認存取 *允許* 的儲存體帳戶

請使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 來傳回 VM 的公用 IP 位址。 以下範例會傳回 myVmPrivate** 虛擬機器的公用 IP 位址：

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

使用前一個命令傳回的公用 IP 位址來取代下列命令中的 `<publicIpAddress>`，然後輸入下列命令：

```powershell
mstsc /v:<publicIpAddress>
```

將會建立一個「遠端桌面通訊協定」(.rdp) 檔案，並下載至您的電腦。 開啟所下載的 rdp 檔案。 如果出現提示，請選取 [連接]。 輸入您在建立 VM 時指定的使用者名稱和密碼。 您可能需要選取 [其他選擇]****，然後選取 [使用不同的帳戶]****，以指定您在建立 VM 時輸入的認證。 選取 [確定]  。 您可能會在登入過程中收到憑證警告。 如果您收到警告，請選取 [是]**** 或 [繼續]**** 以繼續進行連線。

在 *myVmPrivate* VM 上，使用 PowerShell 將 Azure 檔案共用從允許的儲存體帳戶對應到磁片磁碟機 Z。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell 會傳回類似以下範例輸出的輸出：

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Azure 檔案共用已成功對應至 Z 磁碟機。

關閉 myVmPrivate 虛擬機器的遠端桌面工作階段。

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>確認拒絕存取 *非允許* 的儲存體帳戶

在相同的 *myVmPrivate* VM 上，嘗試將 Azure 檔案共用對應到磁片磁碟機 X。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

存取共用遭到拒絕，且您收到 `New-PSDrive : Access is denied` 錯誤。 因為儲存體帳戶 *notallowedaccount* 不在服務端點原則的允許資源清單中，所以拒絕存取。 

關閉 myVmPublic** VM 的遠端桌面工作階段。

## <a name="clean-up-resources"></a>清除資源

您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源：

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

在本文中，您已透過 Azure 虛擬網路服務端點將服務端點原則套用至 Azure 儲存體。 您已建立 Azure 儲存體帳戶，且僅限特定儲存體帳戶的網路存取權 (，因而拒絕其他) 自虛擬網路子網的其他儲存體帳戶。 若要深入瞭解服務端點原則，請參閱 [服務端點原則總覽](virtual-network-service-endpoint-policies-overview.md)。
