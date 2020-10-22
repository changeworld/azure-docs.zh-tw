---
title: 風險原則-Azure Active Directory Identity Protection
description: 在 Azure Active Directory Identity Protection 中啟用和設定風險原則
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
ms.openlocfilehash: 49f3e082969b7483601088cd976d8cc30d500017
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367460"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>如何：設定及啟用風險原則

如同我們在前一篇文章中所學到的身分 [識別保護原則](concept-identity-protection-policies.md) ，我們有兩個可以在目錄中啟用的風險原則。 

- 登入風險原則
- 使用者風險原則

![啟用使用者和登入風險原則的安全性總覽頁面](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

這兩個原則都可在您的環境中自動回應風險偵測，並可讓使用者在偵測到風險時自動進行補救。 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>先決條件 

如果您的組織想要允許使用者在偵測到風險時自行進行補救，則必須同時註冊自助密碼重設和 Azure Multi-Factor Authentication 的使用者。 建議您 [啟用合併的安全性資訊註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md) ，以獲得最佳體驗。 讓使用者能在不需要系統管理員介入的情況下，以更快的速度讓使用者進行自動補救。 系統管理員仍然可以看到這些事件，並在事實之後進行調查。 

## <a name="choosing-acceptable-risk-levels"></a>選擇可接受的風險層級

組織必須決定他們願意接受平衡使用者經驗和安全性狀態的風險層級。 

Microsoft 的建議是將 [使用者風險原則] 閾值設定為 [ **高** ]，並將 [登入風險] 原則設定為 [ **中] 或 [上**]。

選擇 [高] **** 臨界值可減少觸發原則的次數，並將對使用者的影響降至最低。 不過，它會從原則中排除 **低** 和 **中度** 風險偵測，這可能不會封鎖攻擊者利用遭盜用的身分識別。 選取 [ **低** ] 閾值會導致額外的使用者中斷，但會增加安全性狀態。

## <a name="exclusions"></a>排除

所有原則都允許排除使用者，例如您的[緊急存取或急用管理員帳戶](../roles/security-emergency-access.md)。 組織可能會根據帳戶的使用方式，判斷是否需要從特定原則中排除其他帳戶。 所有排除專案都應定期檢查，以查看是否仍適用。

某些風險偵測中的 Identity Protection 會使用設定的受信任 [網路位置](../conditional-access/location-condition.md) ，以減少誤報。

## <a name="enable-policies"></a>啟用原則

若要啟用使用者風險和登入風險原則，請完成下列步驟。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory**  >  **安全性**身分  >  **識別保護**  >  **總覽**。
1. 選取 [ **使用者風險原則**]。
   1. 在**指派**下
      1. **使用者** -選擇 [ **所有使用者** ] **，或選取 [個人和群組** ] 以限制您的首度發行。
         1. （選擇性）您可以選擇將使用者從原則中排除。
      1. **條件**  - **使用者風險**Microsoft 的建議是將此選項設定為 [**高**]。
   1. 在**控制項**下
      1. **存取** -Microsoft 的建議是 **允許存取** ，而且 **需要變更密碼**。
   1. **強制執行原則**  - **開啟**
   1. **儲存** -此動作會讓您回到 [ **總覽** ] 頁面。
1. 選取 [登 **入風險原則**]。
   1. 在**指派**下
      1. **使用者** -選擇 [ **所有使用者** ] **，或選取 [個人和群組** ] 以限制您的首度發行。
         1. （選擇性）您可以選擇將使用者從原則中排除。
      1. **條件**  - 登**入風險**Microsoft 的建議是將此選項設定為 [**中] 或更高**的版本。
   1. 在**控制項**下
      1. **存取** -Microsoft 的建議是 **允許存取** ，而且 **需要多重要素驗證**。
   1. **強制執行原則**  - **開啟**
   1. **儲存**

## <a name="next-steps"></a>後續步驟

- [啟用 Azure Multi-Factor Authentication 註冊原則](howto-identity-protection-configure-mfa-policy.md)

- [什麼是風險](concept-identity-protection-risks.md)

- [調查風險偵測](howto-identity-protection-investigate-risk.md)

- [模擬風險偵測](howto-identity-protection-simulate-risk.md)
