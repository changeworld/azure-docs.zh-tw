---
title: 對 Azure AD Domain Services 中的登入問題進行疑難排解 |Microsoft Docs
description: 瞭解如何在 Azure Active Directory Domain Services 中針對一般使用者登入問題和錯誤進行疑難排解。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612740"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>針對 Azure AD Domain Services 受控網域的帳戶登入問題進行疑難排解

無法登入 Azure AD DS 受控網域的使用者帳戶最常見的原因包括下列案例：

* [帳戶尚未同步到 Azure AD DS。](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS 沒有密碼雜湊可讓帳戶登入。](#azure-ad-ds-doesnt-have-the-password-hashes)
* [帳戶已被鎖定。](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS 無法同步處理 Azure AD 租使用者外部帳戶的認證。 外部使用者無法登入 Azure AD DS 受控網域。

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>帳戶尚未同步到 Azure AD DS

視目錄的大小而定，可能需要一些時間，使用者帳戶和認證雜湊才會在 Azure AD DS 中提供。 若為大型目錄，這個從 Azure AD 進行的初次單向同步處理可能需要幾個小時，最多一天或兩個。 請確定您等待的時間夠久，再重試驗證。

針對使用者 Azure AD Connect 將內部部署目錄資料同步處理至 Azure AD 的混合式環境，請確定您執行的是最新版本的 Azure AD Connect 並已[設定 Azure AD Connect，以便在啟用 AZURE AD DS 之後執行完整同步][azure-ad-connect-phs]處理。 如果您停用 Azure AD DS 然後重新啟用，就必須再次遵循這些步驟。

如果您繼續遇到未透過 Azure AD Connect 同步處理的帳戶問題，請重新開機 Azure AD 同步服務。 從已安裝 Azure AD Connect 的電腦上，開啟 [命令提示字元] 視窗，然後執行下列命令：

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS 沒有密碼雜湊

除非您針對租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式產生或儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

### <a name="hybrid-environments-with-on-premises-synchronization"></a>具有內部部署同步處理的混合式環境

針對使用 Azure AD Connect 從內部部署 AD DS 環境進行同步處理的混合式環境，您可以在本機產生所需的 NTLM 或 Kerberos 密碼雜湊，並將其同步處理至 Azure AD。 建立 Azure AD DS 受控網域之後，[啟用密碼雜湊同步處理以 Azure Active Directory Domain Services][azure-ad-connect-phs]。 若未完成此密碼雜湊同步處理步驟，您就無法使用 Azure AD DS 來登入帳戶。 如果您停用 Azure AD DS 然後重新啟用，就必須再次遵循這些步驟。

如需詳細資訊，請參閱[AZURE AD DS 的密碼雜湊同步處理的運作方式][phs-process]。

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>無內部部署同步處理的僅限雲端環境

在沒有內部部署同步處理的情況下 Azure AD DS 受控網域，只有 Azure AD 中的帳戶，也需要產生必要的 NTLM 或 Kerberos 密碼雜湊。 如果僅限雲端帳戶無法登入，請在啟用 Azure AD DS 之後，已成功完成帳戶的密碼變更程式？

* **否，密碼尚未變更。**
    * [變更帳戶的密碼][enable-user-accounts]以產生所需的密碼雜湊，然後等候15分鐘，再嘗試重新登入。
    * 如果您停用 Azure AD DS，然後重新啟用，則每個帳戶都必須再次遵循這些步驟來變更其密碼，並產生所需的密碼雜湊。
* **是，密碼已變更。**
    * 嘗試使用*UPN*格式（例如 `driley@aaddscontoso.com`）登入，而不是像 `AADDSCONTOSO\deeriley`這樣的*SAMAccountName*格式。
    * 對於 UPN 前置詞過長或與受控網域上的其他使用者相同的使用者，可能會自動產生*SAMAccountName* 。 *UPN*格式保證在 Azure AD 租使用者內是唯一的。

## <a name="the-account-is-locked-out"></a>帳戶已鎖定

當達到未成功登入嘗試的已定義閾值時，Azure AD DS 中的使用者帳戶會遭到鎖定。 此帳戶鎖定行為的設計，是為了保護您免于重複的暴力密碼破解嘗試，這可能表示自動數位攻擊。

根據預設，如果2分鐘內有5個不正確的密碼嘗試，帳戶會被鎖定30分鐘。

如需詳細資訊以及如何解決帳戶鎖定的問題，請參閱針對[AZURE AD DS 中的帳戶鎖定問題進行疑難排解][troubleshoot-account-lockout]。

## <a name="next-steps"></a>後續步驟

如果您仍有將 VM 加入 Azure AD DS 受控網域的問題，請尋找 [說明]，[並開啟 Azure Active Directory 的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
