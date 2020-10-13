---
title: 使用 PowerShell 啟用 Azure DS Domain Services | Microsoft Docs
description: 了解如何使用 Azure AD PowerShell 和 Azure PowerShell 設定和啟用 Azure Active Directory Domain Services。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 10/02/2020
ms.author: iainfou
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b43e546cc4f7a07462817080b287628df2af8a95
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704611"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>使用 PowerShell 啟用 Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如：網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 您不需要自行部署、管理和修補網域控制站，就可以使用這些網域服務。 Azure AD DS 會與您現有的 Azure AD 租用戶整合。 這項整合可讓使用者使用其公司認證進行登入，而您可以使用現有的群組和使用者帳戶來保護資源的存取。

本文說明如何使用 PowerShell 啟用 Azure AD DS。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要下列資源：

* 安裝並設定 Azure PowerShell。
    * 請依需要遵循指示，[安裝 Azure PowerShell 模組並連線至 Azure 訂用帳戶](/powershell/azure/install-az-ps)。
    * 務必使用 [Connect-AzAccount][Connect-AzAccount] Cmdlet 登入您的 Azure 訂用帳戶。
* 安裝並設定 Azure AD PowerShell。
    * 請依需要遵循指示，[安裝 Azure AD PowerShell 模組並連線至 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 務必使用 [Connect-AzureAD][Connect-AzureAD] Cmdlet 登入您的 Azure AD 租用戶。
* 您必須擁有 Azure AD 租用戶的*全域管理員*權限，才能啟用 Azure AD DS。
* 您需要 Azure 訂用帳戶中的「參與者」權限，才能建立必要的 Azure AD DS 資源。

## <a name="create-required-azure-ad-resources"></a>建立所需的 Azure AD 資源

Azure AD DS 需要服務主體和 Azure AD 群組。 這些資源可讓 Azure AD DS 受控網域同步處理資料，以及定義受控網域中具有管理權限的使用者。

首先，建立 Azure AD 服務主體，以供 Azure AD DS 通訊和自我驗證。 系統會使用特定的應用程式識別碼，其名稱為「網域控制站服務」以及 2565bd9d-da50-47d4-8b85-4c97f669dc36 的識別碼。 請勿變更此應用程式識別碼。

使用 [New-AzureADServicePrincipal][New-AzureADServicePrincipal] Cmdlet 來建立 Azure AD 服務主體：

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

現在建立名為 *AAD DC 管理員*的 Azure AD 群組。 系統會授與新增至此群組的使用者權限，以在受控網域上執行管理工作。

首先，使用 [Get-AzureADGroup][Get-AzureADGroup] Cmdlet，取得「AAD DC 管理員」群組物件識別碼。 如果群組不存在，則使用 [New-AzureADGroup][New-AzureADGroup] Cmdlet 以「AAD DC 管理員」群組加以建立：

```powershell
# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# If the group doesn't exist, create it
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}
```

建立「AAD DC 管理員」群組之後，使用 [Get-AzureADUser][Get-AzureADUser] Cmdlet 取得想要的使用者物件識別碼，然後使用 [Add-AzureADGroupMember][Add-AzureADGroupMember] Cmdlet 將使用者新增至群組。

在下列範例中，帳戶的使用者物件識別碼為 `admin@contoso.onmicrosoft.com` 的 UPN。 將此使用者帳戶取代為您要新增至 AAD DC 管理員群組之使用者的 UPN：

```powershell
# Retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-network-resources"></a>建立網路資源

首先，使用 [Register-AzResourceProvider][Register-AzResourceProvider] Cmdlet 註冊 Azure AD Domain Services 資源提供者：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

接下來，使用 [New-AzResourceGroup][New-AzResourceGroup] Cmdelt 建立新的資源群組。 在下列範例中，會在 westus 區域中建立名為 myResourceGroup 的資源群組。 使用您自己的名稱和所需的區域：

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

建立適用於 Azure AD Domain Services 的虛擬網路及子網路。 已建立兩個子網路，一個用於 DomainServices，另一個用於 工作負載。 Azure AD DS 會部署到專用的 DomainServices 子網路中。 請勿將其他應用程式或工作負載部署到這個子網路。 針對其餘的 VM，使用個別的工作負載 或其他子網路。

使用 [New-azvirtualnetworksubnetconfig][New-AzVirtualNetworkSubnetConfig] Cmdlet 建立子網路，然後使用 [New-AzVirtualNetwork][New-AzVirtualNetwork] Cmdlet 建立虛擬網路。

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for Azure AD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

### <a name="create-a-network-security-group"></a>建立網路安全性群組

Azure AD DS 需要一個網路安全性群組來保護受控網域所需的連接埠，並封鎖所有其他的傳入流量。 [網路安全性群組 (NSG)][nsg-overview] 包含規則清單，可允許或拒絕 Azure 虛擬網路中的網路流量。 在 Azure AD DS 中，網路安全性群組可作為額外的保護層，以鎖定對受控網域的存取。 若要檢視所需的連接埠，請參閱[網路安全性群組與必要連接埠][network-ports]。

下列 PowerShell Cmdlet 會使用 [New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig] 來建立規則，然後使用 [New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup] 來建立網路安全性群組。 接著，網路安全性群組和規則會使用 [Set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig] Cmdlet，與虛擬網路子網建立關聯。

```powershell
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 443 traffic for synchronization with Azure AD
$nsg101 = New-AzNetworkSecurityRuleConfig `
    -Name AllowSyncWithAzureAD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 101 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg101,$nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>建立受控網域

我們現在就要建立受控網域。 設定您的 Azure 訂用帳戶識別碼，然後提供受控網域的名稱，例如 aaddscontoso.com。 您可以使用 [Get-AzSubscriptionn][Get-AzSubscription] Cmdlet 取得訂用帳戶識別碼。

如果您選擇支援可用性區域的區域，Azure AD DS 資源會跨區域分散，以供額外的備援。

「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。

您不需要針對要跨區域分散的 Azure AD DS 進行設定。 Azure 平台會自動處理在區域之間分散資源。 如需詳細資訊及查看區域可用性，請參閱[什麼是 Azure 中的可用性區域？][availability-zones]。

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -ApiVersion "2017-06-01" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

需要幾分鐘的時間來建立資源，並將控制權交還給 PowerShell 提示字元。 受控網域會繼續在背景中佈建，且最多可能需要一小時才能完成部署。 在 Azure 入口網站中，受控網域的 [概觀] 頁面會顯示整個部署階段的目前狀態。

當 Azure 入口網站顯示受控網域已完成佈建時，必須完成下列工作：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 在入口網站中選取您的受控網域以設定 DNS。 在 [概觀] 視窗中，系統會提示您自動設定這些 DNS 設定。
* [啟用 Azure AD DS 的密碼同步化](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，讓使用者可使用他們的公司認證來登入受控網域。

## <a name="complete-powershell-script"></a>完整的 PowerShell 指令碼

下列完整的 PowerShell 指令碼結合了本文中顯示的所有工作。 複製指令碼，並儲存至使用 `.ps1` 副檔名的檔案。 在本機 PowerShell 主控台或 [Azure Cloud Shell][cloud-shell] 中執行指令碼。

> [!NOTE]
> 若要啟用 Azure AD DS，您必須是 Azure AD 租用戶的全域管理員。 在 Azure 訂用帳戶中，您也至少需要「參與者」權限。

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Create the delegated administration group for Azure AD Domain Services if it doesn't already exist.
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
  
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 443 traffic for synchronization with Azure AD
$nsg101 = New-AzNetworkSecurityRuleConfig `
    -Name AllowSyncWithAzureAD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 101 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg101,$nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -ApiVersion "2017-06-01" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

需要幾分鐘的時間來建立資源，並將控制權交還給 PowerShell 提示字元。 受控網域會繼續在背景中佈建，且最多可能需要一小時才能完成部署。 在 Azure 入口網站中，受控網域的 [概觀] 頁面會顯示整個部署階段的目前狀態。

當 Azure 入口網站顯示受控網域已完成佈建時，必須完成下列工作：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 在入口網站中選取您的受控網域以設定 DNS。 在 [概觀] 視窗中，系統會提示您自動設定這些 DNS 設定。
* [啟用 Azure AD DS 的密碼同步化](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，讓使用者可使用他們的公司認證來登入受控網域。

## <a name="next-steps"></a>後續步驟

若要查看作用中的受控網域，您可以[將 Windows VM 加入網域][windows-join]、[設定安全 LDAP][tutorial-ldaps]，以及[設定密碼雜湊同步處理][tutorial-phs]。

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig
