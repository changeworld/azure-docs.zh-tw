---
title: 驗證方法和功能 - Azure Active Directory
description: 了解 Azure Active Directory 中可用的各種驗證方法和功能，協助改善和保護登入事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: a166a451c405c2321453e02751baad91c2a14c60
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840012"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory 中有哪些可用的驗證方法？

在 Azure Active Directory (Azure AD) 中的帳戶登入體驗中，使用者有不同的方式可自行驗證。 使用者名稱和密碼是使用者過去提供認證的最常見方式。 使用 Azure AD 中的新式驗證和安全性功能時，應該使用更安全的驗證方法來補充或取代基本密碼。

![Azure AD 中的優點和慣用驗證方法的表格](media/concept-authentication-methods/authentication-methods.png)

無密碼驗證方法（例如 Windows Hello、FIDO2 安全性金鑰和 Microsoft Authenticator 應用程式）提供最安全的登入事件。

Azure AD Multi-Factor Authentication 只會在使用者登入時，使用密碼新增額外的安全性。 系統會提示使用者輸入其他形式的驗證，例如回應推播通知、輸入軟體或硬體權杖中的程式碼，或回應 SMS 或來電。

若要簡化使用者的上線體驗，並同時註冊 MFA 和 SSPR，建議您 [啟用合併的安全性資訊註冊](howto-registration-mfa-sspr-combined.md)。 為了復原，建議您要求使用者註冊多個驗證方法。 當使用者在登入或 SSPR 期間無法使用一個方法時，他們可以選擇使用其他方法進行驗證。 如需詳細資訊，請參閱 [Azure AD 中的建立復原存取控制管理原則](concept-resilient-controls.md)。

## <a name="authentication-method-strength-and-security"></a>驗證方法強度和安全性

當您在組織中部署 Azure AD Multi-Factor Authentication 之類的功能時，請參閱可用的驗證方法。 根據安全性、可用性和可用性，選擇符合或超越您需求的方法。 可能的話，請使用具有最高層級安全性的驗證方法。

下表概述可用驗證方法的安全性考慮。 可用性是指使用者可以使用驗證方法，而不是 Azure AD 中服務可用性的指示：

| 驗證方法          | 安全性 | 可用性 | 可用性 |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello 企業版     | 高     | 高      | 高         |
| Microsoft Authenticator 應用程式    | 高     | 高      | 高         |
|  (預覽版的 FIDO2 安全性金鑰)    | 高     | 高      | 高         |
| OATH 硬體權杖 (預覽) | 適中   | 適中    | 高         |
| OATH 軟體權杖           | 適中   | 適中    | 高         |
| sms                            | 中型   | 高      | 中       |
| 語音                          | 適中   | 適中    | 適中       |
| 密碼                       | 低度      | 高      | 高         |

如需安全性的詳細資訊，請參閱 [驗證弱點和攻擊媒介](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)。

> [!TIP]
> 為了彈性和使用性，我們建議您使用 Microsoft Authenticator 應用程式。 此驗證方法可提供最佳的使用者體驗和多種模式，例如無密碼、MFA 推播通知和 OATH 代碼。

## <a name="how-each-authentication-method-works"></a>每個驗證方法的運作方式

當您登入應用程式或裝置時（例如使用 FIDO2 安全性金鑰或密碼），某些驗證方法可用來作為主要因素。 當您使用 Azure AD Multi-Factor Authentication 或 SSPR 時，其他驗證方法只會以次要因素的形式提供。

下表概述在登入事件期間可以使用驗證方法的時機：

| 方法                         | 主要驗證 | 次要驗證  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello 企業版     | 是                    | MFA                       |
| Microsoft Authenticator 應用程式    | 是 (預覽)          | MFA 和 SSPR              |
|  (預覽版的 FIDO2 安全性金鑰)    | 是                    | MFA                       |
| OATH 硬體權杖 (預覽) | 否                     | MFA                       |
| OATH 軟體權杖           | 否                     | MFA                       |
| sms                            | 是 (預覽)          | MFA 和 SSPR              |
| 語音通話                     | 否                     | MFA 和 SSPR              |
| 密碼                       | 是                    |                           |

所有這些驗證方法都可以在 Azure 入口網站中設定，而且越來越使用 [Microsoft Graph REST API Beta 版](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

若要深入瞭解每個驗證方法的運作方式，請參閱下列個別的概念性文章：

* [Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator 應用程式](concept-authentication-authenticator-app.md)
* [ (預覽版的 FIDO2 安全性金鑰) ](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH 硬體權杖 (預覽)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH 軟體權杖](concept-authentication-oath-tokens.md#oath-software-tokens)
* SMS 登 [入 (預覽版) ](howto-authentication-sms-signin.md) 和 [驗證](concept-authentication-phone-options.md#mobile-phone-verification)
* [語音通話驗證](concept-authentication-phone-options.md)
* 密碼

> [!NOTE]
> 在 Azure AD 中，密碼通常是其中一個主要驗證方法。 您無法停用密碼驗證方法。 如果您使用密碼作為主要驗證因素，請使用 Azure AD Multi-Factor Authentication 來提高登入事件的安全性。

下列其他驗證方法可用於某些案例：

* [應用程式密碼](howto-mfa-app-passwords.md) -用於不支援新式驗證的舊應用程式，且可針對每個使用者 Azure AD Multi-Factor Authentication 進行設定。
* [安全性問題](concept-authentication-security-questions.md) -僅用於 SSPR
* [電子郵件地址](concept-sspr-howitworks.md#authentication-methods) -僅用於 SSPR

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱 [自助式密碼重設的教學課程 (SSPR) ][tutorial-sspr] 和 [Azure AD Multi-Factor Authentication][tutorial-azure-mfa]。

若要深入了解 SSPR 概念，請參閱 [Azure AD 自助式密碼重設的運作方式][concept-sspr]。

若要深入瞭解 MFA 概念，請參閱 [Azure AD Multi-Factor Authentication 的運作方式][concept-mfa]。

深入瞭解如何使用 [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)來設定驗證方法。

若要查看使用中的驗證方法，請參閱使用 [PowerShell Azure AD Multi-Factor Authentication 驗證方法分析](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
