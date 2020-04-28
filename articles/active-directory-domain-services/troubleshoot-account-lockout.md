---
title: 針對 Azure AD Domain Services 中的帳戶鎖定進行疑難排解 |Microsoft Docs
description: 瞭解如何針對造成使用者帳戶在 Azure Active Directory Domain Services 中遭到鎖定的常見問題進行疑難排解。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743460"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>針對 Azure AD Domain Services 受控網域的帳戶鎖定問題進行疑難排解

為避免重複的惡意登入嘗試，Azure AD DS 會在定義的閾值之後鎖定帳戶。 此帳戶鎖定也可能在沒有登入攻擊事件的情況下發生。 例如，如果使用者重複輸入錯誤的密碼或服務嘗試使用舊密碼，則帳戶會遭到鎖定。

這篇疑難排解文章概述為什麼會發生帳戶鎖定，以及如何設定行為，以及如何檢查安全性審查以針對鎖定事件進行疑難排解。

## <a name="what-is-an-account-lockout"></a>什麼是帳戶鎖定？

當達到未成功登入嘗試的已定義閾值時，Azure AD DS 中的使用者帳戶會遭到鎖定。 此帳戶鎖定行為的設計，是為了保護您免于重複的暴力密碼破解嘗試，這可能表示自動數位攻擊。

**根據預設，如果2分鐘內有5個不正確的密碼嘗試，帳戶會被鎖定30分鐘。**

預設帳戶鎖定閾值是使用更細緻的密碼原則來設定。 如果您有一組特定的需求，您可以覆寫這些預設帳戶鎖定閾值。 不過，不建議您增加閾值限制，以嘗試減少帳戶鎖定數目。 先針對帳戶鎖定行為的來源進行疑難排解。

### <a name="fine-grained-password-policy"></a>更細緻的密碼原則

更細緻的密碼原則（Fgpp）可讓您將密碼和帳戶鎖定原則的特定限制套用到網域中的不同使用者。 FGPP 只會影響 Azure AD DS 受控網域內的使用者。 從 Azure AD 同步處理到 Azure AD DS 受控網域的雲端使用者和網域使用者，只會受到 Azure AD DS 內密碼原則的影響。 其在 Azure AD 或內部部署目錄中的帳戶不會受到影響。

原則會透過 Azure AD DS 受控網域中的群組關聯來散發，而您所做的任何變更都會在下一次使用者登入時套用。 變更原則並不會解除鎖定已鎖定的使用者帳戶。

如需更細緻的密碼原則的詳細資訊，以及直接在 Azure AD DS 與從 Azure AD 中同步處理的使用者之間的差異，請參閱[設定密碼和帳戶鎖定原則][configure-fgpp]。

## <a name="common-account-lockout-reasons"></a>常見的帳戶鎖定原因

帳戶被鎖定的最常見原因，沒有任何惡意的意圖或因素，包括下列案例：

* **使用者已將自己鎖定。**
    * 最近的密碼變更之後，使用者是否繼續使用先前的密碼？ 預設的帳戶鎖定原則 5 2 分鐘內失敗的嘗試次數，可能是因為使用者不小心重試舊密碼所導致。
* **有一個具有舊密碼的應用程式或服務。**
    * 如果應用程式或服務使用帳戶，這些資源可能會重複嘗試使用舊密碼登入。 此行為會導致帳戶遭到鎖定。
    * 嘗試將跨多個不同應用程式或服務的帳戶使用降到最低，並記錄使用認證的位置。 如果帳戶密碼已變更，請據以更新相關聯的應用程式或服務。
* **已在不同的環境中變更密碼，但新密碼尚未同步處理。**
    * 如果帳戶密碼在 Azure AD DS 外部變更（例如在內部內部部署 AD DS 環境中），則密碼變更可能需要幾分鐘的時間，才能透過 Azure AD 和 Azure AD DS 同步處理。
    * 在密碼同步處理常式完成之前，嘗試透過 Azure AD DS 登入資源的使用者，會導致帳戶遭到鎖定。

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>使用安全性審核來疑難排解帳戶鎖定

若要疑難排解帳戶鎖定事件發生的時間，以及其來源，請[啟用 AZURE AD DS 的安全性審核][security-audit-events]。 只有當您啟用此功能時，才會從 Audit 事件中捕捉。 在理想的情況下，您應該先啟用安全性審核，*才*會發生帳戶鎖定問題來進行疑難排解。 如果使用者帳戶重複發生鎖定問題，您可以在下次發生這種情況時，讓安全性審查做好準備。

啟用安全性審核之後，下列範例查詢會示範如何審查*帳戶鎖定事件*，程式碼*4740*。

查看過去七天內的所有帳戶鎖定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

針對名為*driley*的帳戶，查看過去七天內的所有帳戶鎖定事件。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

在2019年6月26日上午9點，查看所有帳戶鎖定事件 2019年7月1日午夜，以日期和時間遞增排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>後續步驟

如需更細緻的密碼原則來調整帳戶鎖定閾值的詳細資訊，請參閱[設定密碼和帳戶鎖定原則][configure-fgpp]。

如果您仍有將 VM 加入 Azure AD DS 受控網域的問題，請尋找 [說明]，[並開啟 Azure Active Directory 的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
