---
title: Azure 活動目錄安全性預設值
description: 安全預設策略,可幫助保護組織免受常見攻擊
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f307553a97973d03b0699248373e53e4845aa39a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869907"
---
# <a name="what-are-security-defaults"></a>什麼是安全默認值?

當常見的與身份相關的攻擊越來越流行時,管理安全性可能很困難。 這些攻擊包括密碼噴射、重播和網路釣魚。

Azure 活動目錄 (Azure AD) 中的安全預設值使安全性更加容易,並有助於保護組織。 安全預設值包含常見攻擊的預配置安全設置。 

微軟正在使安全預設值可供所有人使用。 目標是確保所有組織都啟用基本安全級別,無需額外費用。 在 Azure 門戶中打開安全預設值。

![啟用安全預設值的 Azure 門戶的螢幕截圖,](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> 如果您的租戶是在 2019 年 10 月 22 日或之後創建的,則可能是您遇到新的按預設安全行為,並且已在租戶中啟用了安全默認值。 為了保護所有使用者,安全預設值正在推廣到創建的所有新租戶。

有關安全預設值為何可用的更多詳細資訊,請參閱 Alex Weinert 的部落格文章"[介紹安全默認值](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)"。

## <a name="unified-multi-factor-authentication-registration"></a>統一多重身份驗證註冊

租戶中的所有使用者都必須以 Azure 多重身份驗證服務的形式註冊多重身份驗證 (MFA)。 使用者有 14 天的時間使用 Microsoft 身份驗證器應用註冊多重身份驗證。 14天后,在多因素身份驗證註冊完成之前,使用者將無法登錄。

我們理解,某些使用者可能在啟用安全預設值后的 14 天內外出或無法登錄。 為了確保每個使用者都有充足的時間註冊多重身份驗證,14 天的期限對於每個使用者都是唯一的。 使用者 14 天的時間從啟用安全預設值後首次成功交互登錄後開始。

## <a name="multi-factor-authentication-enforcement"></a>多重身份驗證

### <a name="protecting-administrators"></a>保護管理員

有權存取特權帳戶的使用者增加了對您環境的訪問。 這些帳戶具有強大權力，您應特別小心處理。 改進特權帳戶保護的一個常見方法是要求對登錄進行更強有力的帳戶驗證。 在 Azure AD 中,可以通過要求多重身份驗證來獲得更強的帳戶驗證。

完成對多重身份驗證的註冊后,每次登錄時都需要以下九個 Azure AD 管理員角色執行其他身份驗證:

- 全域管理員
- SharePoint 管理員
- Exchange 系統管理員
- 條件式存取系統管理員
- 安全性系統管理員
- 說明管理者或密碼管理員
- 計費管理員
- 使用者管理員
- 驗證管理員

### <a name="protecting-all-users"></a>保護所有使用者

我們傾向於認為管理員帳戶是唯一需要額外身份驗證層的帳戶。 管理員可以廣泛訪問敏感資訊,並可以更改訂閱範圍的設置。 但是攻擊者往往以最終用戶為目標。 

這些攻擊者獲得訪問許可權後,他們可以代表原始帳戶持有人請求訪問特權資訊。 他們甚至可以下載整個目錄,對整個組織執行網路釣魚攻擊。 

改善對所有使用者保護的一個常見方法是要求每個人進行更強有力的帳戶驗證,例如多重身份驗證。 使用者完成多重身份驗證註冊后,將在必要時提示他們進行其他身份驗證。

### <a name="blocking-legacy-authentication"></a>封鎖舊版身份驗證

為了方便使用者存取雲應用,Azure AD 支援各種身份驗證協定,包括舊版身份驗證。 *舊身份驗證*是一個術語,它是指由:

- 不使用現代身份驗證的用戶端(例如,Office 2010 用戶端)。
- 使用較舊的郵件協定(如 IMAP、SMTP 或 POP3)的任何用戶端。

如今,大多數妥協登錄嘗試都來自舊版身份驗證。 舊身份驗證不支援多重身份驗證。 即使您在目錄中啟用了多重身份驗證策略,攻擊者也可以使用較舊的協定進行身份驗證並繞過多重身份驗證。 

在租戶中啟用安全預設值后,舊協定發出的所有身份驗證請求都將被阻止。 安全預設值阻止交換活動同步基本身份驗證。

> [!WARNING]
> 在啟用安全預設值之前,請確保管理員不使用較舊的身份驗證協定。 有關詳細資訊,請參閱[如何遠離舊版身份驗證](concept-fundamentals-block-legacy-authentication.md)。

### <a name="protecting-privileged-actions"></a>保護權限操作

組織使用透過 Azure 資源管理員 API 管理的各種 Azure 服務,包括:

- Azure 入口網站 
- Azure PowerShell 
- Azure CLI

使用 Azure 資源管理員管理服務是一項高度特權的操作。 Azure 資源管理員可以更改租戶範圍的配置,如服務設置和訂閱計費。 單因素身份驗證容易受到網路釣魚和密碼噴淋等多種攻擊的影響。 

請務必驗證想要訪問 Azure 資源管理員並更新配置的使用者的身份。 在允許訪問之前,通過要求其他身份驗證來驗證其身份。

在租戶中啟用安全預設值後,任何訪問 Azure 門戶、Azure PowerShell 或 Azure CLI 的使用者都需要完成其他身份驗證。 此策略適用於訪問 Azure 資源管理器的所有使用者,無論他們是管理員還是使用者。 

> [!NOTE]
> 2017 年前 Exchange Online 租戶預設禁用了現代身份驗證。 為了避免通過這些租戶進行身份驗證時出現登錄迴圈的可能性,必須[啟用現代身份驗證](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

> [!NOTE]
> Azure AD Connect 同步帳戶從安全預設值中排除,不會提示註冊或執行多重身份驗證。 組織不應出於其他目的使用此帳戶。

## <a name="deployment-considerations"></a>部署考量因素

以下其他注意事項與租戶的安全預設值的部署有關。

### <a name="authentication-methods"></a>驗證方法

安全預設值允許**僅使用使用通知的 Microsoft 身份驗證器應用**註冊和使用 Azure 多重身份驗證。 條件訪問允許使用管理員選擇啟用的任何身份驗證方法。

|   | 安全性預設值 | 條件式存取 |
| --- | --- | --- |
| 行動應用程式的通知 | X | X |
| 來自行動應用程式或硬體 Token 的驗證碼 |   | X |
| 電話簡訊 |   | X |
| 電話通話 |   | X |
| 應用程式密碼 |   | X* |

• 應用密碼僅在每個使用者 MFA 中可用,並且只有在管理員啟用的情況下才具有舊版身份驗證方案。

### <a name="conditional-access"></a>條件式存取

可以使用條件訪問配置類似於安全預設值的策略,但具有更粒度的策略,包括使用者排除,這在安全默認值中不可用。 如果您正在使用條件訪問並在環境中啟用條件訪問策略,則安全預設值將不可用。 如果您擁有提供條件訪問但未在環境中啟用任何條件訪問策略的許可證,則歡迎在啟用條件訪問策略之前使用安全預設值。 有關 Azure AD 授權的詳細資訊,請參閱[Azure AD 定價頁](https://azure.microsoft.com/pricing/details/active-directory/)。

![警告訊息,您可以具有安全預設值或條件存取,而不是兩者](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

以下是有關如何使用條件存取配置等效原則的分步指南:

- [需要管理員的 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [需要 Azure 管理的 MFA](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [封鎖舊式驗證](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [要求所有使用者使用 MFA](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [需要 Azure MFA 註冊](../identity-protection/howto-identity-protection-configure-mfa-policy.md)- 需要 Azure AD 識別保護

## <a name="enabling-security-defaults"></a>開啟安全性預設值

在目錄中啟用安全預設值,請進行:

1. 以安全管理員、條件存取管理員或全域管理員的身份登入 [Azure 門戶](https://portal.azure.com) 。
1. 瀏覽到 **Azure 的目錄** > **屬性**。
1. 選擇 **「管理安全預設值**」 。
1. 將**啟用安全預設值**切換為 **「是**」。
1. 選取 [儲存]  。

## <a name="disabling-security-defaults"></a>停用安全預設值

選擇實施替換安全預設值的條件訪問策略的組織必須禁用安全預設值。 

![警告訊息停用安全預設值以啟用條件存取](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

要關閉目錄中的安全性預設值,請進行:

1. 以安全管理員、條件存取管理員或全域管理員的身份登入 [Azure 門戶](https://portal.azure.com) 。
1. 瀏覽到 **Azure 的目錄** > **屬性**。
1. 選擇 **「管理安全預設值**」 。
1. 將**啟用安全預設值**設置為 **「否**」。
1. 選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

[一般條件式存取原則](../conditional-access/concept-conditional-access-policy-common.md)
