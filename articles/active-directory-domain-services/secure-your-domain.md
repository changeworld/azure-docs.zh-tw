---
title: 安全 Azure AD 域服務 |微軟文檔
description: 瞭解如何禁用 Azure 活動目錄域服務託管域的弱密碼、舊協定和 NTLM 密碼雜湊同步。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 8eee516beaaf26ed25bd20f9689d26fdb1eb9b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546229"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>禁用弱密碼和密碼雜湊同步，以保護 Azure AD 域服務託管域

預設情況下，Azure 活動目錄域服務 （Azure AD DS） 允許使用密碼，如 NTLM v1 和 TLS v1。 某些舊應用程式可能需要這些密碼，但被視為弱，如果您不需要，則可以禁用這些密碼。 如果使用 Azure AD Connect 進行本地混合連接，還可以禁用 NTLM 密碼雜湊的同步。

本文介紹如何禁用 NTLM v1 和 TLS v1 密碼並禁用 NTLM 密碼雜湊同步。

## <a name="prerequisites"></a>Prerequisites

要完成本文，您需要以下資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 安裝並設定 Azure PowerShell。
    * 如果需要，請按照說明[安裝 Azure PowerShell 模組並連接到 Azure 訂閱](/powershell/azure/install-az-ps)。
    * 請確保使用[連接-AzAccount][Connect-AzAccount] Cmdlet 登錄到 Azure 訂閱。
* 安裝和配置 Azure AD 電源外殼。
    * 如果需要，請按照說明[安裝 Azure AD PowerShell 模組並連接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 請確保使用[連接 AzureAD][Connect-AzureAD] Cmdlet 登錄到 Azure AD 租戶。

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>禁用弱密碼和 NTLM 密碼雜湊同步

要禁用弱密碼套件和 NTLM 憑據雜湊同步，請登錄到 Azure 帳戶，然後使用[Get-AzResource][Get-AzResource] Cmdlet 獲取 Azure AD DS 資源：

> [!TIP]
> 如果使用*Microsoft.AAD/Domain 服務*資源不存在[的 Get-AzResource][Get-AzResource]命令收到錯誤，[請提升您管理所有 Azure 訂閱和管理組的訪問][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

接下來，定義*域安全設置*以配置以下安全選項：

1. 禁用 NTLM v1 支援。
2. 從您的內部部署 AD 停用 NTLM 密碼雜湊同步處理。
3. 禁用 TLS v1。

> [!IMPORTANT]
> 如果在 Azure AD DS 託管域中禁用 NTLM 密碼雜湊同步，則使用者和服務帳戶無法執行 LDAP 簡單綁定。 如果需要執行 LDAP 簡單綁定，請不要在以下命令中設置 *"同步密碼"="已禁用";* 安全配置選項。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

最後，使用[Set-AzResource][Set-AzResource] Cmdlet 將定義的安全設置應用於 Azure AD DS 託管域。 從第一步指定 Azure AD DS 資源，以及上一步的安全設置。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

將安全設置應用於 Azure AD DS 託管域需要一些時間。

## <a name="next-steps"></a>後續步驟

要瞭解有關同步過程的更多詳細資訊，請參閱[如何在 Azure AD DS 託管域中同步物件和憑據][synchronization]。

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