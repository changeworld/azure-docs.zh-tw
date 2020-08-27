---
title: 設定驗證會話管理-Azure Active Directory
description: 自訂 Azure AD authentication 會話設定，包括使用者登入頻率和瀏覽器會話持續性。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 06/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28516482cf154c81912de0f8e3f35b9e54ef2f69
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948261"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>使用條件式存取來設定驗證工作階段管理

在複雜的部署中，組織可能需要限制驗證會話。 某些案例可能包括：

* 從非受控或共用裝置存取資源
* 從外部網路存取機密資訊
* 高影響力的使用者
* 重要商務應用程式

條件式存取控制可讓您建立以組織內特定使用案例為目標的原則，而不會影響所有使用者。

在深入探討如何設定原則的詳細資訊之前，讓我們先檢查預設設定。

## <a name="user-sign-in-frequency"></a>使用者登入頻率

登入頻率會定義在使用者嘗試存取資源時，要求使用者重新登入之前的時間週期。

Azure Active Directory 的 (Azure AD) 使用者登入頻率的預設設定是90天的滾動時段。 要求使用者提供認證通常是很好的事，但它可以 backfire：已定型來輸入其認證的使用者，而不需要考慮到不慎將他們提供給惡意認證提示。

這可能會讓使用者無法要求使用者重新登入，事實上，任何違反 IT 原則的情況都會撤銷該會話。 部分範例包括 (但不限於) 密碼變更、incompliant 裝置或帳戶停用。 您也可以 [使用 PowerShell 來明確撤銷使用者的會話](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 Azure AD 預設設定會出現「不要求使用者提供其認證（如果其會話的安全性狀態尚未變更」）。

登入頻率設定適用于已根據標準執行 OAUTH2 或 OIDC 通訊協定的應用程式。 大部分適用于 Windows、Mac 和行動裝置的 Microsoft 原生應用程式（包括下列 web 應用程式）都符合該設定。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- O365 系統管理員入口網站
- Exchange Online
- SharePoint 與 OneDrive
- 小組網頁用戶端
- Dynamics CRM Online
- Azure 入口網站

登入頻率設定也適用于 SAML 應用程式，但前提是它們不會卸載自己的 cookie，並會定期重新導向回到 Azure AD 進行驗證。

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>使用者登入頻率和多重要素驗證

登入頻率先前只套用至已加入 Azure AD 的裝置上的第一個要素驗證，已加入混合式 Azure AD，而且 Azure AD 註冊。 我們的客戶無法輕鬆地在這些裝置上 (MFA) 重新強制執行多重要素驗證。 根據客戶的意見反應，登入頻率也將適用于 MFA。

[![登入頻率和 MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>使用者登入頻率和裝置身分識別

如果您已加入 Azure AD 或加入混合式 Azure AD，或 Azure AD 註冊的裝置，當使用者解除鎖定其裝置或以互動方式登入時，此事件也會滿足登入頻率原則。 在下列兩個範例中，使用者登入頻率設定為1小時：

範例 1：

- 在00:00，使用者登入 Windows 10 Azure AD 加入的裝置，並開始處理儲存在 SharePoint Online 上的檔。
- 使用者會在其裝置上繼續使用相同的檔一小時。
- 在01:00，系統會根據系統管理員所設定的條件式存取原則中的登入頻率需求，提示使用者重新登入。

範例 2：

- 在00:00，使用者登入 Windows 10 Azure AD 加入的裝置，並開始處理儲存在 SharePoint Online 上的檔。
- 在00:30，使用者會取得並中斷鎖定其裝置。
- 在00:45 時，使用者會從其中斷返回並解除鎖定裝置。
- 在01:45 時，系統會提示使用者根據其系統管理員設定的條件式存取原則中的登入頻率需求，再次登入，因為上次登入發生于00:45。

## <a name="persistence-of-browsing-sessions"></a>流覽會話的持續性

持續性瀏覽器會話可讓使用者在關閉並重新開啟其瀏覽器視窗之後，保持登入狀態。

瀏覽器會話持續性的 Azure AD 預設值，可讓個人裝置上的使用者選擇是否要顯示「保持登入」來保存會話。 在驗證成功之後提示。 如果使用 [AD FS 單一登入設定一](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)文中的指導方針在 AD FS 中設定瀏覽器持續性，我們將符合該原則，並同時保存 Azure AD 會話。 您也可以設定租使用者中的使用者是否看到「保持登入」。 在 Azure 入口網站中，使用 [ [自訂您的 Azure AD 登入] 頁面](../fundamentals/customize-branding.md)中的指引，變更 [公司商標] 窗格中的適當設定，以提示您。

## <a name="configuring-authentication-session-controls"></a>設定驗證會話控制項

條件式存取是 Azure AD Premium 功能，需要 Premium 授權。 如果您想要深入瞭解條件式存取，請參閱 [什麼是 Azure Active Directory 中的條件式存取？](overview.md#license-requirements)

> [!WARNING]
> 如果您使用目前處於公開預覽狀態的 [可設定權杖存留期](../develop/active-directory-configurable-token-lifetimes.md) 功能，請注意，我們不支援針對相同的使用者或應用程式組合建立兩個不同的原則：一個是此功能，另一個則使用可設定的權杖存留期功能。 Microsoft 計畫在2020年5月1日淘汰可設定的權杖存留期功能，並以條件式存取驗證會話管理功能取代。  

### <a name="policy-1-sign-in-frequency-control"></a>原則1：登入頻率控制

1. 建立新原則
1. 選擇客戶環境的所有必要條件，包括目標雲端應用程式。

   > [!NOTE]
   > 建議您為金鑰 Microsoft Office 應用程式（例如 Exchange Online 和 SharePoint Online）設定等同的驗證提示頻率，以獲得最佳使用者體驗。

1. 移至 [**存取控制**]  >  **會話**，然後按一下 [登**入頻率**]
1. 在第一個文字方塊中輸入所需的天數和時數值
1. 從下拉式清單中選取 [ **小時** ] 或 [ **天** ] 值
1. 儲存您的原則

![針對登入頻率設定的條件式存取原則](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

在 Azure AD 註冊的 Windows 裝置上，登入裝置會被視為提示。 例如，如果您已將 Office 應用程式的登入頻率設定為24小時，則在 Azure AD 註冊的 Windows 裝置上的使用者將會透過登入裝置來滿足登入頻率原則，而在開啟 Office 應用程式時不會再次出現提示。

### <a name="policy-2-persistent-browser-session"></a>原則2：持續性瀏覽器會話

1. 建立新原則
1. 選擇所有必要的條件。

   > [!NOTE]
   > 請注意，此控制項需要選擇 [所有雲端應用程式] 作為條件。 瀏覽器會話持續性由驗證會話權杖控制。 瀏覽器會話中的所有索引標籤都會共用單一會話權杖，因此它們都必須共用持續性狀態。

1. 移至 [**存取控制**]  >  **會話**，然後按一下 [**持續瀏覽器會話**]
1. 從下拉式清單中選取值
1. 儲存您的原則

![針對持續性瀏覽器設定的條件式存取原則](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 條件式存取中的持續性瀏覽器會話設定將會覆寫「保持登入」。 如果您已設定這兩個原則，請在相同使用者的 [公司商標] 窗格中設定 Azure 入口網站。

## <a name="validation"></a>驗證

使用「假設」工具可根據您設定原則的方式，模擬使用者對目標應用程式的登入，以及其他條件。 驗證會話管理控制項會顯示在工具的結果中。

![條件式存取 What If 工具結果](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>原則部署

若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 如需詳細資訊，請參閱 [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。

## <a name="next-steps"></a>接下來的步驟

* 如果您想要知道如何設定條件式存取原則，請參閱本文 [需要具有 Azure Active Directory 條件式存取的特定應用程式的 MFA](../authentication/tutorial-enable-azure-mfa.md)。
* 如果您已準備好設定環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。