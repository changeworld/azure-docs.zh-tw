---
title: Azure 多因素驗證提示和會話存留期
description: 瞭解使用 Azure 多重要素驗證的重新驗證提示的建議設定，以及會話存留期的套用方式。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77f2e78fa3df6c7ac6a422a03335f93d2a19d99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255744"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>優化重新驗證提示，並瞭解 Azure 多重要素驗證的會話存留期

Azure Active Directory （Azure AD）有多個設定，可決定使用者需要重新驗證的頻率。 這種重新驗證可能是第一個因素，例如 password、FIDO 或無密碼 Microsoft Authenticator，或是執行多重要素驗證（MFA）。 您可以視需要針對自己的環境和您想要的使用者體驗，設定這些重新驗證設定。

本文詳細說明建議的設定，以及不同設定的工作和互動方式。

## <a name="recommended-settings"></a>建議的設定

為了讓您的使用者能夠以正確的頻率登入，以取得正確的安全性和易用性，我們建議您進行下列設定：

* 如果您有 Azure AD Premium：
    * 使用[受管理的裝置](../devices/overview.md)或[無縫 SSO](../hybrid/how-to-connect-sso.md)，跨應用程式啟用單一登入（SSO）。
    * 如果需要重新驗證，請使用條件式存取登[入頻率原則](../conditional-access/howto-conditional-access-session-lifetime.md)。
    * 針對從非受控裝置或行動裝置案例登入的使用者，請使用條件式存取來啟用持續性瀏覽器會話和登入頻率原則。
* 如果您有 Office 365 應用程式授權或免費的 Azure AD 層：
    * 使用[受管理的裝置](../devices/overview.md)或[無縫 SSO](../hybrid/how-to-connect-sso.md)，跨應用程式啟用單一登入（SSO）。
    * 保持已啟用 [*保持登入*] 選項，並引導使用者接受它。

我們的研究顯示這些設定對大部分的租使用者而言都是合適的。 這些設定的某些組合（例如，*記住 MFA*並*保持簽章*）可能會導致使用者出現太常驗證的提示。 定期重新驗證提示對使用者生產力而言是不好的，而且可能會使它們更容易遭受攻擊。

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD 會話存留期設定

若要將使用者的驗證提示頻率優化，您可以設定 Azure AD 會話存留期選項。 瞭解您的商務和使用者需求，並進行設定，為您的環境提供最佳平衡。

### <a name="evaluate-session-lifetime-policies"></a>評估會話存留期原則

如果沒有任何會話存留期設定，瀏覽器會話中就不會有持續的 cookie。 每次使用者關閉並開啟瀏覽器時，都會收到重新驗證的提示。 在 Office 用戶端中，預設時間週期是90天的滾動視窗。 使用此預設的 Office 設定時，如果使用者已重設其密碼，或超過90天的非使用狀態，則使用者必須以所有必要的因素（第一個和第二個因素）重新驗證。

在 Azure AD 中，會話存留期最嚴格的原則會決定使用者何時需要重新驗證。 請考慮下列案例：

* 您啟用的*仍會保持登入*狀態，這會使用持續的瀏覽器 cookie，以及
* 您也啟用*了14天的「記住 MFA* 」

在此範例案例中，使用者必須每14天重新驗證一次。 這種行為會遵循限制最嚴格的原則，即使 [*讓我保持登入*] 也不會要求使用者在瀏覽器上進行重新驗證。

### <a name="managed-devices"></a>受管理的裝置

使用 Azure AD Join 或混合式 Azure AD Join 加入 Azure AD 的裝置會收到主要的重新整理[權杖（PRT）](../devices/concept-primary-refresh-token.md) ，以在應用程式間使用單一登入（SSO）。 此 PRT 可讓使用者在裝置上登入一次，並讓 IT 人員確保符合安全性和合規性的標準。 如果需要在某些應用程式或案例的已聯結裝置上要求使用者更頻繁地登入，則可以使用[條件式存取登入頻率](../conditional-access/howto-conditional-access-session-lifetime.md)來達成此目的。

### <a name="show-option-to-remain-signed-in"></a>顯示保持登入的選項

當使用者在登入期間于 [*保持登入？* ] 選項中選取 **[是]** 時，會在瀏覽器上設定持續性 cookie。 這個持續性的 cookie 會記住第一個和第二個因素，而且只適用于瀏覽器中的驗證要求。

![保留登入之範例提示的螢幕擷取畫面](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

如果您有 Azure AD Premium 1 授權，建議您針對*持續性瀏覽器會話*使用條件式存取原則。 此原則會覆寫 [*保持登入？* ] 設定，並提供改良的使用者體驗。 如果您沒有 Azure AD Premium 1 授權，建議您為使用者啟用 [保持登入] 設定。

如需設定選項以讓使用者保持登入的詳細資訊，請參閱[自訂您的 Azure AD 登入頁面](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)。

### <a name="remember-multi-factor-authentication"></a>記住 Multi-Factor Authentication  

此設定可讓您設定1-60 天之間的值，並在使用者選取 [在登入時**不要再詢問 X 天**] 選項時，在瀏覽器上設定持續性 cookie。

![核准登入要求的範例提示螢幕擷取畫面](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

雖然這項設定會減少 web 應用程式的驗證次數，但會增加新式驗證用戶端的驗證次數，例如 Office 用戶端。 這些用戶端通常只會在密碼重設或90天閒置後才提示。 不過，[*記住 MFA* ] 的最大值是60天。 當與**保持登入**或條件式存取原則搭配使用時，可能會增加驗證要求的數目。

如果您使用 [*記住 MFA* ]，並有 Azure AD Premium 1 個授權，請考慮將這些設定遷移至條件式存取登入頻率。 否則，請考慮使用 [*讓我保持登入*]。

如需詳細資訊，請參閱[記住多重要素驗證](howto-mfa-mfasettings.md#remember-multi-factor-authentication)。

### <a name="authentication-session-management-with-conditional-access"></a>使用條件式存取的驗證會話管理

登**入頻率**可讓系統管理員選擇同時適用于用戶端和瀏覽器中的第一個和第二個因素的登入頻率。 我們建議您在需要限制驗證會話（例如重要商務應用程式）的情況下，使用這些設定，以及使用受管理的裝置。

**持續性瀏覽器會話**可讓使用者在關閉並重新開啟其瀏覽器視窗之後，保持登入狀態。 類似于 [*保持登入*] 設定，它會在瀏覽器上設定持續性 cookie。 不過，因為它是由系統管理員所設定，所以不需要使用者在 [保持登*入？* ] 選項中選取 **[是]** ，因此可提供更好的使用者體驗。 如果您使用 [*仍然登入*] 選項，建議您改為啟用**持續性瀏覽器會話**原則。

如需詳細資訊， 請參閱[使用條件式存取來設定驗證會話管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

### <a name="configurable-token-lifetimes"></a>可設定的權杖存留期

此設定允許 Azure Active Directory 所發行之權杖的存留期設定。 此原則會由*具有條件式存取的驗證會話管理*所取代。 如果您目前使用*可*設定的權杖存留期，建議您開始進行條件式存取原則的遷移。

## <a name="review-your-tenant-configuration"></a>檢查您的租使用者設定  

既然您已瞭解不同設定的運作方式和建議的設定，現在可以檢查您的租使用者設定，並據以進行變更：

若要設定或檢查 [*保留登入*] 選項，請完成下列步驟：

1. 在 Azure AD 入口網站中，搜尋並選取 [ *Azure Active Directory*]。
1. 選取 [**公司商標**]，然後針對每個地區設定，選擇 [**顯示選項] 以保持登入**狀態。
1. 選擇 *[是]*，然後選取 [**儲存**]。

若要記住多重要素驗證設定，請完成下列步驟：

1. 在 Azure AD 入口網站中，搜尋並選取 [ *Azure Active Directory*]。
1. 依序選取 [**安全性**] 和 [ **MFA**]。
1. 在 [**設定**] 下，選取 [**其他雲端式 MFA 設定**]。
1. 在 [*多重要素驗證服務設定*] 頁面中，請**務必記住多重要素驗證設定**。 取消核取此核取方塊以停用設定。

若要設定登入頻率和持續性瀏覽器會話的條件式存取原則，請完成下列步驟：

1. 在 Azure AD 入口網站中，搜尋並選取 [ *Azure Active Directory*]。
1. 依序選取 [**安全性**] 和 [**條件式存取**]。
1. 使用本文詳述的建議會話管理選項來設定原則。

若要查看權杖存留期，請[使用 Azure AD PowerShell 來查詢任何 Azure AD 的原則](../develop/active-directory-configurable-token-lifetimes.md#prerequisites)。 停用任何已備妥的原則。

如果您的租使用者中啟用了多個設定，建議您根據您所提供的授權來更新您的設定。 例如，如果您有 Azure AD premium 授權，您應該只使用登*入頻率*和*持續性瀏覽器會話*的條件式存取原則。 如果您有 Office 365 應用程式或 Azure AD 免費的授權，您應該使用 [*仍在登入嗎？* ] 設定。

如果您已啟用可設定的權杖存留期，這項功能很快就會移除。 規劃條件式存取原則的遷移。

下表摘要說明以授權為基礎的建議：

|                              | Azure AD Free 和 Office 365 應用程式 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| SSO                          | 針對未受管理的裝置， [Azure AD 聯結](../devices/concept-azure-ad-join.md)或[混合式 Azure AD 聯結](../devices/concept-azure-ad-join-hybrid.md)，或[無縫 SSO](../hybrid/how-to-connect-sso.md) 。 | Azure AD Join<br />混合式 Azure AD Join |
| 重新驗證設定    | 仍已登入                  | 針對登入頻率和持續性瀏覽器會話使用條件式存取原則 |

## <a name="next-steps"></a>後續步驟

若要開始使用，請完成教學課程以透過[Azure 多重要素驗證來保護使用者登入事件](tutorial-enable-azure-mfa.md)，或[使用使用者登入的風險偵測來觸發 Azure 多重要素驗證](tutorial-risk-based-sspr-mfa.md)。
