---
title: 設定適用于 Windows 虛擬桌面的 Azure 多因素驗證-Azure
description: 如何設定 Azure 多重要素驗證，以提高 Windows 虛擬桌面的安全性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2527071aa246b34c103c2a8732d735f459977a47
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629519"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>啟用 Windows 虛擬桌面的 Azure 多重要素驗證

適用于 Windows 虛擬桌面的 Windows 用戶端是將 Windows 虛擬桌面與本機電腦整合的絕佳選項。 不過，當您將 Windows 虛擬桌面帳戶設定為 Windows 用戶端時，您必須採取一些措施來保護自己和使用者的安全。

當您第一次登入時，用戶端會要求您提供使用者名稱、密碼和 Azure MFA。 之後，當您下次登入時，用戶端將會在您的 Azure Active Directory （AD）企業應用程式中記住您的權杖。 當您選取 [**記住我**] 時，您的使用者可以在重新開機用戶端之後登入，而不需要重新輸入其認證。

雖然記住認證很方便，但它也可以讓企業案例或個人裝置上的部署更不安全。 若要保護您的使用者，您必須確定用戶端持續要求 Azure 多重要素驗證（MFA）認證。 本文將說明如何設定 Windows 虛擬桌面的條件式存取原則，以啟用此設定。

## <a name="prerequisites"></a>Prerequisites

以下是您需要的入門：

- 為使用者指派包含 Azure Active Directory Premium P1 或 P2 的授權。
- 已指派為群組成員之使用者的 Azure Active Directory 群組。
- 為您的所有使用者啟用 Azure MFA。 如需如何執行此動作的詳細資訊，請參閱[如何要求使用者使用雙步驟驗證](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)。

> [!NOTE]
> 下列設定也適用于[Windows 虛擬桌面 web 用戶端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

本節將說明如何建立條件式存取原則，以在連接到 Windows 虛擬桌面時需要多重要素驗證。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
2. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
3. 選取 [新增原則]****。
4. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
5. 在 [指派]  底下，選取 [使用者和群組]  。
   - 在 [**包含**] 底下，選取 [**選取使用者和群組** > ] [**使用者和群組**] > 選擇 [必要條件] 階段中建立的群組。
   - 選取 [完成]  。
6. 在 [**雲端應用程式或動作** > **包括**] 底下，選取 [**選取應用程式**]。
   - 選擇 [ **Windows 虛擬桌面**]，然後依序**選取**[] 和 [**完成**]。
   
     ![[雲端應用程式] 或 [動作] 頁面的螢幕擷取畫面。 Windows 虛擬桌面和 Windows 虛擬桌面用戶端應用程式會以紅色反白顯示。](media/cloud-apps-enterprise.png)

7. 在 **[存取控制** > **授**與] 底下，選取 **[授與存取權**]、[**需要多重要素驗證**]，然後**選取**。
8. 在 [**存取控制** > **會話**] 底下，選取 [登**入頻率**]，將值設定為**1** ，並將單位設為**小時**，然後選取 [**選取**]。
9. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
10. 選取 [**建立**] 以啟用您的原則。

## <a name="next-steps"></a>後續步驟

- [深入瞭解條件式存取原則](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [深入瞭解使用者登入頻率](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
