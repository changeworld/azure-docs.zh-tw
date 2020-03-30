---
title: 使用電源外殼啟用 Azure DS 域服務 |微軟文檔
description: 瞭解如何使用 Azure AD PowerShell 和 Azure PowerShell 配置和啟用 Azure 活動目錄域服務。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613228"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>使用 PowerShell 啟用 Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如：網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 您不需要自行部署、管理和修補網域控制站，就可以使用這些網域服務。 Azure AD DS 會與您現有的 Azure AD 租用戶整合。 這項整合可讓使用者使用其公司認證進行登入，而您可以使用現有的群組和使用者帳戶來保護資源的存取。

本文介紹如何使用 PowerShell 啟用 Azure AD DS。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

要完成本文，您需要以下資源：

* 安裝並設定 Azure PowerShell。
    * 如果需要，請按照說明[安裝 Azure PowerShell 模組並連接到 Azure 訂閱](/powershell/azure/install-az-ps)。
    * 請確保使用[連接-AzAccount][Connect-AzAccount] Cmdlet 登錄到 Azure 訂閱。
* 安裝和配置 Azure AD 電源外殼。
    * 如果需要，請按照說明[安裝 Azure AD PowerShell 模組並連接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 請確保使用[連接 AzureAD][Connect-AzureAD] Cmdlet 登錄到 Azure AD 租戶。
* 您必須擁有 Azure AD 租用戶的*全域管理員*權限，才能啟用 Azure AD DS。
* 您需要 Azure 訂用帳戶中的「參與者」** 權限，才能建立必要的 Azure AD DS 資源。

## <a name="create-required-azure-ad-resources"></a>創建所需的 Azure AD 資源

Azure AD DS 需要服務主體和 Azure AD 組。 這些資源允許 Azure AD DS 託管域同步資料，並定義哪些使用者具有託管域中的管理許可權。

首先，為 Azure AD DS 創建 Azure AD 服務主體以進行通信和身份驗證。 特定應用程式 ID 使用名為*網域控制站服務*，ID為*2565bd9d-da50-47d4-8b85-4c97f669dc36*。 不要更改此應用程式 ID。

使用[New-AzureADService 主體][New-AzureADServicePrincipal]Cmdlet 創建 Azure AD 服務主體：

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

現在創建名為*AAD DC 管理員的*Azure AD 組。 然後，向此組添加的使用者被授予在 Azure AD DS 託管域上執行管理工作的許可權。

使用[新 AzureAD 組][New-AzureADGroup]Cmdlet 創建*AAD DC 管理員*組：

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

創建*AAD DC 管理員*組後，使用[Add-AzureAdGroup 成員][Add-AzureADGroupMember]Cmdlet 將使用者添加到組。 您首先使用[Get-AzureADGroup][Get-AzureADGroup] Cmdlet 獲取*AAD DC 管理員*組物件識別碼，然後使用[Get-AzureADUser][Get-AzureADUser] Cmdlet 獲取所需的使用者的物件識別碼。

在下面的示例中，具有 UPN 的`admin@aaddscontoso.onmicrosoft.com`帳戶的使用者物件識別碼。 將此使用者帳戶替換為要添加到*AAD DC 管理員*組的使用者的 UPN：

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

首先，使用[註冊-AzResource提供程式][Register-AzResourceProvider]Cmdlet 註冊 Azure AD 域服務資來源提供者：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

接下來，使用[新 AzResourceGroup][New-AzResourceGroup] Cmdlet 創建資源組。 在下面的示例中，資源組名為*myResourceGroup，* 並在*西部*區域創建。 使用您自己的名稱和所需區域：

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

為 Azure AD 域服務創建虛擬網路和子網。 創建了兩個子網 - 一個用於*域服務*，另一個用於*工作負載*。 Azure AD DS 部署到專用*域服務*子網中。 不要將其他應用程式或工作負載部署到此子網。 對 VM 的其餘部分使用單獨的*工作負載*或其他子網。

使用[新阿茲虛擬網路 Subnet Config][New-AzVirtualNetworkSubnetConfig] Cmdlet 創建子網，然後使用[新阿茲虛擬網路][New-AzVirtualNetwork]Cmdlet 創建虛擬網路。

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>創建 Azure AD DS 託管域

現在，讓我們創建一個 Azure AD DS 託管域。 設置 Azure 訂閱 ID，然後為託管域提供名稱，如*aaddscontoso.com*。 您可以使用[獲取-Az 訂閱][Get-AzSubscription]Cmdlet 獲取訂閱 ID。

如果您選擇支援可用性區域的區域，Azure AD DS 資源會跨區域分散，以供額外的備援。

「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。

您不需要針對要跨區域分散的 Azure AD DS 進行設定。 Azure 平台會自動處理在區域之間分散資源。 有關詳細資訊，請參閱區域可用性，請參閱 Azure[中有哪些可用性區域？][availability-zones]

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

創建資源並將控制項返回到 PowerShell 提示符需要幾分鐘時間。 Azure AD DS 託管域繼續在後臺預配，最多可能需要一個小時才能完成部署。 在 Azure 門戶中，Azure AD DS 託管域的 **"概述"** 頁顯示整個部署階段的目前狀態。

當 Azure 門戶顯示 Azure AD DS 託管域已完成預配時，需要完成以下任務：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 要配置 DNS，請在門戶中選擇 Azure AD DS 託管域。 在 **"概述"** 視窗中，系統會提示您自動設定這些 DNS 設置。
* 如果在支援可用性區域的區域中創建了 Azure AD DS 託管域，請創建網路安全性群組以限制 Azure AD DS 託管域的虛擬網路中的流量。 創建 Azure 標準負載等化器，需要放置這些規則。 此網路安全性群組保護 Azure AD DS，並且託管域正常工作是必需的。
    * 要創建網路安全性群組和所需規則，請在門戶中選擇 Azure AD DS 託管域。 在 **"概述"** 視窗中，系統會提示您自動創建和配置網路安全性群組。
* [將密碼同步啟用到 Azure AD 域服務](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便最終使用者可以使用其公司憑據登錄到託管域。

## <a name="complete-powershell-script"></a>完整的 PowerShell 腳本

以下完整的 PowerShell 腳本結合了本文中顯示的所有任務。 複製腳本並將其保存到具有副檔名`.ps1`的檔。 在本地 PowerShell 主控台或 Azure[雲外殼][cloud-shell]中運行腳本。

> [!NOTE]
> 要啟用 Azure AD DS，您必須是 Azure AD 租戶的全域管理員。 在 Azure 訂閱中，您還需要至少*參與者*許可權。

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

創建資源並將控制項返回到 PowerShell 提示符需要幾分鐘時間。 Azure AD DS 託管域繼續在後臺預配，最多可能需要一個小時才能完成部署。 在 Azure 門戶中，Azure AD DS 託管域的 **"概述"** 頁顯示整個部署階段的目前狀態。

當 Azure 門戶顯示 Azure AD DS 託管域已完成預配時，需要完成以下任務：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 要配置 DNS，請在門戶中選擇 Azure AD DS 託管域。 在 **"概述"** 視窗中，系統會提示您自動設定這些 DNS 設置。
* 如果在支援可用性區域的區域中創建了 Azure AD DS 託管域，請創建網路安全性群組以限制 Azure AD DS 託管域的虛擬網路中的流量。 創建 Azure 標準負載等化器，需要放置這些規則。 此網路安全性群組保護 Azure AD DS，並且託管域正常工作是必需的。
    * 要創建網路安全性群組和所需規則，請在門戶中選擇 Azure AD DS 託管域。 在 **"概述"** 視窗中，系統會提示您自動創建和配置網路安全性群組。
* [將密碼同步啟用到 Azure AD 域服務](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便最終使用者可以使用其公司憑據登錄到託管域。

## <a name="next-steps"></a>後續步驟

要查看 Azure AD DS 託管域的操作，可以[域加入 Windows VM、][windows-join][配置安全的 LDAP][tutorial-ldaps]和[配置密碼雜湊同步][tutorial-phs]。

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
