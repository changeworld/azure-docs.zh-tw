---
title: Azure Multi-Factor Authentication 提示和會話存留期
description: 瞭解 Azure Multi-Factor Authentication 的重新驗證提示，以及如何套用會話存留期的建議設定。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab36eb9d5c66f61fbb320c91087659cc811eca85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91595538"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>優化重新驗證提示，並瞭解 Azure Multi-Factor Authentication 的會話存留期

Azure Active Directory (Azure AD) 有多個設定，可決定使用者需要重新驗證的頻率。 這種重新驗證可能是第一個因素，例如 password、FIDO 或無密碼 Microsoft Authenticator，或 (MFA) 執行多重要素驗證。 您可以視需要為您自己的環境和您想要的使用者體驗，設定這些重新驗證設定。

使用者登入頻率的 Azure AD 預設設定是90天的滾動時段。 要求使用者提供認證通常是很好的事，但它可以 backfire。 如果使用者在不考慮的情況下輸入其認證，他們可能會不慎將他們提供給惡意認證提示。

它可能會讓使用者不會要求使用者重新登入，但任何違反該會話的 IT 原則。 其中一些範例包括密碼變更、incompliant 裝置或帳戶停用作業。 您也可以 [使用 PowerShell 來明確撤銷使用者的會話](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)。

本文詳述建議的設定，以及不同的設定如何運作並彼此互動。

## <a name="recommended-settings"></a>建議的設定

若要讓您的使用者以正確的頻率登入，讓您的使用者獲得最佳的安全性和使用方式，建議您執行下列設定：

* 如果您有 Azure AD Premium：
    * 使用 [受管理的裝置](../devices/overview.md) 或 [無縫 sso](../hybrid/how-to-connect-sso.md)，在應用程式之間啟用單一登入 (SSO) 。
    * 如果需要重新驗證，請使用條件式存取登 [入頻率原則](../conditional-access/howto-conditional-access-session-lifetime.md)。
    * 針對從非受控裝置或行動裝置案例登入的使用者，請使用條件式存取來啟用持續性瀏覽器會話和登入頻率原則。
* 如果您有 Microsoft 365 apps 授權或免費的 Azure AD 層：
    * 使用 [受管理的裝置](../devices/overview.md) 或 [無縫 sso](../hybrid/how-to-connect-sso.md)，在應用程式之間啟用單一登入 (SSO) 。
    * 保持啟用 [ *保持登入* ] 選項，並引導您的使用者接受它。
* 針對行動裝置案例，請確定您的使用者使用 Microsoft Authenticator 應用程式。 此應用程式會用來做為其他 Azure AD 同盟應用程式的訊息代理程式，並減少裝置上的驗證提示。

我們的研究顯示這些設定最適合大部分的租使用者。 這些設定的某些組合（例如， *記住 MFA* 並 *保持簽章*）可能會導致您的使用者經常進行驗證。 一般的重新驗證提示對於使用者生產力而言是不良的，可能會讓他們更容易遭受攻擊。

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD 會話存留期設定

若要為您的使用者優化驗證提示的頻率，您可以設定 Azure AD 會話存留期選項。 瞭解您企業和使用者的需求，並設定可為您的環境提供最佳平衡的設定。

### <a name="evaluate-session-lifetime-policies"></a>評估會話存留期原則

如果沒有任何會話存留期設定，瀏覽器會話中就不會有任何持續性 cookie。 每次使用者關閉並開啟瀏覽器時，就會出現重新驗證的提示。 在 Office 用戶端中，預設的時間週期是90天的滾動視窗。 使用此預設的 Office 設定時，如果使用者已重設其密碼，或已有超過90天的非使用狀態，則使用者必須重新驗證 (第一個和第二個因素) 的所有必要因素。

使用者可能會在 Azure AD 中沒有身分識別的裝置上看到多個 MFA 提示。 當每個應用程式都有自己的 OAuth 重新整理權杖，而該權杖未與其他用戶端應用程式共用時，會產生 在此案例中，因為每個應用程式要求使用 MFA 驗證 OAuth 重新整理權杖，所以 MFA 會提示多次。

在 Azure AD 中，會話存留期限制最嚴格的原則會決定使用者何時需要重新驗證。 請考慮下列案例：

* 您會啟用 *保持登入*狀態，它會使用持續性瀏覽器 cookie，而
* 您也可啟用 *記住 MFA 14 天*

在此範例案例中，使用者必須每隔14天重新驗證一次。 此行為會遵循限制最嚴格的原則，即使 *讓我保持登入* 本身並不需要使用者在瀏覽器上重新驗證也一樣。

### <a name="managed-devices"></a>受管理的裝置

使用 Azure AD Join 或混合式 Azure AD Join 來加入 Azure AD 的裝置會收到主要重新整理 [權杖 (PRT) ](../devices/concept-primary-refresh-token.md) 在應用程式之間使用單一登入 (SSO) 。 此 PRT 可讓使用者在裝置上登入一次，並可讓 IT 人員確認符合安全性和合規性標準。 如果在某些應用程式或案例中，需要要求使用者更頻繁地登入已加入的裝置，您可以使用 [條件式存取登入頻率](../conditional-access/howto-conditional-access-session-lifetime.md)來達成此目的。

### <a name="show-option-to-remain-signed-in"></a>顯示保持登入的選項

當使用者在登入期間于 [*保持登入？* ] 選項選取 **[是]** 時，會在瀏覽器上設定持續性 cookie。 此持續性 cookie 會記住第一個和第二個因素，而且只適用于瀏覽器中的驗證要求。

![保持登入的範例提示螢幕擷取畫面](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

如果您有 Azure AD Premium 1 授權，建議您針對 *持續性瀏覽器會話*使用條件式存取原則。 此原則會覆寫 [ *保持登入* ] 設定，並提供改善的使用者體驗。 如果您沒有 Azure AD Premium 1 授權，建議您為使用者啟用 [保持登入] 設定。

如需設定選項讓使用者保持登入的詳細資訊，請參閱 [自訂您的 Azure AD 登入頁面](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)。

### <a name="remember-multi-factor-authentication"></a>記住 Multi-Factor Authentication  

這項設定可讓您設定1-365 天之間的值，並在使用者選取 [在登入時 **不要再詢問 X 天** ] 選項時，在瀏覽器上設定持續性 cookie。

![核准登入要求的範例提示螢幕擷取畫面](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

雖然這項設定會減少 web 應用程式的驗證次數，但它會增加新式驗證用戶端（例如 Office 用戶端）的驗證次數。 這些用戶端通常只會在密碼重設或閒置90天后才會提示。 但是，將此值設定為小於90天，會縮短 Office 用戶端的預設 MFA 提示，並增加重新驗證頻率。 當與 **保持登入** 或條件式存取原則搭配使用時，可能會增加驗證要求的數目。

如果您使用「 *記住 MFA* 」並擁有 Azure AD Premium 1 授權，請考慮將這些設定遷移至條件式存取登入頻率。 否則，請考慮使用 [ *讓我保持登入嗎？* ]。

如需詳細資訊，請參閱 [記住 Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication)。

### <a name="authentication-session-management-with-conditional-access"></a>使用條件式存取進行驗證會話管理

登**入頻率**可讓系統管理員選擇同時適用于用戶端和瀏覽器中的第一個和第二個因素的登入頻率。 在需要限制驗證會話的情況下（例如針對重要商務應用程式），我們建議使用這些設定，以及使用受管理的裝置。

**持續性瀏覽器會話** 可讓使用者在關閉並重新開啟其瀏覽器視窗之後，保持登入狀態。 類似于 *保持登入* 的設定，它會在瀏覽器上設定持續性 cookie。 不過，由於系統管理員已設定此設定，因此不需要使用者在 [*保持登入嗎？* ] 選項中選取 **[是]** ，以提供更好的使用者體驗。 如果您使用 [ *保持登入？* ] 選項，建議您改為啟用 **持續性瀏覽器會話** 原則。

如需詳細資訊， 請參閱 [使用條件式存取設定驗證會話管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

### <a name="configurable-token-lifetimes"></a>可設定的權杖存留期

這項設定可讓您設定 Azure Active Directory 所簽發的權杖存留期。 這項原則會由 *具有條件式存取的驗證會話管理*取代。 如果您目前使用 *可* 設定的權杖存留期，我們建議您開始遷移至條件式存取原則。

## <a name="review-your-tenant-configuration"></a>檢查您的租使用者設定  

現在您已瞭解不同的設定如何運作以及建議的設定，現在可以檢查您的租使用者設定，並據以進行變更：

若要設定或檢查 *保持登入* 的選項，請完成下列步驟：

1. 在 Azure AD 入口網站中，搜尋並選取 [ *Azure Active Directory*]。
1. 選取 [ **公司商標**]，然後針對每個地區設定選擇 [ **顯示選項] 以保持登入**狀態。
1. 選擇 [ *是]*，然後選取 [ **儲存**]。

若要記住多重要素驗證設定，請完成下列步驟：

1. 在 Azure AD 入口網站中，搜尋並選取 [ *Azure Active Directory*]。
1. 選取 [ **安全性**]，然後選取 [ **MFA**]。
1. 在 [ **設定**] 底下，選取 [ **其他雲端式 MFA 設定**]。
1. 在 [ *多重要素驗證服務設定* ] 頁面中，請滾動以 **記住多重要素驗證設定**。 取消核取此核取方塊以停用設定。

若要設定登入頻率和持續性瀏覽器會話的條件式存取原則，請完成下列步驟：

1. 在 Azure AD 入口網站中，搜尋並選取 [ *Azure Active Directory*]。
1. 選取 [ **安全性**]，然後選取 [ **條件式存取**]。
1. 使用本文詳述的建議會話管理選項來設定原則。

若要檢查權杖存留期，請 [使用 Azure AD PowerShell 來查詢任何 Azure AD 原則](../develop/configure-token-lifetimes.md#prerequisites)。 停用任何您已備妥的原則。

如果您的租使用者中已啟用一個以上的設定，建議您根據可用的授權更新您的設定。 例如，如果您有 Azure AD premium 授權，您應該只使用登 *入頻率* 和 *持續性瀏覽器會話*的條件式存取原則。 如果您有 Microsoft 365 apps 或 Azure AD 免費授權，您應該使用 [ *保持登入？* ] 設定。

如果您已啟用可設定的權杖存留期，很快就會移除這項功能。 規劃遷移至條件式存取原則。

下表摘要說明以授權為基礎的建議：

|              | Azure AD Free 和 Microsoft 365 應用程式 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | 針對未受管理的裝置[Azure AD 聯結](../devices/concept-azure-ad-join.md)或[混合式 Azure AD 聯結](../devices/concept-azure-ad-join-hybrid.md)或[無縫 SSO](../hybrid/how-to-connect-sso.md) 。 | Azure AD Join<br />混合式 Azure AD Join |
| **重新驗證設定** | 仍已登入                  | 使用條件式存取原則進行登入頻率和持續性瀏覽器會話 |

## <a name="next-steps"></a>後續步驟

若要開始使用，請完成教學課程以 [使用 Azure Multi-Factor Authentication 保護使用者登入事件](tutorial-enable-azure-mfa.md) ，或 [使用風險偵測讓使用者登入觸發 Azure Multi-Factor Authentication](tutorial-risk-based-sspr-mfa.md)。
