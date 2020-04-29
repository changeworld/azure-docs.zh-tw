---
title: 設定驗證會話管理-Azure Active Directory
description: 自訂 Azure AD authentication 會話設定，包括使用者登入頻率和瀏覽器會話持續性。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263279"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>使用條件式存取來設定驗證會話管理

在複雜的部署中，組織可能需要限制驗證會話。 某些案例可能包括：

* 從非受控或共用的裝置存取資源
* 從外部網路存取機密資訊
* 影響度高的使用者
* 重要的商務應用程式

條件式存取控制可讓您建立以組織內特定使用案例為目標的原則，而不會影響所有使用者。

在深入探討如何設定原則的詳細資訊之前，讓我們先檢查預設設定。

## <a name="user-sign-in-frequency"></a>使用者登入頻率

登入頻率會定義在嘗試存取資源時，要求使用者重新登入的時間週期。

使用者登入頻率的 Azure Active Directory （Azure AD）預設設定是90天的滾動視窗。 要求使用者提供認證通常似乎是很好的作法，但它可以 backfire：已定型來輸入認證的使用者，而不想要在無意中將其提供給惡意的認證提示。

這聽起來可能會令使用者不會要求重新登入，事實上，違反 IT 原則會撤銷會話。 某些範例包括（但不限於）密碼變更、incompliant 裝置或帳戶停用。 您也可以[使用 PowerShell 來明確撤銷使用者的會話](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 Azure AD 預設設定會變成「不詢問使用者在其會話的安全性狀態未變更時提供認證」。

登入頻率設定適用于已根據標準來執行 OAUTH2 或 OIDC 通訊協定的應用程式。 大部分適用于 Windows、Mac 和行動裝置的 Microsoft 原生應用程式，包括下列 web 應用程式都符合設定。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- O365 系統管理員入口網站
- Exchange Online
- SharePoint 與 OneDrive
- 小組 web 用戶端
- Dynamics CRM Online
- Azure 入口網站

### <a name="user-sign-in-frequency-and-device-identities"></a>使用者登入頻率和裝置身分識別

如果您已加入 Azure AD、混合式 Azure AD 加入，或 Azure AD 註冊的裝置，當使用者解除鎖定其裝置或以互動方式登入時，此事件也會符合「登入頻率」原則。 在下列2個範例中，使用者登入頻率設定為1小時：

範例 1：

- 在00:00，使用者登入其 Windows 10 Azure AD 加入的裝置，並開始處理儲存在 SharePoint Online 上的檔。
- 使用者會在其裝置上繼續使用相同的檔一小時。
- 在01:00，系統會根據系統管理員所設定的條件式存取原則中的登入頻率需求，提示使用者重新登入。

範例 2：

- 在00:00，使用者登入其 Windows 10 Azure AD 加入的裝置，並開始處理儲存在 SharePoint Online 上的檔。
- 在00:30，使用者會啟動，並中斷鎖定其裝置。
- 在00:45，使用者會從其中斷傳回並解除鎖定裝置。
- 在01:45 時，系統會根據系統管理員所設定的條件式存取原則中的登入頻率需求，提示使用者重新登入，因為上次登入于00:45 為止。

## <a name="persistence-of-browsing-sessions"></a>流覽會話的持續性

持續性瀏覽器會話可讓使用者在關閉並重新開啟其瀏覽器視窗之後，保持登入狀態。

瀏覽器會話持續性的 Azure AD 預設值，可讓個人裝置上的使用者選擇是否要藉由顯示「保持登入」來保存會話。 驗證成功後出現提示。 如果在 AD FS 中使用[AD FS 單一登入設定](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)文章中的指引來設定瀏覽器持續性，我們將會遵循該原則並保存 Azure AD 會話。 您也可以設定您的租使用者中的使用者是否會看到「保持登入？」 使用[自訂您的 Azure AD 登入頁面](../fundamentals/customize-branding.md)一文中的指導方針，在 Azure 入口網站的 [公司商標] 窗格中變更適當的設定來提示。

## <a name="configuring-authentication-session-controls"></a>設定驗證會話控制項

條件式存取是 Azure AD Premium 的功能，而且需要 Premium 授權。 如果您想要深入瞭解條件式存取，請參閱[什麼是 Azure Active Directory 中的條件式存取？](overview.md#license-requirements)

> [!WARNING]
> 如果您使用目前處於公開預覽狀態的[可設定權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)功能，請注意，我們不支援針對相同的使用者或應用程式組合建立兩個不同的原則：一個是使用這項功能，另一個則具有可設定的權杖存留期功能。 Microsoft 計畫在 2020 5 月1日淘汰可設定的權杖存留期功能，並將其取代為條件式存取驗證會話管理功能。  

### <a name="policy-1-sign-in-frequency-control"></a>原則1：登入頻率控制

1. 建立新原則
1. 選擇客戶環境的所有必要條件，包括目標雲端應用程式。

   > [!NOTE]
   > 建議您為金鑰 Microsoft Office 應用程式（例如 Exchange Online 和 SharePoint Online）設定相同的驗證提示頻率，以獲得最佳的使用者體驗。

1. 移至 [**存取控制** > ]**會話**，然後按一下 [登**入頻率**]
1. 在第一個文字方塊中輸入必要的 [日] 和 [小時] 值
1. 從下拉式清單中選取 [**小時**] 或 [**天**] 的值
1. 儲存原則

![設定登入頻率的條件式存取原則](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

在 Azure AD 已註冊的 Windows 裝置上，會將登入裝置視為提示字元。 例如，如果您已將 Office 應用程式的登入頻率設定為24小時，Azure AD 已註冊的 Windows 裝置上的使用者將會藉由登入裝置而滿足登入頻率原則，而在開啟 Office 應用程式時將不會再次提示。

如果您已針對在相同瀏覽器會話中執行的不同 web 應用程式設定不同的登入頻率，最嚴格的原則將會套用至這兩個應用程式，因為在相同的瀏覽器會話中執行的所有應用程式都會共用單一會話權杖。

### <a name="policy-2-persistent-browser-session"></a>原則2：持續性瀏覽器會話

1. 建立新原則
1. 選擇 [所有必要條件]。

   > [!NOTE]
   > 請注意，此控制項必須選擇 [所有雲端應用程式] 做為條件。 瀏覽器會話持續性是由驗證會話權杖所控制。 瀏覽器會話中的所有索引標籤都會共用單一會話權杖，因此它們全都必須共用持續性狀態。

1. 移至 [**存取控制** > ]**會話**，然後按一下 [持續性**瀏覽器會話**]
1. 從下拉式清單中選取值
1. 儲存原則

![針對持續性瀏覽器設定的條件式存取原則](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 條件式存取中的持續性瀏覽器會話設定，將會覆寫「保持登入」。 如果您已設定這兩個原則，請在相同使用者的 Azure 入口網站的 [公司商標] 窗格中設定。

## <a name="validation"></a>驗證

使用 [假設] 工具，根據您設定原則的方式，模擬使用者對目標應用程式和其他條件的登入。 驗證會話管理控制項會顯示在工具的結果中。

![條件式存取 What If 工具結果](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>原則部署

若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 如需詳細資訊，請參閱[Azure Active Directory 中條件式存取的最佳做法](best-practices.md)一文。

## <a name="next-steps"></a>後續步驟

* 如果您想要知道如何設定條件式存取原則，請參閱[使用 Azure Active Directory 條件式存取來要求特定應用程式的 MFA](app-based-mfa.md)一文。
* 如果您已準備好設定環境的條件式存取原則，請參閱[Azure Active Directory 中條件式存取的最佳做法](best-practices.md)一文。
