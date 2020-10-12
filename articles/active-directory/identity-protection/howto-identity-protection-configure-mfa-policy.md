---
title: 設定 MFA 註冊原則-Azure Active Directory Identity Protection
description: 了解如何設定 Azure AD Identity Protection 多重要素驗證註冊原則。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c6b57eaa2a68906053faade48dd0e63fbf0db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464329"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>How To：設定 Azure Multi-Factor Authentication 註冊原則

Azure AD Identity Protection 可協助您管理 Azure Multi-Factor Authentication (MFA) 註冊的推出，方法是設定條件式存取原則來要求 MFA 註冊，無論您要登入的新式驗證應用程式為何。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>什麼是 Azure Multi-Factor Authentication 註冊原則？

Azure Multi-Factor Authentication 提供一種方法來驗證您所使用的使用者，不只是使用者名稱和密碼。 它可為使用者登入提供第二層安全性。為了讓使用者能夠回應 MFA 提示，他們必須先註冊 Azure Multi-Factor Authentication。

建議您將 Azure Multi-Factor Authentication 用於使用者登入，因為它：

- 透過一系列的驗證選項提供增強式驗證。
- 扮演重要的角色，讓您的組織準備好在 Identity Protection 中進行風險偵測的自我補救。

如需 Azure Multi-Factor Authentication 的詳細資訊，請參閱 [什麼是 azure Multi-Factor Authentication？](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>原則組態

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory**  >  **安全性**身分  >  **識別保護**  >  **MFA 註冊原則**。
   1. 在**指派**下
      1. **使用者** -選擇 [ **所有使用者** ] **，或選取 [個人和群組** ] 以限制您的首度發行。
         1. （選擇性）您可以選擇將使用者從原則中排除。
   1. 在**控制項**下
      1. 確定勾選 [ **需要註冊 Azure MFA** 登錄] 核取方塊，然後選擇 [ **選取**]。
   1. **強制執行原則**  - **開啟**
   1. **儲存**

## <a name="user-experience"></a>使用者體驗

Azure Active Directory Identity Protection 將會提示使用者在下次以互動方式登入時註冊，而且會有14天的時間完成註冊。 在14天的期間內，他們可以略過註冊，但是在期間結束時，他們必須先註冊，才能完成登入程式。

如需相關的使用者經驗概觀，請參閱︰

- [使用 Azure AD Identity Protection 時的登入體驗](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>接下來的步驟

- [啟用登入與使用者風險原則](howto-identity-protection-configure-risk-policies.md)

- [啟用 Azure AD 自助式密碼重設](../authentication/howto-sspr-deployment.md)

- [啟用 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
