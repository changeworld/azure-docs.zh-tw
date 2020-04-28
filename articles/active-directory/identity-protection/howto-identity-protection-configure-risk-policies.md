---
title: 風險原則-Azure Active Directory Identity Protection
description: 在 Azure Active Directory Identity Protection 中啟用和設定風險原則
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75707000"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>如何：設定及啟用風險原則

如我們在上一篇文章中所學到的身分[識別保護原則](concept-identity-protection-policies.md)，我們有兩個可在目錄中啟用的風險原則。 

- 登入風險原則
- 使用者風險原則

![啟用使用者和登入風險原則的安全性總覽頁面](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

這兩個原則都適用于將環境中風險偵測的回應自動化，並可讓使用者在偵測到風險時自行補救。 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Prerequisites 

如果您的組織想要允許使用者在偵測到風險時進行自我補救，則必須註冊自助式密碼重設和 Azure 多重要素驗證的使用者。 我們建議您[啟用結合的安全性資訊註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md)，以獲得最佳體驗。 允許使用者進行自我補救，讓他們更快速地回到生產力狀態，而不需要系統管理員介入。 系統管理員仍然可以看到這些事件，並在事實之後進行調查。 

## <a name="choosing-acceptable-risk-levels"></a>選擇可接受的風險層級

組織必須決定他們願意接受平衡使用者體驗和安全性狀態的風險層級。 

Microsoft 的建議是將使用者風險原則閾值設為 [**高**]，並將 [登入風險原則] 設定為 [**中] 和 [上**一層]。

選擇 [高] **** 臨界值可減少觸發原則的次數，並將對使用者的影響降至最低。 不過，它會從原則中排除**低**和**中度**風險偵測，這可能不會阻止攻擊者利用遭到入侵的身分識別。 選取 [**低**] 臨界值會引進額外的使用者中斷，但會增加安全性狀態。

## <a name="exclusions"></a>排除

所有的原則都允許排除使用者，例如您的[緊急存取或半透明的系統管理員帳戶](../users-groups-roles/directory-emergency-access.md)。 組織可能會根據帳戶的使用方式，判斷他們需要從特定原則排除其他帳戶。 所有的排除專案都應該定期檢查，以查看它們是否仍適用。

在某些風險偵測中，Identity Protection 會使用設定的受信任[網路位置](../conditional-access/location-condition.md)來減少誤報。

## <a name="enable-policies"></a>啟用原則

若要啟用使用者風險和登入風險原則，請完成下列步驟。

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]。
1. 流覽至**Azure Active Directory** > **安全性** > **識別保護** > ]**總覽**。
1. 選取 [**設定使用者風險原則**]。
   1. 在 [**指派**] 底下
      1. **使用者**-選擇 [**所有使用者**] **，或選取個人和群組**（如果您要限制首度發行）。
         1. （選擇性）您可以選擇從原則中排除使用者。
      1. **條件** - **使用者風險**Microsoft 的建議是將此選項設定為 [**高**]。
   1. 在**控制項**底下
      1. **存取**-Microsoft 的建議是**允許存取**，而且**需要變更密碼**。
   1. **強制執行原則** -  ** **
   1. **儲存**-此動作會讓您回到 [**總覽**] 頁面。
1. 選取 [**設定登入風險原則**]。
   1. 在 [**指派**] 底下
      1. **使用者**-選擇 [**所有使用者**] **，或選取個人和群組**（如果您要限制首度發行）。
         1. （選擇性）您可以選擇從原則中排除使用者。
      1. **條件** - 登**入風險**Microsoft 的建議是將此選項設定為 [**中] 和 [以上**]。
   1. 在**控制項**底下
      1. **存取**-Microsoft 的建議是**允許存取**，而且**需要多重要素驗證**。
   1. **強制執行原則** -  ** **
   1. **儲存**

## <a name="next-steps"></a>後續步驟

- [啟用 Azure 多重要素驗證註冊原則](howto-identity-protection-configure-mfa-policy.md)

- [什麼是風險](concept-identity-protection-risks.md)

- [調查風險偵測](howto-identity-protection-investigate-risk.md)

- [模擬風險偵測](howto-identity-protection-simulate-risk.md)
