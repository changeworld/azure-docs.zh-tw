---
title: 取得我的應用程式入口網站的協助-Azure Active Directory |Microsoft Docs
description: 取得在我的應用程式入口網站中登入及執行一般工作的協助。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253152"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>針對我的應用程式入口網站的問題進行疑難排解

如果您在登入或使用**我的應用程式**入口網站時遇到問題，請先嘗試這些疑難排解秘訣，再洽詢技術服務人員或系統管理員尋求協助。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>我在安裝我的應用程式安全登入延伸模組時遇到問題

如果您在安裝我的應用程式安全登入延伸模組時遇到問題：

- 請確定您使用的是支援的瀏覽器，包括：

    - **Microsoft Edge。** 在 Windows 10 周年版或更新版本上執行。

    - **Google Chrome。** 在 Windows 7 或更新版本上執行，以及在 Mac OS X 或更新版本上執行。

    - **Mozilla Firefox 26.0 或更新版本。** 在 Windows XP SP2 或更新版本上執行，以及在 Mac OS X 10.6 或更新版本上執行。

    - **Internet Explorer 11。** 在 Windows 7 或更新版本上執行（支援有限）。

- 請確定您的瀏覽器延伸模組設定已開啟。

- 請嘗試重新開機瀏覽器，然後再次登入**我的應用程式**入口網站。

- 請嘗試清除瀏覽器的 cookie，然後重新開機，然後再次登入**我的應用程式**入口網站。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>我無法登入**我的應用程式**入口網站

如果您在登入**我的應用程式**入口網站時遇到問題，您可以嘗試下列動作：

- 請確定您使用的是正確的 URL。 它應該是 https://myapps.microsoft.com 或您組織的自訂頁面，例如 https://myapps.microsoft.com/contoso.com。

- 請確定您的密碼正確且未過期。 如需詳細資訊，請參閱[重設您的工作或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 請確定您的驗證資訊是最新且正確的。 如需詳細資訊，請參閱[Azure 多因素驗證對我有何意義？](multi-factor-authentication-end-user.md)或[變更您的安全性資訊方法與資訊](security-info-add-update-methods-overview.md)。

- 將 [**我的應用程式**入口網站 URL] 新增至 [網際網路內容] **> [安全性 > 信任的網站**] 設定。

- 清除瀏覽器的快取，然後再次嘗試登入。

## <a name="my-password-isnt-working"></a>我的密碼無法運作

如果您忘記密碼、您的組織中從未收到過密碼、已鎖定您的帳戶，或想要變更您的密碼，請參閱 [說明] [、[我忘了我的 Azure AD 密碼](active-directory-passwords-update-your-own-password.md)]。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我想要能夠重設自己的密碼

若要能夠重設自己的密碼，您的系統管理員必須先為您的組織開啟此功能，然後您必須更新並驗證您所需的驗證方法。 如需如何更新驗證方法的詳細資訊，請參閱[註冊自助式密碼重設](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>我在啟動應用程式時收到拒絕存取的訊息

如果您從 [**我的應用程式**] 入口網站啟動應用程式後收到 [**拒絕存取**] 訊息，您可以嘗試下列動作：

- 請確定您已安裝[我的應用程式安全登入延伸](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)模組，而且您使用的是[支援的瀏覽器](my-apps-portal-end-user-access.md#supported-browsers)。

- 請確定您使用的是正確的應用程式 URL，且 URL 位於您的網際網路內容 **> 安全性 > 信任的網站** 清單中。

- 請確定您的密碼正確且未過期。 如需詳細資訊，請參閱[重設您的工作或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 請確定您的驗證資訊是最新且正確的。 如需詳細資訊，請參閱[Azure 多因素驗證對我有何意義？](multi-factor-authentication-end-user.md)或[變更您的安全性資訊方法與資訊](security-info-add-update-methods-overview.md)。

- 清除瀏覽器的快取，然後再次嘗試登入。

如果在嘗試這些專案之後仍然無法存取您的應用程式，您必須洽詢貴組織的技術支援人員尋求協助。

## <a name="next-steps"></a>後續步驟

登入**我的應用程式**入口網站後，您也可以更新您的設定檔和帳戶資訊、群組資訊和存取權審查資訊（如果您有許可權的話）。

- [在我的應用程式入口網站上存取和使用應用程式](my-apps-portal-end-user-access.md)。

- [變更您的設定檔資訊](my-apps-portal-end-user-update-profile.md)。

- [查看和更新您的群組相關資訊](my-apps-portal-end-user-groups.md)。

- [執行您自己的存取權審查](my-apps-portal-end-user-access-reviews.md)。
