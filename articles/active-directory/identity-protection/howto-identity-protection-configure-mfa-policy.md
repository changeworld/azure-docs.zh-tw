---
title: 設定 MFA 註冊原則-Azure Active Directory Identity Protection
description: 了解如何設定 Azure AD Identity Protection 多重要素驗證註冊原則。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74382133"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>如何：設定 Azure 多重要素驗證註冊原則

Azure AD Identity Protection 可協助您管理 Azure 多因素驗證（MFA）註冊的導入，方法是設定條件式存取原則來要求進行 MFA 註冊，無論您登入的新式驗證應用程式為何。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>什麼是 Azure 多重要素驗證註冊原則？

Azure 多因素驗證提供一種方法來驗證您使用的不只是使用者名稱和密碼。 它提供使用者登入的第二層安全性。為了讓使用者能夠回應 MFA 提示，他們必須先註冊 Azure 多重要素驗證。

我們建議您針對使用者登入要求 Azure 多重要素驗證，因為它：

- 透過一系列驗證選項提供增強式驗證。
- 在準備您的組織從 Identity Protection 中的風險偵測進行自我補救時，扮演著重要的角色。

如需有關 Azure 多因素驗證的詳細資訊，請參閱[什麼是 Azure 多重要素驗證？](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>原則組態

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]。
1. 流覽至**Azure Active Directory** > **安全性** > 身分**識別保護** > **MFA 註冊原則**。
   1. 在 [**指派**] 底下
      1. **使用者**-選擇 [**所有使用者**] **，或選取個人和群組**（如果您要限制首度發行）。
         1. （選擇性）您可以選擇從原則中排除使用者。
   1. 在**控制項**底下
      1. 確定已核取 [**需要 AZURE MFA 註冊**] 核取方塊，然後選擇 [**選取**]。
   1. **強制執行原則** -  ** **
   1. **儲存**

## <a name="user-experience"></a>使用者體驗

Azure Active Directory Identity Protection 會在下次以互動方式登入時，提示您的使用者進行註冊，且會有14天的時間完成註冊。 在此14天的期間內，他們可以略過註冊，但是在一段期間結束時，他們必須先註冊，才能完成登入程式。

如需相關的使用者經驗概觀，請參閱︰

- [使用 Azure AD Identity Protection 時的登入體驗](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>後續步驟

- [啟用登入和使用者風險原則](howto-identity-protection-configure-risk-policies.md)

- [啟用 Azure AD 自助式密碼重設](../authentication/howto-sspr-deployment.md)

- [啟用 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
