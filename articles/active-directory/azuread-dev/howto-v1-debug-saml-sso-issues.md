---
title: 針對 SAML 型單一登入進行偵錯 - Azure Active Directory | Microsoft Docs
description: 針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154843"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>針對 Azure Active Directory 中應用程式的 SAML 型單一登入進行偵錯

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

了解如何在 Azure Active Directory (Azure AD) 中支援[安全性聲明標記語言 (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 的應用程式內尋找[單一登入](../manage-apps/what-is-single-sign-on.md)問題，並加以修正。 

## <a name="before-you-begin"></a>開始之前

建議您安裝 [My Apps 安全登入擴充功能](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)。 通過此瀏覽器延伸，可以輕鬆收集需要單一登入解決問題的 SAML 請求和 SAML 回應資訊。 如果您無法安裝此擴充功能，本文會說明如何在已安裝/未安裝此擴充功能的情況下解決問題。

若要下載並安裝 My Apps 安全登入擴充功能，請使用下列其中一個連結。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>測試 SAML 型單一登入

要在 Azure AD 和目標應用程式之間測試基於 SAML 的單一登入：

1. 以全域系統管理員或其他已獲得授權可管理應用程式的系統管理員身分，登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左側邊欄選項卡中，選擇**Azure 活動目錄**，然後選擇**企業應用程式**。 
1. 從企業應用程式清單中，選擇要為其測試單一登入的應用程式，然後從左側的選項中選擇 **"單一登入**"。
1. 要打開基於 SAML 的單登錄測試體驗，請轉到**測試單一登入**（步驟 5）。 如果 **"測試"** 按鈕已變灰，則需要首先在 **"基本 SAML 配置**"部分中填寫並保存所需的屬性。
1. 在 [測試單一登入]**** 刀鋒視窗中，使用公司的認證來登入目標應用程式。 您可以使用目前使用者或不同使用者的身分來登入。 如果您使用不同使用者的身分登入，會有提示要求您進行驗證。

    ![顯示測試 SAML SSO 頁面的螢幕截圖](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

如果您成功登入，就表示您已通過測試。 在此情況下，Azure AD 會向應用程式發出 SAML 回應權杖。 應用程式使用 SAML 權杖成功地將您登入。

如果您在公司的登入頁面或應用程式的頁面上遇到錯誤，請使用下列其中一節來解決錯誤。

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>解決公司登入頁面上的登入錯誤

當您嘗試登錄時，您可能會在公司登錄頁上看到類似于以下示例的錯誤。

![在公司登錄頁中顯示錯誤的示例](./media/howto-v1-debug-saml-sso-issues/error.png)

若要對這個錯誤進行偵錯，您需要錯誤訊息和 SAML 要求。 My Apps 安全登入擴充功能會自動收集此資訊，並在 Azure AD 上顯示解決指導方針。

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>通過安裝"我的應用安全登錄擴展"解決登錄錯誤

1. 發生錯誤時，擴展將重定向回 Azure AD**測試單一登入**邊欄選項卡。
1. 在 **"測試單登錄"** 邊欄選項卡上，選擇 **"下載 SAML 請求**"。
1. 根據錯誤和 SAML 要求中的值，您應該會看到特定的解決指導方針。
1. 您將看到 **"修復它"** 按鈕，用於自動更新 Azure AD 中的配置以解決此問題。 如果看不到此按鈕，則登錄問題不是由於 Azure AD 上的配置錯誤。

如果沒有為登錄錯誤提供解決方案，我們建議您使用回饋文字方塊通知我們。

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>在不安裝"我的應用安全登錄擴展"的情況下解決錯誤

1. 複製頁面右下角的錯誤訊息。 錯誤訊息包括：
    - 相互關聯識別碼和時間戳記。 在向 Microsoft 建立支援案例時務必要有這些值，原因是這些值可協助工程師識別問題並提供正確的問題解決方式。
    - 可識別問題根本原因的陳述。
1. 返回 Azure AD，並尋找 [測試單一登入]**** 刀鋒視窗。
1. 在 [取得解決指導方針]**** 上方的文字方塊中，貼上錯誤訊息。
1. 按一下 [取得解決指導方針]**** 以顯示問題的解決步驟。 此指導方針可能需要來自 SAML 要求或 SAML 回應的資訊。 如果您沒有使用"我的應用安全登錄擴展"，則可能需要[Fiddler](https://www.telerik.com/fiddler)等工具來檢索 SAML 請求和回應。
1. 驗證 SAML 請求中的目標是否與從 Azure AD 獲得的 SAML 單點登錄服務 URL 相對應。
1. 驗證 SAML 請求中的頒發者與您在 Azure AD 中為應用程式佈建的應用程式佈建的識別碼相同。 Azure AD 會使用簽發者尋找您目錄中的應用程式。
1. 驗證斷言消費者服務URL 是應用程式期望從 Azure AD 接收 SAML 權杖的位置。 您可以在 Azure AD 中配置此值，但如果它是 SAML 請求的一部分，則不是強制性的。


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>解決應用程式頁面上的登入錯誤

您可能會先成功登入，之後才在應用程式的頁面上看到錯誤。 當 Azure AD 核發權杖給應用程式，但應用程式不接受該回應時，就會發生這種情況。

若要解決此錯誤，請依照下列步驟執行︰

1. 如果應用程式位於 Azure AD 庫中，請驗證您是否遵循了將應用程式與 Azure AD 集成的所有步驟。 若要尋找應用程式的整合指示，請參閱 [SaaS 應用程式整合教學課程清單](../saas-apps/tutorial-list.md)。
1. 擷取 SAML 回應。
    - 如果您已安裝 My Apps 安全登入擴充功能，請從 [測試單一登入]**** 刀鋒視窗中，按一下 [下載 SAML 回應]****。
    - 如果未安裝此擴充功能，則請使用 [Fiddler](https://www.telerik.com/fiddler) 之類的工具來擷取 SAML 回應。
1. 請注意 SAML 回應權杖中的這些元素：
   - NameID 值和格式的使用者唯一識別碼
   - 在權杖中所發出的宣告
   - 用來簽署權杖的憑證。

     如需 SAML 回應的詳細資訊，請參閱[單一登入 SAML 通訊協定](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

1. 現在，您已經查看了 SAML 回應，[請參閱登錄後應用程式頁面上的錯誤](../manage-apps/application-sign-in-problem-application-error.md)，以瞭解如何解決問題。 
1. 如果您仍然無法成功登錄，可以詢問應用程式供應商 SAML 回應中缺少什麼。

## <a name="next-steps"></a>後續步驟

現在，單一登入正在對應用程式工作，您可以[自動向 SaaS 應用程式預配和取消預配](../manage-apps/user-provisioning.md)，或者[開始使用條件訪問](../conditional-access/app-based-conditional-access.md)。
