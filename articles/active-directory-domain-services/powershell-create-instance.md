---
title: 使用 PowerShell 啟用 Azure DS 網域服務 |Microsoft Docs
description: 瞭解如何使用 Azure AD PowerShell 和 Azure PowerShell 來設定和啟用 Azure Active Directory Domain Services。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613228"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>使用 PowerShell 啟用 Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如：網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 您不需要自行部署、管理和修補網域控制站，就可以使用這些網域服務。 Azure AD DS 會與您現有的 Azure AD 租用戶整合。 這項整合可讓使用者使用其公司認證進行登入，而您可以使用現有的群組和使用者帳戶來保護資源的存取。

本文說明如何使用 PowerShell 啟用 Azure AD DS。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要下列資源：

* 安裝並設定 Azure PowerShell。
    * 如有需要，請遵循指示來[安裝 Azure PowerShell 模組，並連接到您的 Azure 訂](/powershell/azure/install-az-ps)用帳戶。
    * 請務必使用[disconnect-azaccount][Connect-AzAccount] Cmdlet 登入您的 Azure 訂用帳戶。
* 安裝和設定 Azure AD PowerShell。
    * 如有需要，請遵循指示來[安裝 Azure AD PowerShell 模組，並連接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 請務必使用[AzureAD][Connect-AzureAD] Cmdlet 登入您的 Azure AD 租使用者。
* 您必須擁有 Azure AD 租用戶的*全域管理員*權限，才能啟用 Azure AD DS。
* 您需要 Azure 訂用帳戶中的「參與者」權限，才能建立必要的 Azure AD DS 資源。

## <a name="create-required-azure-ad-resources"></a>建立必要的 Azure AD 資源

Azure AD DS 需要服務主體和 Azure AD 群組。 這些資源可讓 Azure AD DS 受控網域同步處理資料，以及定義哪些使用者在受控網域中具有系統管理許可權。

首先，建立 Azure AD DS 的 Azure AD 服務主體，以進行通訊並自行驗證。 系統會使用指定的應用程式識別碼，並將識別碼為*2565bd9d-da50-47d4-8b85-4c97f669dc36*的*網域控制站服務*。 請勿變更此應用程式識別碼。

使用[get-azureadserviceprincipal][New-AzureADServicePrincipal] Cmdlet 建立 Azure AD 服務主體：

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

現在，建立名為*AAD DC 系統管理員*的 Azure AD 群組。 新增至此群組的使用者會被授與許可權，以在 Azure AD DS 受控網域上執行管理工作。

使用[get-azureadgroup][New-AzureADGroup] Cmdlet 建立*AAD DC 系統管理員*群組：

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

建立*AAD DC 系統管理員*群組後，使用[add-azureadgroupmember][Add-AzureADGroupMember] Cmdlet 將使用者新增至群組。 首先，您會使用[get-azureadgroup][Get-AzureADGroup] Cmdlet 取得*AAD DC 系統管理員*群組物件識別碼，然後使用[new-azureaduser 指令程式][Get-AzureADUser]，以所需的使用者物件識別碼。

在下列範例中，`admin@aaddscontoso.onmicrosoft.com`的 UPN 之帳戶的使用者物件識別碼。 以您想要新增至*AAD DC 系統管理員*群組之使用者的 UPN 取代此使用者帳戶：

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>建立支援用 Azure 資源

首先，使用[register-azresourceprovider][Register-AzResourceProvider] Cmdlet 來註冊 Azure AD Domain Services 資源提供者：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

接下來，使用[remove-azresourcegroup][New-AzResourceGroup] Cmdlet 來建立資源群組。 在下列範例中，資源群組會命名為*myResourceGroup* ，並在*westus*區域中建立。 使用您自己的名稱和所需的區域：

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

建立 Azure AD Domain Services 的虛擬網路和子網。 建立兩個子網，一個用於*DomainServices*，另一個用於*工作負載*。 Azure AD DS 會部署到專用的*DomainServices*子網中。 不要將其他應用程式或工作負載部署到這個子網。 針對其餘的 Vm 使用個別的*工作負載*或其他子網。

使用[new-azvirtualnetworksubnetconfig][New-AzVirtualNetworkSubnetConfig] Cmdlet 建立子網，然後使用[new-azvirtualnetwork][New-AzVirtualNetwork] Cmdlet 建立虛擬網路。

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>建立 Azure AD DS 受控網域

現在讓我們建立一個 Azure AD DS 受控網域。 設定您的 Azure 訂用帳戶識別碼，然後提供受控網域的名稱，例如*aaddscontoso.com*。 您可以使用[get-azsubscription][Get-AzSubscription] Cmdlet 來取得訂用帳戶識別碼。

如果您選擇支援可用性區域的區域，Azure AD DS 資源會跨區域分散，以供額外的備援。

「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。

您不需要針對要跨區域分散的 Azure AD DS 進行設定。 Azure 平台會自動處理在區域之間分散資源。 如需詳細資訊及查看區域可用性，請參閱[什麼是 Azure 中的可用性區域？][availability-zones]。

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

需要幾分鐘的時間來建立資源，並將控制權交還給 PowerShell 提示字元。 Azure AD DS 受控網域會繼續在背景中布建，且最多可能需要一小時的時間來完成部署。 在 Azure 入口網站中，您 Azure AD DS 受控網域的 [**總覽**] 頁面會顯示整個部署階段的目前狀態。

當 Azure 入口網站顯示 Azure AD DS 受控網域已完成布建時，必須完成下列工作：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 若要設定 DNS，請在入口網站中選取您的 Azure AD DS 受控網域。 在 [**總覽**] 視窗中，系統會提示您自動設定這些 DNS 設定。
* 如果您在支援可用性區域的區域中建立了 Azure AD DS 受控網域，請建立網路安全性群組，以限制 Azure AD DS 受控網域的虛擬網路中的流量。 建立的 Azure 標準負載平衡器需要進行這些規則。 此網路安全性群組會保護 Azure AD DS，而且受控網域必須能夠正確運作。
    * 若要建立網路安全性群組和所需的規則，請在入口網站中選取您的 Azure AD DS 受控網域。 在 [**總覽**] 視窗中，系統會提示您自動建立及設定網路安全性群組。
* [啟用密碼同步化以 Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) ，讓終端使用者可以使用其公司認證登入受控網域。

## <a name="complete-powershell-script"></a>完成 PowerShell 腳本

下列完整的 PowerShell 腳本結合了本文中顯示的所有工作。 複製腳本，並將它儲存到副檔名為 `.ps1` 的檔案。 在本機 PowerShell 主控台或[Azure Cloud Shell][cloud-shell]中執行腳本。

> [!NOTE]
> 若要啟用 Azure AD DS，您必須是 Azure AD 租使用者的全域管理員。 在 Azure 訂用帳戶中，您也需要至少一個*參與者*許可權。

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
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

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

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

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

需要幾分鐘的時間來建立資源，並將控制權交還給 PowerShell 提示字元。 Azure AD DS 受控網域會繼續在背景中布建，且最多可能需要一小時的時間來完成部署。 在 Azure 入口網站中，您 Azure AD DS 受控網域的 [**總覽**] 頁面會顯示整個部署階段的目前狀態。

當 Azure 入口網站顯示 Azure AD DS 受控網域已完成布建時，必須完成下列工作：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 若要設定 DNS，請在入口網站中選取您的 Azure AD DS 受控網域。 在 [**總覽**] 視窗中，系統會提示您自動設定這些 DNS 設定。
* 如果您在支援可用性區域的區域中建立了 Azure AD DS 受控網域，請建立網路安全性群組，以限制 Azure AD DS 受控網域的虛擬網路中的流量。 建立的 Azure 標準負載平衡器需要進行這些規則。 此網路安全性群組會保護 Azure AD DS，而且受控網域必須能夠正確運作。
    * 若要建立網路安全性群組和所需的規則，請在入口網站中選取您的 Azure AD DS 受控網域。 在 [**總覽**] 視窗中，系統會提示您自動建立及設定網路安全性群組。
* [啟用密碼同步化以 Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) ，讓終端使用者可以使用其公司認證登入受控網域。

## <a name="next-steps"></a>後續步驟

若要查看作用中的 Azure AD DS 受控網域，您可以將[WINDOWS VM 加入網域][windows-join]、[設定安全 LDAP][tutorial-ldaps]，以及[設定密碼雜湊同步][tutorial-phs]處理。

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

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
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
