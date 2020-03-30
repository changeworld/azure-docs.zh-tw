---
title: 配置 MFA 註冊策略 - Azure 活動目錄標識保護
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382133"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>如何：配置 Azure 多重要素驗證註冊策略

Azure AD 標識保護通過配置條件訪問策略來要求 MFA 註冊（無論您登錄到什麼現代身份驗證應用），可説明您管理 Azure 多重要素驗證 （MFA） 註冊的推出。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>什麼是 Azure 多重要素驗證註冊策略？

Azure 多重要素驗證提供了一種驗證使用誰的方法，而不僅僅是使用者名和密碼。 它為使用者登錄提供了第二層安全性。為了使使用者能夠回應 MFA 提示，他們必須首先註冊 Azure 多重要素驗證。

我們建議對使用者登錄需要 Azure 多重要素驗證，因為它：

- 通過一系列驗證選項提供增強式驗證。
- 在使組織能夠自我補救身份保護中的風險檢測方面發揮關鍵作用。

有關 Azure 多重要素驗證的詳細資訊，請參閱什麼是[Azure 多重要素驗證？](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>原則組態

1. 導航到[Azure 門戶](https://portal.azure.com)。
1. 流覽到**Azure 活動目錄** > **安全** > **標識保護** > **MFA 註冊策略**。
   1. 在**分配**下
      1. **使用者**- 如果限制推出，請選擇**所有使用者**或**選擇個人和組**。
         1. 可以選擇將使用者從策略中排除。
   1. 在**控制下**
      1. 確保選中"**需要 Azure MFA 註冊"** 核取方塊，然後**選擇"選擇**"。
   1. **執行策略** -  ** **
   1. **儲存**

## <a name="user-experience"></a>使用者體驗

Azure 活動目錄標識保護將提示使用者下次以對話模式登錄時進行註冊，並且他們有 14 天的時間完成註冊。 在這 14 天期間，他們可以繞過註冊，但在期限結束時，他們將需要註冊，然後才能完成登錄過程。

如需相關的使用者經驗概觀，請參閱︰

- [使用 Azure AD Identity Protection 時的登入體驗](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>後續步驟

- [啟用登錄和使用者風險策略](howto-identity-protection-configure-risk-policies.md)

- [啟用 Azure AD 自助服務密碼重設](../authentication/howto-sspr-deployment.md)

- [啟用 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
