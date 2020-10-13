---
title: Azure Active Directory 安全性預設值
description: 安全性預設原則，可協助保護組織免於 Azure AD 中的常見攻擊
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 2c056bd4d5fa9037ce00588269c0da2937ff57ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705328"
---
# <a name="what-are-security-defaults"></a>什麼是安全性預設值？

管理安全性可能很難處理常見的身分識別相關攻擊，例如密碼噴灑、重新執行，以及網路釣魚變得越來越普及。 安全性預設值可供使用預先設定的設安全性定，輕鬆地保護組織免於遭受這些攻擊：

- 要求所有使用者註冊 Azure Multi-Factor Authentication。
- 要求系統管理員執行多重要素驗證。
- 封鎖舊版驗證通訊協定。
- 要求使用者在必要時執行多重要素驗證。
- 保護特殊許可權的活動，例如存取 Azure 入口網站。

![螢幕擷取畫面，其中顯示具有可切換啟用安全性預設值的 Azure 入口網站](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
如需為何提供安全性預設值的詳細資料，請參閱 Alex Weinert 的部落格文章：[安全性預設值簡介](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414) (英文)。

## <a name="availability"></a>可用性

Microsoft 將安全性預設值提供給所有人。 目標是要確保所有組織都已啟用基本層級的安全性，而不需要額外成本。 請在 Azure 入口網站中開啟安全性預設值。 如果租用戶是在 2019 年 10 月 22 日或之後建立，則租用戶中可能已啟用安全性預設值。 為了保護所有的使用者，我們將安全性預設值推出給所有建立的新租用戶。

### <a name="whos-it-for"></a>適合誰？

- 如果組織想要提升安全性狀態，但您不知道如何或從何處開始，則安全性預設值便很適合您。
- 如果您是使用免費層 Azure Active Directory 授權的組織，則安全性預設值便很適合您。

### <a name="who-should-use-conditional-access"></a>誰應該使用條件式存取？

- 如果組織目前使用條件式存取原則將訊號結合在一起，以進行決策並強制執行組織原則，則安全性預設值可能不適合您。 
- 如果您是具有 Azure Active Directory Premium 授權的組織，則安全性預設值可能不適合您。
- 如果組織有複雜的安全性需求，則您應該考慮使用條件式存取。

## <a name="policies-enforced"></a>強制執行原則

### <a name="unified-multi-factor-authentication-registration"></a>統一的 Multi-Factor Authentication 註冊

您租用戶中所有使用者都必須以 Azure Multi-Factor Authentication 的形式註冊多重要素驗證 (MFA)。 使用者有 14 天的時間可使用 Microsoft Authenticator 應用程式來註冊 Azure Multi-Factor Authentication。 經過 14 天之後，使用者將無法登入，直到註冊完成為止。 使用者在啟用安全性預設值之後的第一次成功互動式登入後，為期 14 天的期間便會開始。

### <a name="protecting-administrators"></a>保護系統管理員

具有特殊權限存取權的使用者已提高對環境的存取權。 這些帳戶具有強大權力，您應特別小心處理。 改善特殊權限帳戶保護的常見方法之一，就是登入時要求更強大的帳戶驗證形式。 在 Azure AD 中，藉由要求多重要素驗證即可取得比較強大的帳戶驗證。

向 Azure Multi-Factor Authentication 註冊完成後，下列九個 Azure AD 系統管理員角色將需要在每次登入時執行額外的驗證：

- 全域管理員
- SharePoint 管理員
- Exchange 系統管理員
- 條件式存取系統管理員
- 安全性系統管理員
- 服務台管理員
- 計費管理員
- 使用者管理員
- 驗證系統管理員

### <a name="protecting-all-users"></a>保護所有使用者

我們通常會將系統管理員帳戶視為唯一需要額外驗證層的帳戶。 系統管理員有廣泛的機密資訊存取權，且可對全訂用帳戶的設定進行變更。 但是攻擊者經常以終端使用者為目標。 

這些攻擊者取得存取權之後，即可代表原始帳戶持有者要求存取特殊權限資訊。 其甚至可下載整個目錄，以在整個組織中執行網路釣魚攻擊。 

為所有使用者改善保護的其中一個常見方法，就是針對所有人都要求更強大的帳戶驗證形式，例如 Multi-Factor Authentication。 使用者完成 Multi-Factor Authentication 註冊之後，系統會在必要時提示其進行額外的驗證。 這種功能可保護所有已向 Azure AD 註冊的應用程式，包括 SaaS 應用程式。

### <a name="blocking-legacy-authentication"></a>封鎖舊版驗證

為了讓使用者能夠輕鬆存取雲端應用程式，Azure AD 支援多種驗證通訊協定，包括舊版驗證。 「舊版驗證」一詞，是指由以下幾者提出的驗證要求：

- 不使用新式驗證的用戶端 (例如，Office 2010 用戶端)。
- 任何使用舊版郵件通訊協定 (例如 IMAP、SMTP 或 POP3) 的用戶端。

現今，大部分的入侵登入嘗試來自於舊版驗證。 舊版驗證不支援 Multi-Factor Authentication。 即使已在目錄上啟用 Multi-Factor Authentication 原則，攻擊者仍可使用較舊的通訊協定進行驗證，而略過 Multi-Factor Authentication。 

在租用戶中啟用安全性預設值之後，較舊通訊協定所發出的所有驗證要求將會遭到封鎖。 安全性預設值會封鎖 Exchange Active Sync 基本驗證。

> [!WARNING]
> 啟用安全性預設值之前，請確定系統管理員未使用舊版驗證通訊協定。 如需詳細資訊，請參閱[如何離開舊版驗證](concept-fundamentals-block-legacy-authentication.md)。

- [如何使用 Microsoft 365 設定多功能裝置或應用程式以傳送電子郵件](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>保護特殊權限動作

組織會使用透過 Azure Resource Manager API 管理的各種 Azure 服務，包括：

- Azure 入口網站 
- Azure PowerShell 
- Azure CLI

使用 Azure Resource Manager 來管理服務是高度具有特殊權限的動作。 Azure Resource Manager 可改變全租用戶的設定，例如服務設定和訂用帳戶計費。 單一要素驗證很容易遭受各種攻擊，例如網路釣魚和密碼噴濺。 

請務必驗證要存取 Azure Resource Manager 和更新設定的使用者身分識別。 您可在允許存取之前，先要求額外的驗證來驗證其身分識別。

在租用戶中啟用安全性預設值之後，任何存取 Azure 入口網站、Azure PowerShell 或 Azure CLI 的使用者都必須完成額外的驗證。 此原則適用於所有存取 Azure Resource Manager 的使用者，無論其為系統管理員或使用者。 

> [!NOTE]
> 2017 之前的 Exchange Online 租用戶預設會停用新式驗證。 為了避免在透過這些租用戶進行驗證時可能發生登入迴圈，您必須[啟用新式驗證](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

> [!NOTE]
> Azure AD Connect 同步處理帳戶會從安全性預設值排除，且不會提示註冊或執行多重要素驗證。 組織不應將此帳戶用於其他用途。

## <a name="deployment-considerations"></a>部署考量因素

下列其他考慮與安全性預設值的部署有關。

### <a name="authentication-methods"></a>驗證方法

這些免費的安全性預設值允許註冊和使用 Azure Multi-Factor Authentication，且**只使用利用通知的 Microsoft Authenticator 應用程式**。 條件式存取允許使用系統管理員選擇啟用的任何驗證方法。

| 方法 | 安全性預設值 | 條件式存取 |
| --- | --- | --- |
| 行動應用程式的通知 | X | X |
| 來自行動應用程式或硬體 Token 的驗證碼 | X** | X |
| 電話簡訊 |   | X |
| 電話通話 |   | X |
| 應用程式密碼 |   | X*** |

- ** 使用者可使用來自 Microsoft Authenticator 應用程式的驗證碼，但只能使用通知選項進行註冊。
- *** 只有在系統管理員啟用時，應用程式密碼才能用於傳統驗證案例的每一使用者 MFA。

### <a name="disabled-mfa-status"></a>已停用 MFA 狀態

如果組織先前是使用以使用者為基礎的 Azure Multi-Factor Authentication，則在查看 [多重要素驗證] 頁面時，如果沒有看到 [已啟用] 或 [已強制] 狀態的使用者，請勿驚慌。 [已停用] 狀態適合的使用者，是使用安全性預設值或以條件式存取為基礎的 Azure Multi-Factor Authentication 使用者。

### <a name="conditional-access"></a>條件式存取

您可使用條件式存取來設定與安全性預設值類似的原則，但可更加細微，包括安全性預設值中無法使用的使用者排除功能。 如果使用條件式存取並已在環境中啟用條件式存取原則，則無法使用安全性預設值。 如果授權提供條件式存取，但在環境中未啟用任何條件式存取原則，則可使用安全性預設值，直到啟用條件式存取原則為止。 如需 Azure AD 授權的詳細資訊，請參閱 [Azure AD 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

![警告訊息，您可使用安全性預設值或條件式存取，但不能兩者同時](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

以下逐步執行指南說明如何使用條件式存取來設定與安全性預設值啟用原則相等的原則：

- [要求系統管理員使用 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [要求 Azure 管理時使用 MFA](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [封鎖舊式驗證](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [要求所有使用者使用 MFA](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [要求 Azure MFA 註冊](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - 要求 Azure AD Premium P2 的 Azure AD Identity Protection 部分。

## <a name="enabling-security-defaults"></a>啟用安全性預設值

在目錄中啟用安全性預設值：

1. 以安全性系統管理員、條件式存取系統管理員或全域管理員的身分，登入  [Azure 入口網站](https://portal.azure.com) 。
1. 瀏覽至 [Azure Active Directory] ****  >[屬性] **** 。
1. 選取 [管理安全性預設值]。
1. 將 [啟用安全性預設值] 切換設為 [是]。
1. 選取 [儲存]。

## <a name="disabling-security-defaults"></a>停用安全性預設值

選擇實作條件式存取原則且取代安全性預設值的組織，必須停用安全性預設值。 

![警告訊息，停用安全性預設值，以啟用條件式存取](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

在目錄中停用安全性預設值：

1. 以安全性系統管理員、條件式存取系統管理員或全域管理員的身分，登入  [Azure 入口網站](https://portal.azure.com) 。
1. 瀏覽至 [Azure Active Directory] ****  >[屬性] **** 。
1. 選取 [管理安全性預設值]。
1. 將 [啟用安全性預設值] 切換設為 [否]。
1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

[一般條件式存取原則](../conditional-access/concept-conditional-access-policy-common.md)