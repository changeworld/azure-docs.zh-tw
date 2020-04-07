---
title: 在 Azure AD 網域服務中排除帳戶鎖定故障 |微軟文件
description: 瞭解如何解決導致使用者帳戶在 Azure 活動目錄域服務中鎖定的常見問題。
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743460"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>解決 Azure AD 網域服務託管域的帳戶鎖定問題

為了防止重複的惡意登錄嘗試,Azure AD DS 會在定義的閾值后鎖定帳戶。 此帳戶鎖定也可能意外發生,而不會發生登錄攻擊事件。 例如,如果使用者反覆輸入錯誤的密碼或服務嘗試使用舊密碼,則帳戶將鎖定。

此故障排除文章概述了帳戶鎖定發生的原因以及如何配置行為,以及如何查看安全審核以排除鎖定事件。

## <a name="what-is-an-account-lockout"></a>什麼是帳戶鎖定?

當滿足未成功登錄嘗試的已定義閾值時,Azure AD DS 中的使用者帳戶將鎖定。 此帳戶鎖定行為旨在保護您免受可能指示自動數位攻擊的重複暴力強制登錄嘗試的影響。

**默認情況下,如果在 2 分鐘內嘗試了 5 次錯誤密碼,則帳戶將鎖定 30 分鐘。**

使用細粒度密碼策略配置預設帳戶鎖定閾值。 如果您有一組特定的要求,則可以覆蓋這些預設帳戶鎖定閾值。 但是,不建議增加閾值限制以嘗試減少帳戶鎖定的數量。 首先排除帳戶鎖定行為的來源。

### <a name="fine-grained-password-policy"></a>更細緻的密碼原則

細粒度密碼策略 (FGP) 允許您對網域中的不同使用者應用密碼和帳戶鎖定策略的特定限制。 FGPP 僅影響 Azure AD DS 託管域中的使用者。 從 Azure AD 同步到 Azure AD DS 託管域的雲端使用者和網域使用者僅受 Azure AD DS 中的密碼策略的影響。 其在 Azure AD 或本地目錄中的帳戶不受影響。

策略通過 Azure AD DS 託管域中的組關聯分發,所做的任何更改都應用於下一個用戶登錄。 更改策略不會解鎖已鎖定的使用者帳戶。

有關細粒度密碼策略的詳細資訊,以及直接在 Azure AD DS 中建立的使用者與從 Azure AD 同步的使用者之間的差異,請參閱[設定密碼和帳戶鎖定策略][configure-fgpp]。

## <a name="common-account-lockout-reasons"></a>常見帳戶鎖定原因

在沒有任何惡意或因素的情況下,將帳戶鎖定的最常見原因包括以下方案:

* **使用者把自己鎖在門外。**
    * 最近更改密碼後,用戶繼續使用以前的密碼嗎? 默認帳戶鎖定策略在 2 分鐘內嘗試 5 次失敗,可能是由於使用者無意中重試舊密碼造成的。
* **有一個應用程式或服務具有舊密碼。**
    * 如果應用程式或服務使用帳戶,這些資源可能會反覆嘗試使用舊密碼登錄。 此行為會導致帳戶被鎖定。
    * 嘗試最小化跨多個不同應用程式或服務的帳戶使用,並記錄憑據的使用位置。 如果帳戶密碼已更改,請相應地更新關聯的應用程式或服務。
* **密碼已在不同的環境中更改,並且新密碼尚未同步。**
    * 如果在 Azure AD DS 之外(如在 ADDS 環境中)更改了帳戶密碼,則密碼更改可能需要幾分鐘才能通過 Azure AD 同步並同步到 Azure AD DS。
    * 嘗試在密碼同步過程完成之前通過 Azure AD DS 登錄到資源的使用者會導致其帳戶被鎖定。

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>通過安全審核解決帳戶鎖定問題

在帳號鎖定事件發生時與它們來自何處進行故障排除,[請為 Azure AD DS 啟用安全稽核 。][security-audit-events] 審核事件僅從啟用該功能時捕獲。 理想情況下,您應該在出現要排除帳戶鎖定問題*之前*啟用安全審核。 如果使用者帳戶反覆出現鎖定問題,則可以為下次發生這種情況啟用安全審核。

開啟安全稽核後,以下範例查詢將向您展示如何檢視*帳戶鎖定事件*,代碼*4740*。

檢視過去七天的所有帳戶鎖定事件:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

查看名為*driley*的帳戶過去七天的所有帳戶鎖定事件。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

查看 2019 年 6 月 26 日上午 9 點的所有帳戶鎖定事件。 和 2019 年 7 月 1 日午夜,按日期和時間排序:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>後續步驟

有關調整帳戶鎖定閾值的細粒度密碼策略的詳細資訊,請參閱[設定密碼和帳戶鎖定策略][configure-fgpp]。

如果仍難以將 VM 加入 Azure AD DS 託管域,[請尋找說明並開啟 Azure 活動目錄的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
