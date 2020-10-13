---
title: 安全 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何停用 Azure Active Directory Domain Services 受控網域的弱式密碼、舊通訊協定和 NTLM 密碼雜湊同步處理。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: c5a3a7b6cd43bebccdbfb22bb4c81f376abc15d9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967744"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>停用弱式密碼和密碼雜湊同步處理以保護 Azure Active Directory Domain Services 受控網域

根據預設，Azure Active Directory Domain Services (Azure AD DS) 會啟用加密，例如 NTLM v1 和 TLS v1。 某些繼承應用程式可能需要這些加密，但會被視為弱式，而且如果您不需要這些密碼，則可以停用它們。 如果您使用 Azure AD Connect 的內部部署混合式連接，也可以停用 NTLM 密碼雜湊的同步處理。

本文說明如何停用 NTLM v1 和 TLS v1 密碼，以及停用 NTLM 密碼雜湊同步處理。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要下列資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 安裝並設定 Azure PowerShell。
    * 請依需要遵循指示，[安裝 Azure PowerShell 模組並連線至 Azure 訂用帳戶](/powershell/azure/install-az-ps)。
    * 務必使用 [Connect-AzAccount][Connect-AzAccount] Cmdlet 登入您的 Azure 訂用帳戶。
* 安裝並設定 Azure AD PowerShell。
    * 請依需要遵循指示，[安裝 Azure AD PowerShell 模組並連線至 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 務必使用 [Connect-AzureAD][Connect-AzureAD] Cmdlet 登入您的 Azure AD 租用戶。

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>停用弱式加密和 NTLM 密碼雜湊同步處理

若要停用弱式加密套件和 NTLM 認證雜湊同步處理，請登入您的 Azure 帳戶，然後使用 [get-azresource][Get-AzResource] Cmdlet 取得 Azure AD DS 資源：

> [!TIP]
> 如果您在使用 [get-azresource][Get-AzResource] 命令時收到錯誤，指出 *Microsoft AAD/DomainServices* 資源不存在，請 [提高您的存取權以管理所有 Azure 訂用帳戶和管理群組][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

接下來，定義 *DomainSecuritySettings* 來設定下列安全性選項：

1. 停用 NTLM v1 支援。
2. 從您的內部部署 AD 停用 NTLM 密碼雜湊同步處理。
3. 停用 TLS v1。

> [!IMPORTANT]
> 如果您在 Azure AD DS 受控網域中停用 NTLM 密碼雜湊同步處理，則使用者和服務帳戶無法執行 LDAP 簡單系結。 如果您需要執行 LDAP 簡單系結，請勿在下列命令中設定 *"SyncNtlmPasswords" = "Disabled";* security configuration 選項。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

最後，使用 [get-azresource][Set-AzResource] 指令 Cmdlet 將已定義的安全性設定套用至受控網域。 從第一個步驟指定 Azure AD DS 資源，並指定上一個步驟中的安全性設定。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

將安全性設定套用至受控網域需要幾分鐘的時間。

> [!IMPORTANT]
> 停用 NTLM 之後，請在 Azure AD Connect 中執行完整的密碼雜湊同步處理，以移除受控網域中的所有密碼雜湊。 如果您停用 NTLM，但不強制密碼雜湊同步，則只會在下次密碼變更時移除使用者帳戶的 NTLM 密碼雜湊。 如果使用者在使用 NTLM 作為驗證方法的系統上快取認證，則此行為可能會讓使用者繼續登入。
>
> 一旦 NTLM 密碼雜湊和 Kerberos 密碼雜湊不同，回復至 NTLM 將無法運作。 如果 VM 連接到受控網域控制站，則快取的認證也無法再運作。  

## <a name="next-steps"></a>後續步驟

若要深入瞭解同步處理常式，請參閱 [如何在受控網域中同步處理物件和認證][synchronization]。

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
