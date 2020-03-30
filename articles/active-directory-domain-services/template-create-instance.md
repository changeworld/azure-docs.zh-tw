---
title: 使用範本啟用 Azure DS 域服務 |微軟文檔
description: 瞭解如何使用 Azure 資源管理器範本配置和啟用 Azure 活動目錄域服務
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612787"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本創建 Azure 活動目錄域服務託管域

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如：網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 您不需要自行部署、管理和修補網域控制站，就可以使用這些網域服務。 Azure AD DS 會與您現有的 Azure AD 租用戶整合。 這項整合可讓使用者使用其公司認證進行登入，而您可以使用現有的群組和使用者帳戶來保護資源的存取。

本文介紹如何使用 Azure 資源管理器範本啟用 Azure AD DS。 使用 Azure PowerShell 創建支援資源。

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

## <a name="dns-naming-requirements"></a>DNS 命名要求

當您建立 Azure AD DS 執行個體時，您可以指定 DNS 名稱。 以下是選擇此 DNS 名稱時的一些考量：

* **內置功能變數名稱：** 預設情況下，使用目錄的內置功能變數名稱 *（.onmicrosoft.com*尾碼）。 如果您想要啟用透過網際網路對受控網域進行安全 LDAP 存取，則無法建立數位憑證來保護對此預設網域建立的連線。 .onmicrosoft.com** 網域屬於 Microsoft，因此憑證授權單位不會發行憑證。
* **自訂功能變數名稱：** 最常見的方法是指定自訂功能變數名稱，通常是您已經擁有且可路由的功能變數名稱。 當您使用可路由的自訂網域時，流量就可以在應用程式需要支援時，流向正確的位置。
* **不可路由域尾碼：** 我們通常建議您避免使用不可路由的功能變數名稱尾碼，如*contoso.local*。 .local** 尾碼是不可路由的，因此會導致 DNS 解析發生問題。

> [!TIP]
> 如果您建立自訂網域名稱，請注意現有的 DNS 命名空間。 建議使用獨立于任何現有 Azure 或本地 DNS 名稱空間的功能變數名稱。
>
> 例如，如果現有 dns 名稱空間*為 contoso.com*，則創建具有*aaddscontoso.com*的自訂功能變數名稱的 Azure AD DS 託管域。 如果需要使用安全 LDAP，則必須註冊並擁有此自訂功能變數名稱才能生成所需的證書。
>
> 您可能需要為環境中的其他服務創建一些其他 DNS 記錄，或者在環境中的現有 DNS 名稱空間之間創建條件 DNS 轉寄站。 例如，如果您使用根 DNS 名稱執行裝載網站的 Web 伺服器，可能會發生需要其他 DNS 項目的命名衝突。
>
> 在這些教程和操作操作文章中 *，aaddscontoso.com*的自訂域用作一個簡短的示例。 在所有命令中，指定您自己的功能變數名稱。

下列 DNS 名稱限制也適用於此：

* **域首碼限制：** 不能創建首碼超過 15 個字元的託管域。 指定功能變數名稱的首碼（如*aaddscontoso.com*功能變數名稱中的*aaddscontoso）* 必須包含 15 個或更少字元。
* **網路名稱衝突：** 託管域的 DNS 功能變數名稱不應在虛擬網路中存在。 具體而言，請檢閱下列會導致名稱衝突的案例：
    * Azure 虛擬網路上是否已有包含相同 DNS 網域名稱的 Active Directory 網域。
    * 您打算啟用受控網域的虛擬網路是否與內部部署網路建立 VPN 連線。 在此案例中，確定您在內部部署網路上沒有使用相同 DNS 網域名稱的網域。
    * 您在 Azure 虛擬網路上是否已有使用該名稱的 Azure 雲端服務。

## <a name="create-required-azure-ad-resources"></a>創建所需的 Azure AD 資源

Azure AD DS 需要服務主體和 Azure AD 組。 這些資源允許 Azure AD DS 託管域同步資料，並定義哪些使用者具有託管域中的管理許可權。

首先，使用[註冊-AzResource提供程式][Register-AzResourceProvider]Cmdlet 註冊 Azure AD 域服務資來源提供者：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

使用[New-AzureADService 主體][New-AzureADServicePrincipal]Cmdlet 創建 Azure AD 服務主體，以便 Azure AD DS 進行通信和身份驗證。 特定應用程式 ID 使用名為*網域控制站服務*，ID為*2565bd9d-da50-47d4-8b85-4c97f669dc36*。 不要更改此應用程式 ID。

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

現在，使用[新 AzureAD 組][New-AzureADGroup]Cmdlet 創建名為*AAD DC 管理員的*Azure AD 組。 然後，向此組添加的使用者被授予在 Azure AD DS 託管域上執行管理工作的許可權。

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

最後，使用[新 AzResourceGroup][New-AzResourceGroup] Cmdlet 創建資源組。 在下面的示例中，資源組名為*myResourceGroup，* 並在*西部*區域創建。 使用您自己的名稱和所需區域：

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

如果您選擇支援可用性區域的區域，Azure AD DS 資源會跨區域分散，以供額外的備援。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。

您不需要針對要跨區域分散的 Azure AD DS 進行設定。 Azure 平台會自動處理在區域之間分散資源。 有關詳細資訊，請參閱區域可用性，請參閱 Azure[中有哪些可用性區域？][availability-zones]

## <a name="resource-definition-for-azure-ad-ds"></a>Azure AD DS 的資源定義

作為資源管理器資源定義的一部分，需要以下配置參數：

| 參數               | 值 |
|-------------------------|---------|
| domainName              | 託管域的 DNS 功能變數名稱，考慮了以前有關命名首碼和衝突的要點。 |
| 過濾同步            | Azure AD DS 可讓您同步 Azure AD 中的「所有」** 使用者和群組，或僅對特定群組進行「限域」** 同步。 如果您選擇同步「所有」使用者和群組，您之後就無法選擇只執行限域同步。<br /> 如需有關限域同步的詳細資訊，請參閱 [Azure AD Domain Services 的限域同步][scoped-sync]。|
| notificationSettings    | 如果在 Azure AD DS 託管域中生成任何警報，則可以發送電子郵件通知。 <br />可以為這些通知*啟用*Azure 租戶的*全域管理員*和*AAD DC 管理員*組的成員。<br /> 如果需要，當有需要注意的警報時，可以為通知添加其他收件者。|
| 域配置類型 | 根據預設，Azure AD DS 受控網域會建立為*使用者*樹系。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 不需要指定*域配置*值來創建使用者林。<br /> *資源*樹系只會同步直接在 Azure AD 中建立的使用者和群組。 資源樹系目前為預覽狀態。 將值設置為*資源信任*以創建資源林。<br />如需*資源*樹系的詳細資訊，包括您使用某一樹系的原因，以及如何建立與內部部署 AD DS 網域之間的樹系信任，請參閱 [Azure AD DS 資源樹系概觀][resource-forests]。|

以下壓縮參數定義顯示了如何聲明這些值。 創建名為*aaddscontoso.com*的使用者林，所有使用者從 Azure AD 同步到 Azure AD DS 託管域：

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

然後，以下壓縮資源管理器範本資源類型用於定義和創建 Azure AD DS 託管域。 Azure 虛擬網路和子網必須已經存在，或者作為資源管理器範本的一部分創建。 Azure AD DS 託管域已連接到此子網。

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

這些參數和資源類型可用作更廣泛的資源管理器範本的一部分來部署託管域，如下節所示。

## <a name="create-a-managed-domain-using-sample-template"></a>使用示例範本創建託管域

以下完整的資源管理器示例範本創建 Azure AD DS 託管域和支援的虛擬網路、子網和網路安全性群組規則。 網路安全性群組規則是保護託管域並確保流量能夠正確流動所必需的。 將創建具有*aaddscontoso.com* DNS 名稱的使用者林，所有使用者都從 Azure AD 同步：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

此範本可以使用首選部署方法（如[Azure 門戶][portal-deploy][、Azure PowerShell][powershell-deploy]或 CI/CD 管道）進行部署。 下面的示例使用[新阿茲資源組部署][New-AzResourceGroupDeployment]Cmdlet。 指定您自己的資源組名稱和範本檔案名：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

創建資源並將控制項返回到 PowerShell 提示符需要幾分鐘時間。 Azure AD DS 託管域繼續在後臺預配，最多可能需要一個小時才能完成部署。 在 Azure 門戶中，Azure AD DS 託管域的 **"概述"** 頁顯示整個部署階段的目前狀態。

當 Azure 門戶顯示 Azure AD DS 託管域已完成預配時，需要完成以下任務：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 要配置 DNS，請在門戶中選擇 Azure AD DS 託管域。 在 **"概述"** 視窗中，系統會提示您自動設定這些 DNS 設置。
* [將密碼同步啟用到 Azure AD 域服務](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便最終使用者可以使用其公司憑據登錄到託管域。

## <a name="next-steps"></a>後續步驟

要查看 Azure AD DS 託管域的操作，可以[域加入 Windows VM、][windows-join][配置安全的 LDAP][tutorial-ldaps]和[配置密碼雜湊同步][tutorial-phs]。

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

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
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
