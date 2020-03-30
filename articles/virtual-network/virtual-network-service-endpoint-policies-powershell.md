---
title: 將資料外泄限制為 Azure 存儲 - Azure PowerShell
description: 在本文中，您將瞭解如何使用 Azure PowerShell 使用虛擬網路服務終結點策略限制和限制虛擬網路資料向 Azure 存儲資源滲透。
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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78253022"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>使用 Azure PowerShell 使用虛擬網路服務終結點策略管理資料滲出到 Azure 存儲帳戶

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

虛擬網路服務終結點策略使您能夠通過服務終結點從虛擬網路內對 Azure 存儲帳戶應用存取控制。 這是保護工作負載、管理允許哪些存儲帳戶以及允許資料滲漏的位置的關鍵。
在本文中，您將學會如何：

* 建立虛擬網路。
* 添加子網並為 Azure 存儲啟用服務終結點。
* 創建兩個 Azure 存儲帳戶，並允許從上面創建的子網進行網路訪問。
* 創建服務終結點策略，僅允許訪問其中一個存儲帳戶。
* 將虛擬機器 （VM） 部署到子網。
* 確認從子網對允許的存儲帳戶的訪問。
* 確認從子網對不允許的存儲帳戶的訪問被拒絕。

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

在虛擬網路中創建子網。 在此範例中，系統會建立名為「Private (私人)」** 的子網路，當中包含適用於 Microsoft.Storage 的服務端點**： 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>限制子網的網路訪問

使用[New-AzNetwork 安全規則配置創建](/powershell/module/az.network/new-aznetworksecurityruleconfig)網路安全性群組安全規則。 下列規則允許對指派給 Azure 儲存體服務之公用 IP 位址的輸出存取： 

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

將網路安全性群組與*專用*子[網與集-Az虛擬網路子系統配置關聯](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)，然後將子網配置寫入虛擬網路。 下列範例會將 myNsgPrivate** 網路安全性群組與「私人」** 子網路建立關聯：

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>限制對 Azure 存儲帳戶的網路訪問

如果資源是透過已針對服務端點啟用的 Azure 服務建立，則限制其網路存取的必要步驟會因為服務不同而有所差異。 請參閱個別服務的文件，以了解每個服務的特定步驟。 本文的其餘部分包含限制 Azure 儲存體帳戶網路存取的步驟 (以範例形式說明)。

### <a name="create-two-storage-accounts"></a>創建兩個存儲帳戶

使用[新 Az 存儲帳戶](/powershell/module/az.storage/new-azstorageaccount)創建 Azure 存儲帳戶。

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

創建存儲帳戶後，將存儲帳戶的金鑰檢索到具有[Get-AzStorageAccountKey 的](/powershell/module/az.storage/get-azstorageaccountkey)變數中：

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

在稍後步驟中，會使用金鑰來建立檔案共用。 輸入 `$storageAcctKey` 並將值記下，因為當您在稍後步驟中將檔案共用對應至 VM 中的磁碟機時，也需要以手動方式將它輸入。

現在重複上述步驟以創建第二個存儲帳戶。

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

此外，還可以從此帳戶中檢索存儲帳戶金鑰，以便以後使用來創建檔共用。

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>在每個存儲帳戶中創建檔共用

使用[New-AzStorageCoNtext](/powershell/module/az.storage/new-AzStoragecontext)為存儲帳戶創建上下文和金鑰。 內容包含儲存體帳戶名稱和帳戶金鑰：

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

使用[New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare)創建檔共用 ：

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>拒絕對存儲帳戶的所有網路訪問

根據預設，儲存體帳戶會接受來自任何網路用戶端的網路連線。 要限制對所選網路的訪問，請將預設操作更改為 *"* 使用[更新-AzStorageAccount網路規則集](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)拒絕"。 一旦網路存取遭到拒絕後，就無法從任何網路存取儲存體帳戶。

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

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>僅從 VNet 子網啟用網路訪問

使用[Get-Az虛擬網路](/powershell/module/az.network/get-azvirtualnetwork)檢索創建的虛擬網路，然後將專用子網物件檢索到具有[Get-AzVirtualNetwork SubnetConfig 的變數中](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)：

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

允許使用[Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)從*專用*子網訪問存儲帳戶。

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

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>應用策略以允許訪問有效的存儲帳戶

為了確保虛擬網路中的使用者只能訪問安全且允許的 Azure 存儲帳戶，可以在定義中創建包含允許存儲帳戶清單的服務終結點策略。 然後，此策略將應用於通過服務終結點連接到存儲的虛擬網路子網。

### <a name="create-a-service-endpoint-policy"></a>建立服務端點原則

本節創建策略定義，其中列出了允許的資源，以便通過服務終結點進行訪問

檢索第一個（允許的）存儲帳戶的資源識別碼 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

創建策略定義以允許上述資源

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

使用上面創建的策略定義創建服務終結點策略

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>將服務終結點策略關聯到虛擬網路子網

創建服務終結點策略後，您將將其與目標子網與 Azure 存儲的服務終結點配置相關聯。

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>驗證對 Azure 存儲帳戶的訪問限制

### <a name="deploy-the-virtual-machine"></a>部署虛擬機器

要測試對存儲帳戶的網路訪問，請將 VM 部署在子網中。

使用[New-AzVM](/powershell/module/az.compute/new-azvm)在*專用*子網中創建虛擬機器。 執行接下來的命令時，系統會提示您輸入認證。 您輸入的值會設定為 VM 的使用者名稱和密碼。 `-AsJob` 選項會在背景建立 VM，以便您繼續進行下一步。

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

### <a name="confirm-access-to-the-allowed-storage-account"></a>確認對*允許*的存儲帳戶的訪問

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

將會建立一個「遠端桌面通訊協定」(.rdp) 檔案，並下載至您的電腦。 開啟所下載的 rdp 檔案。 如果出現提示，請選取 [連接]****。 輸入您在建立 VM 時指定的使用者名稱和密碼。 您可能需要選取 [其他選擇]****，然後選取 [使用不同的帳戶]****，以指定您在建立 VM 時輸入的認證。 選取 [確定]****。 您可能會在登入過程中收到憑證警告。 如果您收到警告，請選取 [是]**** 或 [繼續]**** 以繼續進行連線。

在*myVmPrivate* VM 上，從允許的存儲帳戶映射 Azure 檔共用，使用 PowerShell 驅動 Z。 

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

關閉 myVmPrivate** 虛擬機器的遠端桌面工作階段。

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>確認對*不允許的*存儲帳戶的訪問被拒絕

在同一*myVmPrivate* VM 上，嘗試將 Azure 檔共用映射到磁碟機 X。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

存取共用遭到拒絕，且您收到 `New-PSDrive : Access is denied` 錯誤。 訪問被拒絕，因為不允許的存儲*帳戶不在*服務終結點策略中的允許資源清單中。 

關閉 myVmPublic** VM 的遠端桌面工作階段。

## <a name="clean-up-resources"></a>清除資源

您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源：

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

在本文中，您將服務終結點策略應用於 Azure 存儲。" 您創建了 Azure 存儲帳戶，並且僅從虛擬網路子網對某些存儲帳戶（因此拒絕其他存儲帳戶）進行網路訪問。 要瞭解有關服務終結點策略的詳細資訊，請參閱[服務終結點策略概述](virtual-network-service-endpoint-policies-overview.md)。
