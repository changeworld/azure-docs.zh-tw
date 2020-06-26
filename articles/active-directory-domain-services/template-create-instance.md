---
title: 使用範本啟用 Azure DS Domain Services | Microsoft Docs
description: 了解如何使用 Azure Resource Manager 範本來設定和啟用 Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: d826a40073d243193f87d90ab80333b491a203b2
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734210"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立 Azure Active Directory Domain Services 受控網域

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如：網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 您不需要自行部署、管理和修補網域控制站，就可以使用這些網域服務。 Azure AD DS 會與您現有的 Azure AD 租用戶整合。 這項整合可讓使用者使用其公司認證進行登入，而您可以使用現有的群組和使用者帳戶來保護資源的存取。

本文說明如何使用 Azure Resource Manager 範本啟用 Azure AD DS。 支援的資源是使用 Azure PowerShell 建立的。

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

## <a name="dns-naming-requirements"></a>DNS 命名需求

當您建立 Azure AD DS 受控網域時，您可以指定 DNS 名稱。 以下是選擇此 DNS 名稱時的一些考量：

* **內建網域名稱：** 預設會使用目錄的內建網域名稱 (具有 .onmicrosoft.com 尾碼)。 如果您想要啟用透過網際網路對受控網域進行安全 LDAP 存取，則無法建立數位憑證來保護對此預設網域建立的連線。 .onmicrosoft.com 網域屬於 Microsoft，因此憑證授權單位不會發行憑證。
* **自訂網域名稱：** 最常見的方法是指定自訂網域名稱，通常是您已擁有且可路由的名稱。 當您使用可路由的自訂網域時，流量就可以在應用程式需要支援時，流向正確的位置。
* **非路由式網域尾碼：** 我們通常會建議您避免使用無法路由的網域名稱尾碼，例如 contoso. local。 .local 尾碼是不可路由的，因此會導致 DNS 解析發生問題。

> [!TIP]
> 如果您建立自訂網域名稱，請注意現有的 DNS 命名空間。 建議使用與任何現有 Azure 或內部部署 DNS 命名空間不同的功能變數名稱。
>
> 例如，如果您現有的 DNS 名稱空間是 contoso.com，請使用 corp.contoso.com 自訂網域名稱建立受控網域。 如果您需要使用安全 LDAP，必須註冊並擁有此自訂功能網域名稱，才能產生必要的憑證。
>
> 建議您為環境中的其他服務，或環境中現有 DNS 命名空間之間的條件式 DNS 轉寄站，建立一些額外的 DNS 記錄。 例如，如果您使用根 DNS 名稱執行裝載網站的 Web 伺服器，可能會發生需要其他 DNS 項目的命名衝突。
>
> 在這些教學課程和操作說明文章中，會使用 aadds.contoso.com 的自訂網域作為簡短的範例。 在所有命令中，指定您自己的網域名稱。

下列 DNS 名稱限制也適用於此：

* **網域前置詞限制：** 您無法使用超過 15 個字元的前置詞來建立受控網域。 您指定的網域名稱的前置詞字元數 (例如，aaddscontoso.com 網域名稱中的 aaddscontoso) 必須少於 15 個字元。
* **網路名稱衝突：** 受控網域的 DNS 網域名稱不應已存在於虛擬網路中。 具體而言，請檢閱下列會導致名稱衝突的案例：
    * Azure 虛擬網路上是否已有包含相同 DNS 網域名稱的 Active Directory 網域。
    * 您打算啟用受控網域的虛擬網路是否與內部部署網路建立 VPN 連線。 在此案例中，確定您在內部部署網路上沒有使用相同 DNS 網域名稱的網域。
    * 您在 Azure 虛擬網路上是否已有使用該名稱的 Azure 雲端服務。

## <a name="create-required-azure-ad-resources"></a>建立所需的 Azure AD 資源

Azure AD DS 需要服務主體和 Azure AD 群組。 這些資源可讓受控網域同步處理資料，以及定義受控網域中具有管理權限的使用者。

首先，使用 [Register-AzResourceProvider][Register-AzResourceProvider] Cmdlet 註冊 Azure AD Domain Services 資源提供者：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

使用 [New-AzureADServicePrincipal][New-AzureADServicePrincipal] Cmdlet 建立 Azure AD 服務主體，以供 Azure AD DS 通訊和自我驗證。 系統會使用特定的應用程式識別碼，其名稱為「網域控制站服務」以及 2565bd9d-da50-47d4-8b85-4c97f669dc36 的識別碼。 請勿變更此應用程式識別碼。

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

現在，使用 [New-AzureADGroup][New-AzureADGroup] Cmdlet，建立名為 *AAD DC 管理員* 的 Azure AD 群組。 系統會授與新增至此群組的使用者權限，以在受控網域上執行管理工作。

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

建立 *AAD DC 管理員*群組之後，請使用 [Add-AzureADGroupMember][Add-AzureADGroupMember] Cmdlet，將使用者新增至群組。 首先，您會使用 [Get-AzureADGroup][Get-AzureADGroup] Cmdlet 取得 *AAD DC 管理員*群組物件識別碼，然後使用[Get-AzureADUser][Get-AzureADUser] Cmdlet 取得所需的使用者物件識別碼。

在下列範例中，帳戶的使用者物件識別碼為 `admin@aaddscontoso.onmicrosoft.com` 的 UPN。 將此使用者帳戶取代為您要新增至 AAD DC 管理員群組之使用者的 UPN：

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

最後，使用 [New-AzResourceGroup][New-AzResourceGroup] Cmdelt 建立新的資源群組。 在下列範例中，會在 westus 區域中建立名為 myResourceGroup 的資源群組。 使用您自己的名稱和所需的區域：

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

如果您選擇支援可用性區域的區域，Azure AD DS 資源會跨區域分散，以供額外的備援。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。

您不需要針對要跨區域分散的 Azure AD DS 進行設定。 Azure 平台會自動處理在區域之間分散資源。 如需詳細資訊及查看區域可用性，請參閱[什麼是 Azure 中的可用性區域？][availability-zones]。

## <a name="resource-definition-for-azure-ad-ds"></a>Azure AD DS 的資源定義

作為 Resource Manager 資源定義的一部分，需要下列組態參數：

| 參數               | 值 |
|-------------------------|---------|
| domainName              | 受控網域的 DNS 網域名稱，請將前面幾項命名前置碼和衝突情況列入考慮。 |
| filteredSync            | Azure AD DS 可讓您同步 Azure AD 中的「所有」使用者和群組，或僅對特定群組進行「限域」同步。 如果選擇同步所有使用者和群組，之後就無法選擇只執行限域同步。<br /> 如需有關限域同步的詳細資訊，請參閱 [Azure AD Domain Services 的限域同步][scoped-sync]。|
| notificationSettings    | 如果受控網域中產生任何警示，則會送出電子郵件通知。 <br />您可以「啟用」Azure 租用戶的「全域管理員」和「AAD DC 管理員」 群組的成員，以便他們可以收到通知。<br /> 如有需要，請變新增其他收件者，以便他們在有警示且需要注意時可收到通知。|
| domainConfigurationType | 根據預設，受控網域會建立為*使用者*樹系。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 您不需要指定 domainConfiguration 值來建立使用者樹系。<br /> *資源*樹系只會同步直接在 Azure AD 中建立的使用者和群組。 資源樹系目前為預覽狀態。 將值設定為 ResourceTrusting 以建立資源樹系。<br />如需*資源*樹系的詳細資訊，包括您使用某一樹系的原因，以及如何建立與內部部署 AD DS 網域之間的樹系信任，請參閱 [Azure AD DS 資源樹系概觀][resource-forests]。|

下列壓縮的參數定義會顯示這些值的宣告方式。 系統會建立名為 aaddscontoso.com 的使用者樹系，並將所有使用者從 Azure AD 同步至受控網域：

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

接著會使用下列已壓縮的 Resource Manager 範本資源類型，來定義和建立受控網域。 Azure 虛擬網路和子網路必須已經存在，或建立為 Resource Manager 範本的一部分。 受控網域已連線至此子網路。

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

這些參數和資源類型可用來作為較廣泛 Resource Manager 範本的一部分，以部署受控網域，如下一節所示。

## <a name="create-a-managed-domain-using-sample-template"></a>使用範例範本建立受控網域

下列完整的 Resource Manager 範例範本會建立受控網域，以及支援的虛擬網路、子網路和網路安全性群組規則。 必須要有網路安全性群組規則，才能保護受控網域，並確保流量可以正確地流動。 已建立 DNS 名稱為 aaddscontoso.com 的使用者樹系，並從 Azure AD 同步處理所有使用者：

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

此範本可以使用您偏好的部署方法來部署，例如 [Azure 入口網站][portal-deploy]、[Azure PowerShell][powershell-deploy] 或 CI/CD 管線。 下列範例會使用 [New-azresourcegroupdeployment][New-AzResourceGroupDeployment] Cmdlet。 指定您自己的資源組名稱和範本檔名：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

需要幾分鐘的時間來建立資源，並將控制權交還給 PowerShell 提示字元。 受控網域會繼續在背景中佈建，且最多可能需要一小時才能完成部署。 在 Azure 入口網站中，受控網域的 [概觀] 頁面會顯示整個部署階段的目前狀態。

當 Azure 入口網站顯示受控網域已完成佈建時，必須完成下列工作：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 在入口網站中選取您的受控網域以設定 DNS。 在 [概觀] 視窗中，系統會提示您自動設定這些 DNS 設定。
* [啟用 Azure AD Domain Services 的密碼同步化](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，讓使用者可使用他們的公司認證來登入受控網域。

## <a name="next-steps"></a>後續步驟

若要查看作用中的受控網域，您可以[將 Windows VM 加入網域][windows-join]、[設定安全 LDAP][tutorial-ldaps]，以及[設定密碼雜湊同步處理][tutorial-phs]。

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
