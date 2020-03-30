---
title: Azure AD 域服務中的疑難排解登錄問題 |微軟文檔
description: 瞭解如何在 Azure 活動目錄域服務中解決常見使用者登錄問題和錯誤。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612740"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>解決 Azure AD 域服務託管域的帳戶登錄問題

使用者帳戶無法登錄到 Azure AD DS 託管域的最常見原因包括以下方案：

* [該帳戶尚未同步到 Azure AD DS。](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS 沒有密碼雜湊，無法讓帳戶登錄。](#azure-ad-ds-doesnt-have-the-password-hashes)
* [帳戶已經鎖定。](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS 無法在 Azure AD 租戶外部的帳戶的憑據中同步。 外部使用者無法登錄到 Azure AD DS 託管域。

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>帳戶尚未同步到 Azure AD DS

根據目錄的大小，使用者帳戶和憑據雜湊可能需要一段時間才能在 Azure AD DS 中可用。 對於大型目錄，來自 Azure AD 的初始單向同步可能需要幾個小時，最多需要一兩天。 在重試身份驗證之前，請確保等待足夠長的時間。

對於使用者 Azure AD Connect 將本地目錄資料同步到 Azure AD 的混合環境，請確保運行最新版本的 Azure AD 連接，並[配置 Azure AD 連接以在啟用 Azure AD DS 後執行完全同步][azure-ad-connect-phs]。 如果禁用 Azure AD DS，然後重新啟用，則必須再次執行這些步驟。

如果帳戶繼續出現問題，無法通過 Azure AD 連接同步，請重新開機 Azure AD 同步服務。 從安裝了 Azure AD 連接的電腦中打開命令提示視窗並運行以下命令：

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS 沒有密碼雜湊

除非您針對租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式產生或儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

### <a name="hybrid-environments-with-on-premises-synchronization"></a>具有本地同步的混合環境

對於使用 Azure AD Connect 從本地 AD DS 環境進行同步的混合環境，可以在本地生成所需的 NTLM 或 Kerberos 密碼雜湊到 Azure AD 中。 創建 Azure AD DS 託管域後，[啟用對 Azure 活動目錄域服務的密碼雜湊同步][azure-ad-connect-phs]。 如果不完成此密碼雜湊同步步驟，就無法使用 Azure AD DS 登錄到帳戶。 如果禁用 Azure AD DS，然後重新啟用，則必須再次執行這些步驟。

有關詳細資訊，請參閱[密碼雜湊同步如何適用于 Azure AD DS][phs-process]。

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>無本地同步的僅雲環境

Azure AD DS 託管域沒有本地同步（僅 Azure AD 中的帳戶）還需要生成所需的 NTLM 或 Kerberos 密碼雜湊。 如果僅雲帳戶無法登錄，則在啟用 Azure AD DS 後，是否成功完成了帳戶的密碼更改過程？

* **否，密碼未更改。**
    * [更改帳戶的密碼][enable-user-accounts]以生成所需的密碼雜湊，然後等待 15 分鐘，然後再次嘗試登錄。
    * 如果禁用 Azure AD DS 然後重新啟用，則每個帳戶必須再次按照這些步驟更改其密碼並生成所需的密碼雜湊。
* **是，密碼已更改。**
    * 嘗試使用*UPN*格式（如`driley@aaddscontoso.com`）而不是*SAMAccountName*格式（如`AADDSCONTOSO\deeriley`）登錄。
    * 對於 UPN 首碼過長或與託管域上的其他使用者相同的使用者，可以自動生成*SAMAccountName。* *UPN*格式保證在 Azure AD 租戶中是唯一的。

## <a name="the-account-is-locked-out"></a>帳戶被鎖定

當滿足未成功登錄嘗試的已定義閾值時，Azure AD DS 中的使用者帳戶將鎖定。 此帳戶鎖定行為旨在保護您免受可能指示自動數位攻擊的重複暴力強制登錄嘗試的影響。

預設情況下，如果在 2 分鐘內嘗試了 5 次錯誤密碼，則帳戶將鎖定 30 分鐘。

有關詳細資訊以及如何解決帳戶鎖定問題，請參閱 Azure AD [DS 中的帳戶鎖定問題故障排除][troubleshoot-account-lockout]。

## <a name="next-steps"></a>後續步驟

如果仍難以將 VM 加入 Azure AD DS 託管域，[請查找説明並打開 Azure 活動目錄的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
