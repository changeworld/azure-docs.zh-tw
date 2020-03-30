---
title: 條件訪問基線策略 - Azure 活動目錄
description: 保護組織免受常見攻擊的基線條件訪問策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767563"
---
# <a name="what-are-baseline-policies"></a>什麼是基線策略？

基線策略是一組預定義策略，可説明保護組織免受許多常見攻擊。 這些常見的攻擊可能包括密碼噴灑、重播和網路釣魚。 基線策略在所有版本的 Azure AD 中都可用。 Microsoft 將這些基準保護原則提供給所有人使用的原因，是因為以身分識別為基礎的攻擊在過去幾年一直持續攀升。 這四個策略的目標是確保所有組織都啟用了基本安全級別，無需額外費用。

管理自訂的條件訪問策略需要 Azure AD 高級許可證。

> [!IMPORTANT]
> 正在棄用基線策略。 有關詳細資訊[，請參閱 Azure 活動目錄中的新增功能。](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>基準原則

![Azure 門戶中的條件訪問基線策略](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

有四個基線策略：

* 需要管理員 MFA（預覽）
* 最終使用者保護（預覽）
* 阻止舊版身份驗證（預覽）
* 需要 MFA 進行服務管理（預覽）

所有這些策略都將影響舊版身份驗證流，如 POP、IMAP 和較舊的 Office 桌面用戶端。

### <a name="exclusions"></a>排除

當基線策略進入其初始公共預覽版時，可以選擇將使用者排除在策略之外。 此功能通過預覽演變，于 2019 年 7 月刪除。 已創建排除的組織能夠繼續保留新使用者無法向策略添加排除項。

### <a name="require-mfa-for-admins-preview"></a>需要管理員 MFA（預覽）

由於管理員帳戶具有電源和存取權限，因此應特別注意它們。 改善特權帳戶保護的一個常見方法是，在用於登錄時，需要更強有力的帳戶驗證形式。 在 Azure 活動目錄中，可以通過要求管理員註冊和使用 Azure 多重要素驗證來獲得更強的帳戶驗證。

管理員需要 MFA（預覽）是一種基線策略，需要以下目錄角色多重要素驗證 （MFA），被視為最特權的 Azure AD 角色：

* 全域管理員
* SharePoint 管理員
* Exchange 系統管理員
* 條件式存取系統管理員
* 安全性系統管理員
* 服務台管理員/密碼管理員
* 計費管理員
* 使用者管理員

如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將它們取代為[受管理的身分識別](../managed-identities-azure-resources/overview.md)。

### <a name="end-user-protection-preview"></a>最終使用者保護（預覽）

高特權管理員並不是唯一的攻擊目標。 錯誤的參與者往往以普通使用者為目標。 獲得存取權限後，這些不法分子可以代表原始帳戶持有人請求存取權限資訊，或下載整個目錄，對整個組織實施網路釣魚攻擊。 改善對所有使用者的保護的一個常見方法是，在檢測到有風險登錄時，需要更強有力的帳戶驗證形式。

**最終使用者保護（預覽）** 是保護目錄中所有使用者的基準策略。 啟用此策略要求所有使用者在 14 天內註冊 Azure 多重要素驗證。 註冊之後，只有在有風險的登入嘗試時，才會提示使用者進行 MFA。 遭盜用的使用者帳戶會遭到封鎖，直到重設密碼和風險解除為止。 

> [!NOTE]
> 以前標記為風險的任何使用者都被阻止，直到密碼重設，並在策略啟動時面臨解雇風險。

### <a name="block-legacy-authentication-preview"></a>阻止舊版身份驗證（預覽）

舊版身份驗證協定（例如：IMAP、SMTP、POP3）是較舊的郵件用戶端通常用於進行身份驗證的協定。 舊協定不支援多重要素驗證。 即使您有一個策略需要對目錄進行多重要素驗證，壞參與者也可以使用這些遺留協定之一進行身份驗證，並繞過多重要素驗證。

保護帳戶免受舊協定惡意身份驗證請求攻擊的最佳方式是阻止這些請求。

**阻止舊版身份驗證（預覽）** 基準策略阻止使用舊協定進行的身份驗證請求。 必須使用現代身份驗證成功登錄所有使用者。 與其他基線策略結合使用時，來自舊協定的請求將被阻止。 此外，所有使用者都需要在需要時進行 MFA。 此策略不會阻止 Exchange 活動同步。

### <a name="require-mfa-for-service-management-preview"></a>需要 MFA 進行服務管理（預覽）

組織使用各種 Azure 服務，並從基於 Azure 資源管理器的工具管理這些服務，例如：

* Azure 入口網站
* Azure PowerShell
* Azure CLI

使用這些工具執行資源管理是一項高度特權的操作。 這些工具可以更改訂閱範圍的配置，如服務設置和訂閱計費。

為了保護特權操作，此**要求 MFA 進行服務管理（預覽）** 策略將需要對訪問 Azure 門戶、Azure PowerShell 或 Azure CLI 的任何使用者進行多重要素驗證。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

* [啟用安全預設值](../fundamentals/concept-fundamentals-security-defaults.md)
* [一般條件式存取原則](concept-conditional-access-policy-common.md)
* [可保護身分識別基礎結構的五個步驟](../../security/fundamentals/steps-secure-identity.md)
