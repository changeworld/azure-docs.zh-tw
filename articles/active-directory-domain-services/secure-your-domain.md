---
title: 保護 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何停用 Azure Active Directory Domain Services 受控網域的弱式加密、舊通訊協定和 NTLM 密碼雜湊同步處理。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 581963c94129c36acbd8761d93e369281797fa9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654727"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>停用弱式密碼和密碼雜湊同步處理，以保護 Azure AD Domain Services 受控網域

根據預設，Azure Active Directory Domain Services （Azure AD DS）可讓您使用 NTLM v1 和 TLS v1 之類的密碼。 某些繼承應用程式可能需要這些加密，但被視為弱式，如果您不需要，也可以停用這些加密。 如果您使用 Azure AD Connect 的內部部署混合式連線，您也可以停用 NTLM 密碼雜湊的同步處理。

本文說明如何停用 NTLM v1 和 TLS v1 密碼，並停用 NTLM 密碼雜湊同步處理。

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要下列資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 安裝並設定 Azure PowerShell。
    * 請依需要遵循指示，[安裝 Azure PowerShell 模組並連線至 Azure 訂用帳戶](/powershell/azure/install-az-ps)。
    * 務必使用 [Connect-AzAccount][Connect-AzAccount] Cmdlet 登入您的 Azure 訂用帳戶。
* 安裝並設定 Azure AD PowerShell。
    * 請依需要遵循指示，[安裝 Azure AD PowerShell 模組並連線至 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 務必使用 [Connect-AzureAD][Connect-AzureAD] Cmdlet 登入您的 Azure AD 租用戶。

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>停用弱式加密和 NTLM 密碼雜湊同步處理

若要停用弱式加密套件和 NTLM 認證雜湊同步處理，請登入您的 Azure 帳戶，然後使用[get-azresource][Get-AzResource] Cmdlet 取得 Azure AD DS 資源：

> [!TIP]
> 如果您使用[get-azresource][Get-AzResource]命令收到錯誤，指出*Microsoft AAD/DomainServices*資源不存在，請[提升您的存取權以管理所有 Azure 訂用帳戶和管理群組][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

接下來，定義*DomainSecuritySettings*以設定下列安全性選項：

1. 停用 NTLM v1 支援。
2. 從您的內部部署 AD 停用 NTLM 密碼雜湊同步處理。
3. 停用 TLS v1。

> [!IMPORTANT]
> 如果您停用 Azure AD DS 受控網域中的 NTLM 密碼雜湊同步處理，使用者和服務帳戶就無法執行 LDAP 簡單系結。 如果您需要執行 LDAP 簡單系結，請不要在下列命令中設定 *"SyncNtlmPasswords" = "Disabled";* security configuration 選項。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

最後，使用[get-azresource][Set-AzResource] Cmdlet，將定義的安全性設定套用至 Azure AD DS 受控網域。 指定第一個步驟中的 Azure AD DS 資源，以及上一個步驟中的安全性設定。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

將安全性設定套用至 Azure AD DS 受控網域需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟

若要深入瞭解同步處理常式，請參閱[如何在 AZURE AD DS 受控網域中同步處理物件和認證][synchronization]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD