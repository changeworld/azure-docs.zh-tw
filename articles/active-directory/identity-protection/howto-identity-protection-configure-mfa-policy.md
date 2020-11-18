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
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835861"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>如何：設定 Azure AD Multi-Factor Authentication 註冊原則

Azure AD Identity Protection 藉由設定條件式存取原則來要求 MFA 註冊（無論您登入的新式驗證應用程式為何），以協助您管理 Azure AD Multi-Factor Authentication (MFA) 註冊的推出。

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>什麼是 Azure AD Multi-Factor Authentication 註冊原則？

Azure AD Multi-Factor Authentication 會提供一種方法來驗證您所使用的使用者，不只是使用者名稱和密碼。 它可為使用者登入提供第二層安全性。為了讓使用者能夠回應 MFA 提示，他們必須先註冊 Azure AD Multi-Factor Authentication。

建議您要求使用者登入 Azure AD Multi-Factor Authentication，因為它：

- 透過一系列的驗證選項提供增強式驗證。
- 扮演重要的角色，讓您的組織準備好在 Identity Protection 中進行風險偵測的自我補救。

如需有關 Azure AD Multi-Factor Authentication 的詳細資訊，請參閱[Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)的功能。

## <a name="policy-configuration"></a>原則組態

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 **Azure Active Directory**  >  **安全性** 身分  >  **識別保護**  >  **MFA 註冊原則**。
   1. 在 **指派** 下
      1. **使用者** -選擇 [ **所有使用者** ] **，或選取 [個人和群組** ] 以限制您的首度發行。
         1. （選擇性）您可以選擇將使用者從原則中排除。
   1. 在 **控制項** 下
      1. 確定核取 [ **需要 AZURE AD MFA 註冊** ] 核取方塊，然後選擇 [ **選取**]。
   1. **強制執行原則**  - **開啟**
   1. **儲存**

## <a name="user-experience"></a>使用者體驗

Azure Active Directory Identity Protection 將會提示使用者在下次以互動方式登入時註冊，而且會有14天的時間完成註冊。 在14天的期間內，他們可以略過註冊，但是在期間結束時，他們必須先註冊，才能完成登入程式。

如需相關的使用者經驗概觀，請參閱︰

- [使用 Azure AD Identity Protection 時的登入體驗](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>後續步驟

- [啟用登入與使用者風險原則](howto-identity-protection-configure-risk-policies.md)

- [啟用 Azure AD 自助式密碼重設](../authentication/howto-sspr-deployment.md)

- [啟用 Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
