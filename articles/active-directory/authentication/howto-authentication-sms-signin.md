---
title: Azure Active Directory 的 SMS 型使用者登入
description: 了解如何使用 SMS，設定及啟用 Azure Active Directory 的使用者登入 (預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 849057ab1ccde2e0771f0c1cd52ea399ffe4cea7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964684"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>使用 Azure Active Directory 設定及啟用使用者的 SMS 型驗證 (預覽)

為了降低使用者登入應用程式和服務的複雜性和安全性風險，Azure Active Directory (Azure AD) 提供多個驗證選項。 SMS 型驗證 (目前處於預覽狀態) 可讓使用者登入，而不需要提供 (或甚至知道) 他們的使用者名稱和密碼。 在身分識別系統管理員建立他們的帳戶之後，他們可以在登入提示中輸入其電話號碼，並提供透過文字簡訊傳送給他們的驗證碼。 此驗證方法會簡化對應用程式和服務的存取，特別是針對前線的員工。

本文說明如何針對 Azure AD 中選取的使用者或群組啟用 SMS 型驗證。

> [!NOTE]
> 適用於使用者的 SMS 型驗證是 Azure Active Directory 的一項公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 您必須擁有 Azure AD 租用戶的「全域管理員」權限，才能啟用 SMS 型驗證。
* 在文字簡訊驗證方法原則中啟用的每個使用者都必須獲得授權，即使使用者未使用該驗證。 每個啟用的使用者都必須具有下列其中一個 Azure AD、EMS、Microsoft 365 授權：
    * [Azure AD Premium P1 或 P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 或 F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 或 E5][ems-licensing] 或 [Microsoft 365 (M365) E3 或 E5][m365-licensing]

## <a name="limitations"></a>限制

在 SMS 型驗證的公開預覽期間，適用下列限制：

* SMS 型驗證目前與 Azure Multi-Factor Authentication 不相容。
* 除了 Teams 以外，SMS 型驗證目前與原生 Office 應用程式不相容。
* 不建議針對 B2B 帳戶使用 SMS 型驗證。
* 同盟使用者不會在主租用戶中進行驗證。 他們只會在雲端中進行驗證。

## <a name="enable-the-sms-based-authentication-method"></a>啟用 SMS 型驗證方法

在貴組織中啟用和使用 SMS 型驗證有三個主要步驟：

* 啟用驗證方法原則。
* 選取可以使用 SMS 型驗證方法的使用者或群組。
* 為每個使用者帳戶指派電話號碼。
    * 此電話號碼可以在 Azure 入口網站中指派 (如本文所示)，以及在 [我的員工] 或 [我的設定檔] 中指派。

首先，讓我們為您的 Azure AD 租用戶啟用 SMS 型驗證。

1. 以「全域管理員」身分登入 [Azure 入口網站][azure-portal]。
1. 搜尋並選取 [Azure Active Directory]。
1. 從 Azure Active Directory 視窗左側的導覽功能表中，選取 [安全性 > 驗證方法 > 驗證方法原則 (預覽)]。

    [![在 Azure 入口網站中流覽並選取 [驗證方法] 原則 (預覽]) 視窗。](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 從可用驗證方法的清單中，選取 [文字簡訊]。
1. 將 [啟用] 設定為 [是]。

    ![在驗證方法原則視窗中啟用文字驗證](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    您可以選擇針對「所有使用者」或「選取使用者」和群組，啟用 SMS 型驗證。 在下一節中，您會為測試使用者啟用 SMS 型驗證。

## <a name="assign-the-authentication-method-to-users-and-groups"></a>將驗證方法指派給使用者和群組

在您的 Azure AD 租用戶中啟用 SMS 型驗證之後，現在請選取要允許使用此驗證方法的某些使用者或群組。

1. 在文字簡訊驗證原則視窗中，將 [目標] 設定為 [選取使用者]。
1. 選擇 [新增使用者或群組]，然後選取測試使用者或群組，例如「Contoso 使用者」或「Contoso SMS 使用者」。

    [![選擇要在 Azure 入口網站中啟用 SMS 驗證的使用者或群組。](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 當您選取使用者或群組之後，請選擇 [選取]，然後選擇 [儲存] 已更新的驗證方法原則。

在文字簡訊驗證方法原則中啟用的每個使用者都必須獲得授權，即使使用者未使用該驗證。 請確定您對於在驗證方法原則中啟用的使用者擁有適當授權，特別是當您為大型使用者群組啟用此功能時。

## <a name="set-a-phone-number-for-user-accounts"></a>為使用者帳戶設定電話號碼

使用者現在已啟用 SMS 型驗證，但是其電話號碼必須與 Azure AD 中的使用者設定檔建立關聯，才能登入。 使用者可以在 [我的設定檔] 中[自行設定此電話號碼](../user-help/sms-sign-in-explainer.md)，或者您可以使用 Azure 入口網站指派電話號碼。 電話號碼可以由「全域管理員」、「驗證管理員」，或「特殊權限驗證管理員」來設定。

設定 SMS 登入的電話號碼時，也可以與 [Azure Multi-Factor Authentication][tutorial-azure-mfa] 和[自助式密碼重設][tutorial-sspr]搭配使用。

1. 搜尋並選取 [Azure Active Directory]。
1. 從 Azure Active Directory 視窗左側的導覽功能表中，選取 [使用者]。
1. 選取您在上一節中啟用 SMS 型驗證的使用者，例如「Contoso 使用者」，然後選取 [驗證方法]。
1. 輸入使用者的電話號碼，包括國家/地區代碼，例如「+1 xxxxxxxxx」。 Azure 入口網站會驗證電話號碼的格式是否正確。

    ![在 Azure 入口網站中設定使用者的電話號碼，與 SMS 型驗證搭配使用](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    電話號碼在您的租用戶中必須是唯一的。 如果您嘗試為多個使用者使用相同的電話號碼，則會顯示錯誤訊息。

1. 若要將電話號碼套用至使用者的帳戶，請選取 [儲存]。

成功佈建時，會顯示「SMS 登入已啟用」的核取記號。

## <a name="test-sms-based-sign-in"></a>測試 SMS 型登入

若要測試現在已啟用 SMS 型登入的使用者帳戶，請完成下列步驟：

1. 開啟新的 InPrivate 或 Incognito 網頁瀏覽器視窗至 [https://www.office.com][office]
1. 選取右上角的 [登入]。
1. 在登入提示中，輸入與上一節中使用者相關聯的電話號碼，然後選取 [下一步]。

    ![在登入提示中為測試使用者輸入電話號碼](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 文字簡訊會傳送到提供的電話號碼。 若要完成登入程序，請在登入提示中輸入於文字簡訊中提供的 6 位數代碼。

    ![輸入透過文字簡訊傳送至使用者電話號碼的確認碼](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 使用者現在已登入，不需要提供使用者名稱或密碼。

## <a name="troubleshoot-sms-based-sign-in"></a>針對 SMS 型登入進行疑難排解

如果您有啟用和使用 SMS 型登入的問題，可以使用下列案例和疑難排解步驟。

### <a name="phone-number-already-set-for-a-user-account"></a>已經為使用者帳戶設定電話號碼

如果使用者已註冊 Azure Multi-Factor Authentication 和/或自助式密碼重設 (SSPR)，他們的帳戶就已經有相關聯的電話號碼。 此電話號碼不會自動與 SMS 型登入搭配使用。

系統會針對其帳戶已設定電話號碼的使用者，在其 [我的設定檔] 頁面中顯示 [針對 SMS 登入啟用] 按鈕。 選取此按鈕，就會啟用帳戶與 SMS 型登入和先前的 Azure Multi-Factor Authentication 或 SSPR 註冊搭配使用。

如需使用者體驗的詳細資訊，請參閱[電話號碼的 SMS 登入使用者體驗 (預覽)](../user-help/sms-sign-in-explainer.md)。

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>嘗試在使用者帳戶上設定電話號碼時發生錯誤

當您嘗試在 Azure 入口網站中為使用者帳戶設定電話號碼時，如果收到錯誤，請參閱下列疑難排解步驟：

1. 請確定您已啟用 SMS 型登入預覽。
1. 確認使用者帳戶已在「文字簡訊」驗證方法原則中啟用。
1. 請確定您設定的電話號碼具有適當格式，如 Azure 入口網站中所驗證 (例如「+1 4251234567」)。
1. 請確定您的租用戶中其他地方未使用此電話號碼。
1. 請檢查帳戶上未設定任何語音號碼。 若已設定語音號碼，請刪除並再次嘗試電話號碼。

## <a name="next-steps"></a>後續步驟

如需在沒有密碼的情況下登入 Azure AD 的其他方式 (例如 Microsoft Authenticator 應用程式或 FIDO2 安全性金鑰)，請參閱 [Azure AD 的無密碼驗證選項][concepts-passwordless]。

您也可以使用 Microsoft Graph REST API Beta 來 [啟用][rest-enable] 或 [停][rest-disable] 用 SMS 登入。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
