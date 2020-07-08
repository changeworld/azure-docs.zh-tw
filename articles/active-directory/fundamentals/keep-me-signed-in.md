---
title: 設定「保持登入？」 Azure Active Directory 帳戶的提示
description: 瞭解 [讓我保持登入（KMSI）]，這會顯示保持登入的情況？提示字元、如何在 Azure Active Directory 入口網站中進行設定，以及如何對登入問題進行疑難排解。
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
ms.openlocfilehash: fd24e6847dbf02bc7efe5d9e6ea02043879f720b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054707"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>設定「保持登入？」 Azure AD 帳戶的提示

[讓我保持登入（KMSI）] 會顯示 [**保持登入**]，在使用者成功登入後提示。 如果使用者對此提示回答 **[是]** ，則 [讓我保持登入] 服務會為他們提供持續性重新整理[權杖](../develop/developer-glossary.md#refresh-token)。 針對同盟租使用者，系統會在使用者成功驗證同盟身分識別服務之後顯示提示。

下圖顯示受管理租使用者和同盟租使用者的使用者登入流程，以及新的 [讓我保持登入] 提示。 此流程包含智慧型邏輯，如果機器學習系統偵測到來自共用裝置的高風險登入或登入，則不會顯示 [**保持登入？** ] 選項。

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="此圖顯示受管理與同盟租使用者的使用者登入流程":::

> [!NOTE]
> 設定 [讓我保持登入] 選項，會要求您使用 Azure Active Directory （Azure AD） Premium 1、Premium 2 或 Basic 版本，或擁有 Microsoft 365 授權。 如需授權和版本的詳細資訊，請參閱[註冊 Azure AD Premium](active-directory-get-started-premium.md)。<br><br>Azure AD Premium 和 Basic 版本適用于使用全球 Azure AD 實例的中國客戶。 由 21Vianet 在中國營運的 Azure 服務目前不支援 Azure AD Premium 和 Basic 版本。 如需詳細資訊，請使用[Azure AD 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)與我們交談。

## <a name="configure-kmsi"></a>設定 KMSI

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取 [ **Azure Active Directory**]，選取 [**公司商標**]，然後選取 [**設定**]。
1. 在 [ **Advanced settings** ] （設定）區段中，尋找 [**顯示] 選項以保持登入**設定。

   此設定可讓您選擇是否要在使用者明確登出之前，保持登入 Azure AD。
   * 如果您選擇 [**否**]，則會在使用者成功登入後隱藏 [**保持登入？** ] 選項，而且使用者必須在每次關閉並重新開啟瀏覽器時登入。
   * 如果您選擇 [**是]**，則會向使用者顯示 [**保持登入？** ] 選項。

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="螢幕擷取畫面顯示 [顯示] 選項以保持登入設定":::

## <a name="troubleshoot-sign-in-issues"></a>對登入問題進行疑難排解

如果使用者不作用於 [**保持登入**] 提示字元（如下圖所示），但放棄登入嘗試，您會看到指出中斷的登入記錄專案。

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="顯示保持登入的情況？及時":::

登入錯誤的詳細資料如下所示，並在範例中反白顯示。

* 登**入錯誤碼**：50140
* **失敗原因**：由於使用者登入時「讓我保持登入」中斷，因此發生此錯誤。

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="具有「讓我保持登入」中斷的範例登入記錄專案":::

您可以將 [advanced 商標] 設定中的 [**顯示] 選項設定為**[**否**]，以防止使用者看到中斷。 這會停用 Azure AD 目錄中所有使用者的 KMSI 提示。

您也可以在條件式存取中使用持續性瀏覽器會話控制項，以防止使用者看到 KMSI 提示字元。 此選項可讓您停用所選使用者群組（例如全域系統管理員）的 KMSI 提示，而不會影響目錄中剩餘使用者的登入行為。 如需詳細資訊，請參閱[使用者登入頻率](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。 

為確保只有在可以讓使用者受益時，才會顯示 KMSI 提示，在下列案例中，不會顯示 KMSI 提示：

* 使用者已透過無縫 SSO 和整合式 Windows 驗證（IWA）登入
* 使用者已透過 Active Directory 同盟服務和 IWA 登入
* 使用者是租使用者中的來賓
* 使用者的風險分數高
* 在使用者或系統管理員同意流程期間進行登入
* 持續性瀏覽器會話控制項已在條件式存取原則中設定

## <a name="next-steps"></a>後續步驟

深入瞭解會影響登入會話超時的其他設定：

* Microsoft 365 –[閒置會話超時](https://docs.microsoft.com/sharepoint/sign-out-inactive-users)
* Azure AD 條件式存取-[使用者登入頻率](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)
* Azure 入口網站–[目錄層級的非啟用時間](https://docs.microsoft.com/azure/azure-portal/admin-timeout)
