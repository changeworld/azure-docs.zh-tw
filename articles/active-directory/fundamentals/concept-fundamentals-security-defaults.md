---
title: Azure Active Directory 安全性預設值
description: 安全性預設原則，可協助保護組織免于遭受常見的攻擊
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb6170aa54c286a5a2d8353c1dd951859fdf8a0
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024580"
---
# <a name="what-are-security-defaults"></a>什麼是安全性預設值？

當常見的身分識別相關攻擊變得越來越普及時，管理安全性可能會很棘手。 這些攻擊包括密碼噴灑、重新執行和網路釣魚。

Azure Active Directory （Azure AD）中的安全性預設值可讓您更輕鬆地保護組織的安全並協助保護。 安全性預設值包含常見攻擊的預先設定安全性設定。 

Microsoft 將安全性預設值提供給所有人。 目標是要確保所有組織都已啟用基本層級的安全性，而不需要額外成本。 您會開啟 Azure 入口網站中的安全性預設值。

![Azure 入口網站的螢幕擷取畫面，具有切換以啟用安全性預設值](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> 如果您的租使用者是在2019年10月22日日或之後建立的，您可能會遇到新的預設安全行為，並已在您的租使用者中啟用安全性預設值。 為了保護所有的使用者，系統會將安全性預設值推出至所有建立的新租使用者。

如需有關如何提供安全性預設值的詳細資訊，請參閱 Alex Weinert 的 blog 文章，[安全性預設值簡介](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)。

## <a name="unified-multi-factor-authentication-registration"></a>統一的多重要素驗證註冊

您租使用者中的所有使用者都必須以 Azure 多因素驗證服務的形式註冊多重要素驗證（MFA）。 使用者有14天的時間，可以使用 Microsoft Authenticator 應用程式註冊多重要素驗證。 經過14天之後，使用者將無法登入，直到多重要素驗證註冊完成為止。

我們瞭解某些使用者可能不在辦公室，也不會在啟用安全性預設值之後的14天內登入。 為確保每個使用者有充足的時間來註冊多重要素驗證，每個使用者的14天期間都是唯一的。 在您啟用安全性預設值之後，使用者的14天期間會開始第一次成功互動式登入。

## <a name="multi-factor-authentication-enforcement"></a>強制執行多重要素驗證

### <a name="protecting-administrators"></a>保護系統管理員

具有特殊許可權帳戶存取權的使用者，可以提高您環境的存取權。 這些帳戶具有強大權力，您應特別小心處理。 有一種常見的方法可以改善特殊許可權帳戶的保護，就是需要更強的帳戶驗證形式來進行登入。 在 Azure AD 中，您可以藉由要求多重要素驗證，來取得更強的帳戶驗證。

完成多重要素驗證的註冊之後，下列九個 Azure AD 系統管理員角色將需要在每次登入時執行額外的驗證：

- 全域管理員
- SharePoint 管理員
- Exchange 系統管理員
- 條件式存取系統管理員
- 安全性系統管理員
- 服務台管理員或密碼管理員
- 計費管理員
- 使用者管理員
- 驗證管理員

### <a name="protecting-all-users"></a>保護所有使用者

我們通常會將系統管理員帳戶視為唯一需要額外驗證層的帳戶。 系統管理員有廣泛的機密資訊存取權，而且可以對全訂用帳戶的設定進行變更。 但是攻擊者通常是以終端使用者為目標。 

這些攻擊者取得存取權之後，就可以代表原始帳戶持有者要求存取授權資訊。 他們甚至可以下載整個目錄，在整個組織中執行網路釣魚攻擊。 

為所有使用者改善保護的其中一個常見方法，就是針對所有人都需要更強大的帳戶驗證形式，例如多重要素驗證。 使用者完成多重要素驗證註冊之後，系統會在必要時提示他們進行其他驗證。

### <a name="blocking-legacy-authentication"></a>封鎖舊版驗證

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure AD 支援各種驗證通訊協定，包括舊版驗證。 *舊版驗證*是指由下列各項所提出的驗證要求：

- 不使用新式驗證的舊版 Office 用戶端（例如，Office 2010 用戶端）。
- 任何使用舊版郵件通訊協定（如 IMAP、SMTP 或 POP3）的用戶端。

現今，大部分的登入嘗試都會來自舊版驗證。 舊版驗證不支援多重要素驗證。 即使您已在目錄上啟用多重要素驗證原則，攻擊者仍可使用較舊的通訊協定進行驗證，並略過多重要素驗證。 

在您的租使用者中啟用安全性預設值之後，較舊的通訊協定所發出的所有驗證要求將會遭到封鎖。 安全性預設值不會封鎖 Exchange ActiveSync。

> [!WARNING]
> 啟用安全性預設值之前，請確定您的系統管理員未使用舊版驗證通訊協定。 如需詳細資訊，請參閱[如何從舊版驗證移開](concept-fundamentals-block-legacy-authentication.md)。

### <a name="protecting-privileged-actions"></a>保護特殊許可權動作

組織會使用透過 Azure Resource Manager API 管理的各種 Azure 服務，包括：

- Azure Portal 
- Azure PowerShell 
- Azure CLI

使用 Azure Resource Manager 來管理您的服務是具有高許可權的動作。 Azure Resource Manager 可以改變整個租使用者的設定，例如服務設定和訂用帳戶計費。 單一要素驗證容易遭受各種攻擊，例如網路釣魚和密碼噴灑。 

請務必確認要存取 Azure Resource Manager 和更新設定的使用者身分識別。 您可以在允許存取之前，先要求額外的驗證來驗證其身分識別。

在您的租使用者中啟用安全性預設值之後，任何存取 Azure 入口網站、Azure PowerShell 或 Azure CLI 的使用者都必須完成額外的驗證。 此原則適用于所有存取 Azure Resource Manager 的使用者，無論他們是系統管理員或使用者。 

如果使用者未註冊多重要素驗證，使用者將需要使用 Microsoft Authenticator 應用程式進行註冊，才能繼續進行。 將不會提供14天的多重要素驗證註冊期間。

2017之前的 Exchange Online 租使用者預設會停用新式驗證。 為了避免在透過這些租使用者進行驗證時，登入迴圈的可能性，您必須[啟用新式驗證](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

> [!NOTE]
> Azure AD Connect 同步處理帳戶會從安全性預設中排除，且不會提示您註冊或執行多重要素驗證。 組織不應將此帳戶用於其他用途。

## <a name="deployment-considerations"></a>部署考量因素

下列其他考慮與部署您租使用者的安全性預設值有關。

### <a name="authentication-methods"></a>驗證方法

安全性預設值只允許透過**使用通知的 Microsoft Authenticator 應用程式**來註冊和使用 Azure 多重要素驗證。 條件式存取允許使用系統管理員選擇啟用的任何驗證方法。

|   | 安全性預設值 | 條件式存取 |
| --- | --- | --- |
| 行動應用程式的通知 | X | X |
| 來自行動應用程式或硬體 Token 的驗證碼 |   | X |
| 電話簡訊 |   | X |
| 電話通話 |   | X |
| 應用程式密碼 |   | X * * |

\* * 只有在系統管理員啟用時，應用程式密碼才會在具有傳統驗證案例的每一使用者 MFA 中提供。

### <a name="conditional-access"></a>條件式存取

您可以使用條件式存取來設定與安全性預設值類似的原則，但更多的細微性包括不會出現在安全性預設值中的使用者排除。 如果您使用條件式存取，並在您的環境中啟用條件式存取原則，則不會提供安全性預設值。 如果您的授權提供條件式存取，但未在您的環境中啟用任何條件式存取原則，您就可以使用安全性預設值，直到您啟用條件式存取原則為止。 如需 Azure AD 授權的詳細資訊，請參閱[Azure AD 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

![警告訊息，您可以同時擁有安全性預設值或條件式存取](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

以下是如何使用條件式存取來設定對等原則的逐步指南：

- [系統管理員需要 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Azure 管理需要 MFA](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [封鎖舊版驗證](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [所有使用者都需要 MFA](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [需要 AZURE MFA 註冊](../identity-protection/howto-identity-protection-configure-mfa-policy.md)-需要 Azure AD Identity Protection

## <a name="enabling-security-defaults"></a>啟用安全性預設值

若要在您的目錄中啟用安全性預設值：

1. 以安全性系統管理員、條件式存取系統管理員或全域管理員身分登入 [Azure 入口網站](https://portal.azure.com) 。
1. 流覽至 **Azure Active Directory** > **屬性**。
1. 選取 [**管理安全性預設值**]。
1. 將 [**啟用安全性預設值**] 切換為 **[是]** 。
1. 選取 [儲存]。

## <a name="disabling-security-defaults"></a>停用安全性預設值

選擇要執行取代安全性預設值之條件式存取原則的組織，必須停用安全性預設值。 

![警告訊息停用安全性預設值以啟用條件式存取](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

若要停用目錄中的安全性預設值：

1. 以安全性系統管理員、條件式存取系統管理員或全域管理員身分登入 [Azure 入口網站](https://portal.azure.com) 。
1. 流覽至 **Azure Active Directory** > **屬性**。
1. 選取 [**管理安全性預設值**]。
1. 將 [**啟用安全性預設值**] 切換為 [**否**]。
1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

[一般條件式存取原則](../conditional-access/concept-conditional-access-policy-common.md)
