---
title: 針對 SAML 型單一登入進行偵錯 - Azure Active Directory | Microsoft Docs
description: 針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: bbd622ccb7aeecfff4d3dc744623105a593e6a67
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85383575"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

了解如何在 Azure Active Directory (Azure AD) 中支援[安全性聲明標記語言 (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 的應用程式內尋找[單一登入](../manage-apps/what-is-single-sign-on.md)問題，並加以修正。 

## <a name="before-you-begin"></a>開始之前

建議您安裝 [My Apps 安全登入擴充功能](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)。 此瀏覽器擴充功能可讓您輕鬆地收集需要用來解決單一登入問題的 SAML 要求和 SAML 回應資訊。 如果您無法安裝此擴充功能，本文會說明如何在已安裝/未安裝此擴充功能的情況下解決問題。

若要下載並安裝 My Apps 安全登入擴充功能，請使用下列其中一個連結。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>測試 SAML 型單一登入

若要測試 Azure AD 與目標應用程式之間的 SAML 型單一登入：

1. 以全域系統管理員或其他已獲得授權可管理應用程式的系統管理員身分，登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左側的分頁視窗中，選取 [ **Azure Active Directory**]，然後選取 [**企業應用程式**]。 
1. 從企業應用程式清單中，選取您要測試單一登入的應用程式，然後從左側的選項中選取 [**單一登入**]。
1. 若要開啟以 SAML 為基礎的單一登入測試體驗，請移至**測試單一登入**（步驟5）。 如果 [**測試**] 按鈕呈現灰色，您必須在 [**基本 SAML**設定] 區段中，先填寫並儲存必要的屬性。
1. 在 [測試單一登入]**** 刀鋒視窗中，使用公司的認證來登入目標應用程式。 您可以使用目前使用者或不同使用者的身分來登入。 如果您使用不同使用者的身分登入，會有提示要求您進行驗證。

    ![顯示 [測試 SAML SSO] 頁面的螢幕擷取畫面](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

如果您成功登入，就表示您已通過測試。 在此情況下，Azure AD 會向應用程式發出 SAML 回應權杖。 應用程式使用 SAML 權杖成功地將您登入。

如果您在公司的登入頁面或應用程式的頁面上遇到錯誤，請使用下列其中一節來解決錯誤。

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>解決公司登入頁面上的登入錯誤

當您嘗試登入時，您可能會在公司的登入頁面上看到類似下列範例的錯誤。

![在公司登入頁面中顯示錯誤的範例](./media/howto-v1-debug-saml-sso-issues/error.png)

若要對這個錯誤進行偵錯，您需要錯誤訊息和 SAML 要求。 My Apps 安全登入擴充功能會自動收集此資訊，並在 Azure AD 上顯示解決指導方針。

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>若要解決已安裝我的應用程式安全登入延伸模組的登入錯誤

1. 發生錯誤時，延伸模組會將您重新導向回到 Azure AD**測試單一登入**] 分頁。
1. 在 [**測試單一登入**] 分頁上，選取 [**下載 SAML 要求**]。
1. 根據錯誤和 SAML 要求中的值，您應該會看到特定的解決指導方針。
1. 您會看到 [**修正**] 按鈕，以自動更新 Azure AD 中的設定來解決問題。 如果您看不到此按鈕，表示登入問題不是因為 Azure AD 上設定錯誤。

如果沒有針對登入錯誤提供解決方案，建議您使用 [意見反應] 文字方塊來通知我們。

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>解決錯誤而不安裝我的應用程式安全登入延伸模組

1. 複製頁面右下角的錯誤訊息。 錯誤訊息包括：
    - 相互關聯識別碼和時間戳記。 在向 Microsoft 建立支援案例時務必要有這些值，原因是這些值可協助工程師識別問題並提供正確的問題解決方式。
    - 可識別問題根本原因的陳述。
1. 返回 Azure AD，並尋找 [測試單一登入]**** 刀鋒視窗。
1. 在 [取得解決指導方針]**** 上方的文字方塊中，貼上錯誤訊息。
1. 按一下 [取得解決指導方針]**** 以顯示問題的解決步驟。 此指導方針可能需要來自 SAML 要求或 SAML 回應的資訊。 如果您不是使用我的應用程式安全登入延伸模組，您可能需要[Fiddler](https://www.telerik.com/fiddler)之類的工具來取得 SAML 要求和回應。
1. 確認 SAML 要求中的目的地對應到從 Azure AD 取得的 SAML 單一登入服務 URL。
1. 確認 SAML 要求中的簽發者與您在 Azure AD 中為應用程式設定的識別碼相同。 Azure AD 會使用簽發者尋找您目錄中的應用程式。
1. 確認 AssertionConsumerServiceURL 是應用程式預期會從 Azure AD 接收 SAML 權杖的位置。 您可以在 Azure AD 中設定此值，但如果它是 SAML 要求的一部分，則不是必要的。


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>解決應用程式頁面上的登入錯誤

您可能會先成功登入，之後才在應用程式的頁面上看到錯誤。 當 Azure AD 核發權杖給應用程式，但應用程式不接受該回應時，就會發生這種情況。

若要解決此錯誤，請依照下列步驟執行︰

1. 如果應用程式位於 Azure AD 資源庫中，請確認您已遵循整合應用程式與 Azure AD 的所有步驟。 若要尋找應用程式的整合指示，請參閱 [SaaS 應用程式整合教學課程清單](../saas-apps/tutorial-list.md)。
1. 擷取 SAML 回應。
    - 如果您已安裝 My Apps 安全登入擴充功能，請從 [測試單一登入]**** 刀鋒視窗中，按一下 [下載 SAML 回應]****。
    - 如果未安裝此擴充功能，則請使用 [Fiddler](https://www.telerik.com/fiddler) 之類的工具來擷取 SAML 回應。
1. 請注意 SAML 回應權杖中的這些元素：
   - NameID 值和格式的使用者唯一識別碼
   - 在權杖中所發出的宣告
   - 用來簽署權杖的憑證。

     如需 SAML 回應的詳細資訊，請參閱[單一登入 SAML 通訊協定](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

1. 現在您已審核 SAML 回應，請參閱登[入後應用程式頁面上的錯誤](../manage-apps/application-sign-in-problem-application-error.md)，以取得如何解決問題的指引。 
1. 如果您仍然無法成功登入，您可以向應用程式廠商詢問 SAML 回應中缺少的內容。

## <a name="next-steps"></a>後續步驟

現在，單一登入正在使用您的應用程式，您可以將[使用者布建和解除布建到 SaaS 應用程式](../manage-apps/user-provisioning.md)，或[開始使用條件式存取](../conditional-access/app-based-conditional-access.md)。
