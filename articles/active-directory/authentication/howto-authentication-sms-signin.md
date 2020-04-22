---
title: Azure 活動目錄基於 SMS 的使用者登入
description: 瞭解如何設定與使用使用者能夠使用 SMS 登入 Azure 活動目錄(預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770558"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>使用 Azure 的目錄設定與開啟使用者進行基於 SMS 的身份驗證(預覽)

為了降低使用者登錄到應用程式和服務的複雜性和安全風險,Azure 活動目錄 (Azure AD) 提供了多個身份驗證選項。 基於 SMS 的身份驗證當前處於預覽狀態,允許使用者無需提供甚至知道使用者名和密碼即可登錄。 身份管理員創建其帳戶後,他們可以在登錄提示符處輸入其電話號碼,並提供通過簡訊發送給他們的身份驗證代碼。 此身份驗證方法簡化了對應用程式和服務的訪問,尤其是對於前線工作人員。

本文介紹如何為 Azure AD 中選定的使用者或組啟用基於 SMS 的身份驗證。

|     |
| --- |
| 基於 SMS 的使用者身份驗證是 Azure 活動目錄的公共預覽功能。 有關預覽的詳細資訊,請參閱 Microsoft [Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>開始之前

要完成本文,您需要以下資源和特權:

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與訂閱關聯的 Azure 活動目錄租戶。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 需要在 Azure AD 租戶中具有*全域管理員*許可權才能啟用基於 SMS 的身份驗證。
* 在文本消息身份驗證方法策略中啟用的每個用戶都必須獲得許可,即使他們不使用它。 每個啟用的使用者必須具有以下 Azure AD 或 Microsoft 365 許可證之一:
    * [Azure AD Premium P1 或 P2][azuread-licensing]
    * [微軟 365 (M365) F1 或 F3][m365-firstline-workers-licensing]
    * [企業移動性 + 安全 (EMS) E3 或 E5][ems-licensing]或[Microsoft 365 (M365) E3 或 E5][m365-licensing]

## <a name="limitations"></a>限制

在基於 SMS 的身份驗證的公共預覽期間,以下限制適用:

* 基於 SMS 的身份驗證當前與 Azure 多重身份驗證不相容。
* 除 Teams 外,基於 SMS 的身份驗證當前與本機 Office 應用程式不相容。
* 不建議 B2B 帳戶使用基於 SMS 的身份驗證。
* 聯合使用者不會在主租戶中進行身份驗證。 它們僅在雲中進行身份驗證。

## <a name="enable-the-sms-based-authentication-method"></a>開啟基於 SMS 的身分驗證方法

您的組織中啟用與使用基於 SMS 的身分驗證有三個主要步驟:

* 啟用身份驗證方法策略。
* 選擇可以使用基於 SMS 的身份驗證方法的使用者或組。
* 為每個使用者帳戶分配電話號碼。
    * 此電話號碼可以在 Azure 門戶(本文中所示)和 *「我的員工*」或 *「我的個人資料*」中分配。

首先,讓我們為 Azure AD 租戶啟用基於 SMS 的身份驗證。

1. 以*全域管理員*身份登入[Azure 門戶][azure-portal]。
1. 搜尋並選取 [Azure Active Directory]  。
1. 從 Azure 活動目錄視窗左側的導航功能表中,選擇 **「安全>身份驗證方法>身份驗證方法策略(預覽)。**

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 從可用身份驗證方法清單中,選擇 **「文本訊息**」。
1. 將**啟用設定為***"是*"。

    ![在認證方法原則視窗中啟用文字身份驗證](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    您可以選擇為*所有使用者*或*選擇使用者*和組啟用基於 SMS 的身份驗證。 在下一節中,您可以為測試使用者啟用基於 SMS 的身份驗證。

## <a name="assign-the-authentication-method-to-users-and-groups"></a>將認證方法分配給使用者和群組

在 Azure AD 租戶中啟用基於 SMS 的身份驗證後,現在選擇允許使用此身份驗證方法的某些使用者或組。

1. 在簡訊身份驗證策略視窗中,將 **「目標」** 設定為 *「選擇使用者*」 。
1. 選擇新增**使用者或群組**,然後選擇測試使用者或群組,如*Contoso 使用者*或*Contoso SMS 使用者*。

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 選擇使用者或組后,**選擇"選擇**",然後**保存**更新的身份驗證方法策略。

在文本消息身份驗證方法策略中啟用的每個用戶都必須獲得許可,即使他們不使用它。 請確保為身份驗證方法策略中啟用的使用者提供了適當的許可證,尤其是在為大型使用者組啟用該功能時。

## <a name="set-a-phone-number-for-user-accounts"></a>設定使用者帳戶的電話號碼

用戶現在已啟用基於 SMS 的身份驗證,但其電話號碼必須與 Azure AD 中的使用者配置檔關聯,然後才能登錄。 用戶可以在 *「我的設定檔」* 中[自行設定此電話號碼](../user-help/sms-sign-in-explainer.md),也可以使用 Azure 門戶分配電話號碼。 電話號碼可以由*全域管理員*、*身份驗證管理員*或*特權身份驗證管理員*設置。

當為 SMS 簽名設定電話號碼時,它也可以與[Azure 多重身份驗證][tutorial-azure-mfa]和[自助服務密碼重置][tutorial-sspr]一起使用。

1. 搜尋並選取 [Azure Active Directory]  。
1. 從 Azure 活動目錄視窗左側的導航功能表中,選擇 **「使用者**」 。。
1. 在上一個項目(如*Contoso User)* 選擇開啟 SMS 的身分驗證的使用者,然後選擇**認證方法**。
1. 輸入使用者的電話號碼,包括國家/地區代碼,如 *#1 xxxxxxxxx*。 Azure 門戶驗證電話號碼的格式正確。

    ![為 Azure 門戶中的使用者設定電話號碼,以便與基於 SMS 的身份驗證一起使用](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    電話號碼在租戶中必須是唯一的。 如果嘗試對多個使用者使用相同的電話號碼,將顯示一條錯誤消息。

1. 要將電話號碼應用於使用者帳戶,請選擇"**保存**"。

成功預先接後,*將顯示啟用 SMS 登入的*複選標記。

## <a name="test-sms-based-sign-in"></a>測試基於 SMS 的登入

要測試現在啟用的基於 SMS 的登入的使用者帳戶,請完成以下步驟:

1. 打開新的 InPrivate 或隱身 Web 瀏覽器視窗,[https://www.office.com][office]
1. 在右上角,選擇 **「登錄」。**
1. 在登錄提示符中,在上一節中輸入與使用者關聯的電話號碼,然後選擇 **「下一步**」。。

    ![在測試使用者的登入提示處輸入電話號碼](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 文本消息發送到提供的電話號碼。 要完成登錄過程,請在登錄提示符的簡訊中輸入 6 位元碼。

    ![輸入透過簡訊傳送到使用者電話號碼的確認代碼](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 用戶現在登錄而無需提供使用者名或密碼。

## <a name="troubleshoot-sms-based-sign-in"></a>排除基於 SMS 的登入故障

如果在啟用和使用基於 SMS 的登錄時遇到問題,可以使用以下方案和故障排除步驟。

### <a name="phone-number-already-set-for-a-user-account"></a>已為使用者帳戶設定的電話號碼

如果用戶已註冊 Azure 多重身份驗證和/或自助服務密碼重置 (SSPR),則他們已具有與其帳戶關聯的電話號碼。 此電話號碼不能自動用於基於 SMS 的登錄。

已設定電話號碼的使用者會顯示一個按鈕,用於在其 **「我的個人資料」** 頁*中啟用 SMS 登入*。 選擇此按鈕,該帳戶將啟用,以便與基於 SMS 的登錄和以前的 Azure 多重身份驗證或 SSPR 註冊一起使用。

有關最終使用者體驗的詳細資訊,請參閱[SMS 登錄使用者體驗的電話號碼(預覽)。](../user-help/sms-sign-in-explainer.md)

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>嘗試在使用者帳戶上設定電話號碼時出錯

如果在 Azure 門戶中嘗試為使用者帳戶設定電話號碼時收到錯誤,請查看以下故障排除步驟:

1. 確保已啟用基於 SMS 的登錄預覽。
1. 確認在*簡訊*身份驗證方法策略中啟用了使用者帳戶。
1. 請確保使用 Azure 門戶中驗證的正確格式設置電話號碼(如 *+1 4251234567)。*
1. 確保電話號碼在租戶的其他地方未使用。
1. 檢查帳戶上未設置語音號碼。 如果設置了語音號碼,請刪除並再次嘗試電話號碼。

## <a name="next-steps"></a>後續步驟

有關在沒有密碼的情況下登入 Azure AD 的其他方法,例如 Microsoft 身份驗證器應用或 FIDO2 安全金鑰,請參閱[Azure AD 的無密碼身份驗證選項][concepts-passwordless]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
