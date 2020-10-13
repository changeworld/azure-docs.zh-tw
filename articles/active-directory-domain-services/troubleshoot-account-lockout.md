---
title: 針對 Azure AD Domain Services 中的帳戶鎖定進行疑難排解 |Microsoft Docs
description: 瞭解如何針對在 Azure Active Directory Domain Services 中導致使用者帳戶遭到鎖定的常見問題進行疑難排解。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 340811ee1c518cdccb5bbb0ae9b5f215f5564cfa
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967608"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>針對 Azure Active Directory Domain Services 受控網域的帳戶鎖定問題進行疑難排解

為了避免重複的惡意登入嘗試，Azure Active Directory Domain Services (Azure AD DS) 受控網域會在定義的閾值之後鎖定帳戶。 這項帳戶鎖定也可能在沒有登入攻擊事件的情況下發生。 例如，如果使用者重複輸入錯誤的密碼或服務嘗試使用舊密碼，則會鎖定帳戶。

此疑難排解文章概述發生帳戶鎖定的原因，以及如何設定行為，以及如何檢查安全性審核以針對鎖定事件進行疑難排解。

## <a name="what-is-an-account-lockout"></a>什麼是帳戶鎖定？

當符合未成功登入嘗試的定義閾值時，會鎖定 Azure AD DS 受控網域中的使用者帳戶。 此帳戶鎖定行為的設計目的，是為了保護您免于重複的暴力密碼破解登入嘗試，這可能表示自動化的數位攻擊。

**根據預設，如果在2分鐘內有5個錯誤的密碼嘗試，帳戶將會被鎖定30分鐘。**

預設帳戶鎖定閾值是使用更細緻的密碼原則進行設定。 如果您有一組特定的需求，則可以覆寫這些預設帳戶鎖定閾值。 但是，不建議您增加閾值限制來嘗試減少數量的帳戶鎖定。 先對帳戶鎖定行為的來源進行疑難排解。

### <a name="fine-grained-password-policy"></a>更細緻的密碼原則

更細緻的密碼原則 (Fgpp) 可讓您將特定的密碼和帳戶鎖定原則限制套用到網域中的不同使用者。 FGPP 只會影響受控網域內的使用者。 從 Azure AD 同步處理至受控網域的雲端使用者和網域使用者只會受到受控網域內的密碼原則影響。 他們在 Azure AD 或內部部署目錄中的帳戶不會受到影響。

原則是透過受控網域中的群組關聯來散發，而您所做的任何變更都會在下次使用者登入時套用。 變更原則無法解除鎖定已鎖定的使用者帳戶。

如需更細緻的密碼原則的詳細資訊，以及直接在 Azure AD DS 中建立的使用者與 Azure AD 中的同步處理之間的差異，請參閱 [設定密碼和帳戶鎖定原則][configure-fgpp]。

## <a name="common-account-lockout-reasons"></a>常見的帳戶鎖定原因

帳戶遭到鎖定（沒有任何惡意意圖或因素）的最常見原因包括下列案例：

* **使用者自行鎖定。**
    * 在最近的密碼變更之後，使用者是否繼續使用先前的密碼？ 使用者不小心重試舊密碼可能導致2分鐘內五次嘗試失敗的預設帳戶鎖定原則。
* **有一個具有舊密碼的應用程式或服務。**
    * 如果應用程式或服務使用帳戶，這些資源可能會重複嘗試使用舊密碼登入。 此行為會導致帳戶遭到鎖定。
    * 嘗試將跨多個不同應用程式或服務的帳戶使用降至最低，並記錄使用認證的位置。 如果帳戶密碼已變更，請據以更新相關聯的應用程式或服務。
* **密碼在不同的環境中已經變更，而且新的密碼尚未同步處理。**
    * 如果在受控網域以外的帳戶密碼有所變更，例如在內部內部部署 AD DS 環境中，密碼變更可能需要幾分鐘的時間，才能透過 Azure AD 和受控網域進行同步處理。
    * 在該密碼同步處理常式完成之前，嘗試在受控網域中登入資源的使用者，會導致其帳戶遭到鎖定。

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>針對具有安全性審核的帳戶鎖定進行疑難排解

若要在發生帳戶鎖定事件時進行疑難排解，以及它們來自何處，請 [針對 AZURE AD DS 啟用安全性審核][security-audit-events]。 只有當您啟用此功能時，才會捕捉 Audit 事件。 在理想的情況下，您應該先啟用安全性審核， *然後才* 會發生帳戶鎖定問題進行疑難排解。 如果使用者帳戶重複發生鎖定問題，您可以在下一次發生這種情況時，讓安全性審核做好準備。

啟用安全性審核之後，下列查詢範例會示範如何檢查 *帳戶鎖定事件*（代碼 *4740*）。

查看過去七天內的所有帳戶鎖定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

針對名為 *driley*的帳戶，查看過去七天內的所有帳戶鎖定事件。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

在2020年6月26日上午9點之間查看所有帳戶鎖定事件 和午夜的2020年7月1日，以日期和時間遞增排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>後續步驟

如需更細緻的密碼原則以調整帳戶鎖定閾值的詳細資訊，請參閱 [設定密碼和帳戶鎖定原則][configure-fgpp]。

如果您在將 VM 加入受控網域時仍有問題，請尋找 [說明] [並開啟 Azure Active Directory 的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
