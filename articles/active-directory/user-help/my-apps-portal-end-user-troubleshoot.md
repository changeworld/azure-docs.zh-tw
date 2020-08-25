---
title: 取得我的應用程式入口網站的協助 - Azure Active Directory | Microsoft Docs
description: 取得在我的應用程式入口網站中登入及執行常見工作的協助。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c5d1b08944417493d175de23c7738db58f1c7bd3
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798001"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>針對我的應用程式入口網站的問題進行疑難排解

如果您在登入或使用 [我的應用程式] 入口網站時遇到問題，請在連絡技術服務人員或系統管理員以取得協助之前，先嘗試下列疑難排解提示。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>我無法順利安裝我的應用程式安全登入擴充功能

如果您在安裝「我的應用程式安全登入擴充功能」時遇到問題：

- 請確定您使用的是支援的瀏覽器，其中包括：

    - **Microsoft Edge。** 在 Windows 10 Anniversary Edition 或更新版本上執行。

    - **Google Chrome。** 在 Windows 7 或更新版本，和在 MacOS X 或更新版本上執行。

    - **Mozilla Firefox 26.0 或更新版本。** 在 Windows XP SP2 或更新版本，和在 Mac OS X 10.6 或更新版本上執行。

    - **Internet Explorer 11。** 在 Windows 7 或更新版本上 (部分支援) 執行。

- 請確定您的瀏覽器擴充功能設定已開啟。

- 嘗試重新啟動瀏覽器，然後再次登入 [我的應用程式] 入口網站。

- 嘗試清除瀏覽器的 Cookie，然後重新啟動並再次登入 [我的應用程式] 入口網站。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>我無法登入 [我的應用程式] 入口網站

如果您在登入 [我的應用程式] 入口網站時遇到問題，您可嘗試下列動作：

- 確定您使用的是正確的 URL。 其應該是 https://myapps.microsoft.com 或貴組織的自訂頁面，例如 https://myapps.microsoft.com/contoso.com 。

- 確定密碼正確無誤且尚未過期。 如需詳細資訊，請參閱[重設您的工作密碼或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 確定您的驗證資訊是最新的且正確無誤。 如需詳細資訊，請參閱 [Azure Multi-Factor Authentication 對我有何意義？](./multi-factor-authentication-end-user-first-time.md)或[變更您的安全性資訊方法和資訊](./security-info-setup-auth-app.md)。

- 將 [我的應用程式] 入口網站 URL 新增至 [網際網路屬性] > [安全性] > [信任的網站] 設定。

- 清除瀏覽器的快取，然後嘗試再次登入。

## <a name="my-password-isnt-working"></a>我的密碼無法運作

如果您忘記密碼、未曾收到貴組織給予的密碼、系統鎖定了您的帳戶或您想要變更密碼，請參閱[忘記 Azure AD 密碼的說明](active-directory-passwords-update-your-own-password.md)。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我希望能夠重設自己的密碼

若要能夠重設自己的密碼，您的系統管理員必須先為您的組織開啟此功能，然後您必須更新並確認您所需的驗證方法。 如需如何更新驗證方法的詳細資訊，請參閱[註冊自助式密碼重設](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>我在啟動應用程式時收到拒絕存取訊息

如果您在從 [我的應用程式] 入口網站啟動應用程式後收到 [拒絕存取] 訊息，可以嘗試下列動作：

- 確定您已安裝[我的應用程式安全登入擴充功能](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)，而且您使用的是[支援的瀏覽器](my-apps-portal-end-user-access.md#supported-browsers)。

- 確定您使用的是正確的應用程式 URL，且該 URL 位於 [網際網路屬性] > [安全性] > [信任的網站] 清單中。

- 確定密碼正確無誤且尚未過期。 如需詳細資訊，請參閱[重設您的工作密碼或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 確定您的驗證資訊是最新的且正確無誤。 如需詳細資訊，請參閱 [Azure Multi-Factor Authentication 對我有何意義？](./multi-factor-authentication-end-user-first-time.md)或[變更您的安全性資訊方法和資訊](./security-info-setup-auth-app.md)。

- 清除瀏覽器的快取，然後嘗試再次登入。

如果在嘗試這些動作之後，仍然無法存取您的應用程式，您必須連絡貴組織的技術支援中心以取得協助。

## <a name="next-steps"></a>後續步驟

登入 [我的應用程式] 入口網站後，您也可以更新您的設定檔和帳戶資訊、您的群組資訊及存取權檢閱資訊 (如果您有權限)。

- [在我的應用程式入口網站上存取和使用應用程式](my-apps-portal-end-user-access.md)。

- [變更設定檔資訊](my-apps-portal-end-user-update-profile.md)。

- [檢視和更新您的群組相關資訊](my-apps-portal-end-user-groups.md)。

- [執行您自己的存取權檢閱](my-apps-portal-end-user-access-reviews.md)。