---
title: 設定 [保持登入嗎？] 提示要求 Azure Active Directory 帳戶
description: 瞭解如何讓我保持登入 (KMSI) ，以顯示保持登入的資訊？提示、如何在 Azure Active Directory 入口網站中進行設定，以及如何針對登入問題進行疑難排解。
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320251"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>設定 [保持登入嗎？] 提示要求 Azure AD 帳戶

讓我保持登入 (KMSI) 在使用者成功登入之後，顯示 [ **保持登入？** ] 提示。 如果使用者對此提示回答 **[是]** ，讓我保持登入服務會為他們提供持續性的重新整理 [權杖](../develop/developer-glossary.md#refresh-token)。 若為同盟租使用者，則會在使用者成功向同盟身分識別服務驗證之後顯示提示。

下圖顯示受管理租使用者和同盟租使用者的使用者登入流程，以及新的 [讓我保持登入] 提示。 如果機器學習系統偵測到高風險的登入或從共用裝置登入，則此流程包含智慧型邏輯，因此不會顯示 [ **保持登入？** ] 選項。

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="顯示受控與同盟租使用者的使用者登入流程圖表":::

> [!NOTE]
> 設定 [讓我保持登入] 選項會要求您使用 Azure Active Directory (Azure AD) Premium 1、Premium 2 或 Basic edition，或具有 Microsoft 365 授權。 如需授權和版本的詳細資訊，請參閱[註冊 Azure AD Premium](active-directory-get-started-premium.md)。<br><br>Azure AD Premium 和 Basic 版本適用于使用全球 Azure AD 實例的中國客戶。 由 21Vianet 在中國營運的 Azure 服務目前不支援 Azure AD Premium 和 Basic 版本。 如需詳細資訊，請使用 [Azure AD 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)來與我們討論。

## <a name="configure-kmsi"></a>設定 KMSI

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取 **Azure Active Directory**，選取 [ **公司商標**]，然後選取 [ **設定**]。
1. 在 [ **Advanced settings** ] 區段中，尋找 [ **顯示] 選項以保持登入** 設定。

   這項設定可讓您選擇使用者是否保持登入 Azure AD，直到他們明確登出為止。
   * 如果您選擇 [ **否**]，在使用者成功登入之後，就會隱藏 [ **保持登入？** ] 選項，而且使用者必須在每次關閉並重新開啟瀏覽器時登入。
   * 如果您選擇 [ **是]**，則會向使用者顯示 [ **保持登入？** ] 選項。

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="顯示受控與同盟租使用者的使用者登入流程圖表":::

## <a name="troubleshoot-sign-in-issues"></a>對登入問題進行疑難排解

如果使用者不會在 [ **保持登入嗎？** ] 提示中採取動作，如下圖所示，但放棄登入嘗試，您將會看到指出中斷的登入記錄專案。

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="顯示受控與同盟租使用者的使用者登入流程圖表":::

登入錯誤的詳細資料如下所示，並在範例中反白顯示。

* 登**入錯誤碼**：50140
* **失敗原因**：由於使用者登入時「讓我保持登入」的中斷，導致此錯誤發生。

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="顯示受控與同盟租使用者的使用者登入流程圖表":::

您可以在 [advanced 商標] 設定中，將 [ **顯示] 選項設定為** [ **否** ]，以防止使用者看到中斷。 這樣會停用 Azure AD 目錄中所有使用者的 KMSI 提示。

您也可以在條件式存取中使用持續性瀏覽器會話控制項，以防止使用者看到 KMSI 提示。 此選項可讓您停用使用者群組 (的 KMSI 提示，例如全域系統管理員) ，而不會影響目錄中其餘使用者的登入行為。 如需詳細資訊，請參閱 [使用者登入頻率](../conditional-access/howto-conditional-access-session-lifetime.md)。 

為了確保 KMSI 提示只會在它可讓使用者受益時顯示，KMSI 提示會刻意未顯示在下列案例中：

* 使用者透過無縫 SSO 登入，並整合式 Windows 驗證 (IWA) 
* 使用者透過 Active Directory 同盟服務和 IWA 登入
* 使用者是租使用者中的來賓
* 使用者的風險分數很高
* 在使用者或系統管理員同意流程期間進行登入
* 在條件式存取原則中設定持續性瀏覽器會話控制項

## <a name="next-steps"></a>後續步驟

深入瞭解影響登入會話超時的其他設定：

* Microsoft 365 – [閒置會話超時](/sharepoint/sign-out-inactive-users)
* Azure AD 條件式存取- [使用者登入頻率](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure 入口網站– [目錄層級的非啟用時間](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)