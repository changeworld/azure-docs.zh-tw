---
title: 條件式存取基準原則-Azure Active Directory
description: 基準條件式存取原則，以保護組織免于遭受常見的攻擊
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75767563"
---
# <a name="what-are-baseline-policies"></a>什麼是基準原則？

基準原則是一組預先定義的原則，可協助組織抵禦許多常見的攻擊。 這些常見的攻擊可能包括密碼噴灑、重播和網路釣魚。 基準原則適用于所有版本的 Azure AD。 Microsoft 將這些基準保護原則提供給所有人使用的原因，是因為以身分識別為基礎的攻擊在過去幾年一直持續攀升。 這四個原則的目標是要確保所有組織都已啟用基準層級的安全性，而不需要額外成本。

管理自訂的條件式存取原則需要 Azure AD Premium 授權。

> [!IMPORTANT]
> 基準原則即將淘汰。 如需詳細資訊，請參閱[Azure Active Directory 的新功能？](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults) 。

## <a name="baseline-policies"></a>基準原則

![Azure 入口網站中的條件式存取基準原則](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

有四個基準原則：

* 需要系統管理員的 MFA （預覽）
* 終端使用者保護（預覽）
* 封鎖舊版驗證（預覽）
* 服務管理需要 MFA （預覽）

這四種原則都會影響舊版的驗證流程，例如 POP、IMAP 和較舊的 Office 桌面用戶端。

### <a name="exclusions"></a>排除

當基準原則進入其初始公開預覽時，可以選擇從原則中排除使用者。 這項功能已透過預覽演變，並于2019年7月移除。 已經建立排除專案的組織可以繼續保留新的使用者無法將排除專案新增至原則。

### <a name="require-mfa-for-admins-preview"></a>需要系統管理員的 MFA （預覽）

由於系統管理員帳戶擁有的能力和存取權，因此您應該特別小心對待它們。 若要改善特殊許可權帳戶的保護，其中一個常見的方法是在用來登入時需要更強大的帳戶驗證形式。 在 Azure Active Directory 中，您可以要求系統管理員註冊並使用 Azure 多重要素驗證，以取得更強的帳戶驗證。

[要求系統管理員使用 MFA （預覽）] 是一項基準原則，其需要下列目錄角色的多重要素驗證（MFA），視為最具許可權的 Azure AD 角色：

* 全域管理員
* SharePoint 管理員
* Exchange 系統管理員
* 條件式存取系統管理員
* 安全性系統管理員
* 服務台管理員/密碼管理員
* 計費管理員
* 使用者管理員

如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將它們取代為[受管理的身分識別](../managed-identities-azure-resources/overview.md)。

### <a name="end-user-protection-preview"></a>終端使用者保護（預覽）

高特殊許可權的系統管理員並不是攻擊的唯一目標。 不良執行者通常會以一般使用者為目標。 取得存取權之後，這些不良的執行者就可以代表原始帳戶持有者要求存取許可權資訊，或下載整個目錄並在整個組織中執行網路釣魚攻擊。 有一個改善所有使用者保護的常見方法，就是在偵測到有風險的登入時，需要更強大的帳戶驗證形式。

**終端使用者保護（預覽）** 是保護目錄中所有使用者的基準原則。 若要啟用此原則，所有使用者都必須在14天內註冊 Azure 多重要素驗證。 註冊之後，只有在有風險的登入嘗試時，才會提示使用者進行 MFA。 遭盜用的使用者帳戶會遭到封鎖，直到重設密碼和風險解除為止。 

> [!NOTE]
> 先前標示為有風險的任何使用者都會遭到封鎖，直到發生密碼重設和在原則啟用關閉的風險為止。

### <a name="block-legacy-authentication-preview"></a>封鎖舊版驗證（預覽）

舊版的驗證通訊協定（例如： IMAP、SMTP、POP3）是通常由較舊的郵件用戶端用來進行驗證的通訊協定。 舊版通訊協定不支援多重要素驗證。 即使您的原則要求您的目錄需要多重要素驗證，不良的執行者還是可以使用其中一種舊版通訊協定進行驗證，並略過多重要素驗證。

保護您的帳戶免于舊版通訊協定所提出之惡意驗證要求的最佳方式，就是封鎖它們。

「**封鎖舊版驗證」（預覽）** 基準原則會封鎖使用舊版通訊協定所提出的驗證要求。 必須使用新式驗證才能成功登入所有使用者。 與其他基準原則一起使用時，將會封鎖來自舊版通訊協定的要求。 此外，所有使用者都需要在需要時進行 MFA。 此原則不會封鎖 Exchange ActiveSync。

### <a name="require-mfa-for-service-management-preview"></a>服務管理需要 MFA （預覽）

組織會使用各種不同的 Azure 服務，並從以 Azure Resource Manager 為基礎的工具進行管理，例如：

* Azure 入口網站
* Azure PowerShell
* Azure CLI

使用上述任何工具來執行資源管理是一項具有高度許可權的動作。 這些工具可以改變全訂用帳戶的設定，例如服務設定和訂用帳戶計費。

為了保護特殊許可權動作，這**需要服務管理（預覽）** 原則的 MFA，會要求任何存取 Azure 入口網站、Azure PowerShell 或 Azure CLI 的使用者進行多重要素驗證。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱：

* [啟用安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)
* [一般條件式存取原則](concept-conditional-access-policy-common.md)
* [可保護身分識別基礎結構的五個步驟](../../security/fundamentals/steps-secure-identity.md)
