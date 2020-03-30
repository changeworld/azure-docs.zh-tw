---
title: 為 Azure AD 庫應用配置密碼 SSO 時出現問題
description: 了解使用者在為應用程式設定密碼單一登入 (這類應用程式已經列於 Azure AD 應用程式庫中) 時所面臨的常見問題
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159041"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>為 Azure AD 資源庫應用程式設定密碼單一登入時遇到的問題

本文可協助您了解使用者在搭配 Azure AD 資源庫應用程式設定**密碼單一登入**時所面臨的常見問題。

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>認證會填入，但延伸模組不會提交它們

如果應用程式廠商最近有變更登入頁面，以新增欄位、變更用來偵測使用者名稱和密碼欄位的識別碼，或修改其應用程式的登入體驗運作方式，通常就會發生此問題。 幸運的是，在許多情況下，Microsoft 可以與應用程式廠商合作，快速解決這些問題。

雖然 Microsoft 有技術可在整合中斷時進行自動偵測，但無法立即找出這些問題，或者問題需要一些時間來修正。 萬一其中一項整合無法正常運作，請開啟支援案例，如此便能儘速修正問題。

**如果您與此應用程式的廠商有所聯繫，** 請將我們的連絡方式傳送給他們，讓 Microsoft 能夠與他們合作，將其應用程式與 Azure Active Directory 進行原生整合。 您可以將廠商引導到[在 Azure Active Directory 應用程式庫中列出您的應用程式](../azuread-dev/howto-app-gallery-listing.md)，讓他們可以立即開始。

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>認證會填入並提交，但頁面指出認證不正確

若要解決此問題，請嘗試下列方式：

- 讓使用者先嘗試使用為他們儲存的認證，**直接登入應用程式網站**。

  * 如果可登入，接著讓使用者在[應用程式存取面板](https://myapps.microsoft.com/)的 [應用程式]**** 區段中，按一下 [應用程式磚]**** 上的 [更新認證]**** 按鈕，將他們更新至最新已知的有效使用者名稱和密碼。

  * 如果您或其他系統管理員已為此使用者指派認證，請尋找使用者或群組的應用程式指派，方法是瀏覽至應用程式的 [使用者和群組]**** 索引標籤、選取指派，然後按一下 [更新認證]**** 按鈕。

- 如果使用者已指派他們自己的認證，讓使用者**檢查以確定他們的密碼在應用程式中尚未過期**，如果過期，請直接登入應用程式，以**更新他們過期的密碼**。

  * 在應用程式中更新密碼之後，要求使用者在[應用程式存取面板](https://myapps.microsoft.com/)的 [應用程式]**** 區段中，按一下 [應用程式磚]**** 上的 [更新認證]**** 按鈕，以將他們更新至最新已知的有效使用者名稱和密碼。

  * 如果您或其他系統管理員已為此使用者指派認證，請尋找使用者或群組的應用程式指派，方法是瀏覽至應用程式的 [使用者和群組]**** 索引標籤、選取指派，然後按一下 [更新認證]**** 按鈕。

- 讓使用者依照[如何安裝存取面板的瀏覽器延伸模組](#how-to-install-the-access-panel-browser-extension)一節中的下列步驟，來更新存取面板的瀏覽器延伸模組。

- 確定存取面板的瀏覽器延伸模組正在使用者的瀏覽器中執行且已啟用。

- 確定您的使用者未在 **incognito、InPrivate 或私人模式**中，嘗試從存取面板登入應用程式。 這些模式不支援存取面板延伸模組。

萬一先前的建議不可行，可能是因為應用程式端發生了變更，暫時中斷了應用程式與 Azure AD 的整合。 例如，這可能發生在下列情況中：當應用程式廠商在其頁面上引進了一個指令碼，而其行為在手動與自動輸入時極為不同，這可能導致自動整合 (就像我們自己的) 中斷。 幸運的是，在許多情況下，Microsoft 可以與應用程式廠商合作，快速解決這些問題。

雖然 Microsoft 有技術可在應用程式整合中斷時進行自動偵測，但無法立即找出這些問題，或者問題可能需要一些時間來修正。 若整合未正常運作，您可以開啟支援案例，以便儘快修正。 

此外，**如果您與此應用程式的廠商聯繫，** **請將我們的連絡方式傳送給他們**，讓我們能夠與他們合作，將其應用程式與 Azure Active Directory 進行原生整合。 您可以將廠商引導到[在 Azure Active Directory 應用程式庫中列出您的應用程式](../azuread-dev/howto-app-gallery-listing.md)，讓他們可以立即開始。

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>延伸模組可在 Chrome 和 Firefox 中運作，但無法在 Internet Explorer 中運作

這個問題有兩個主要原因：

- 根據已在 Internet Explorer 中啟用的安全性設定而定，如果網站不屬於**信任的區域**，有時我們的指令碼會被中斷而無法針對應用程式執行。

  *  若要解決此問題，指示使用者**新增應用程式的網站**至其 **Internet Explorer 安全性設定**內的**信任的網站**清單。 您可以將使用者引導至[如何將網站新增到信任的網站清單](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5)一文，以取得詳細指示。

- 在很罕見的情況下，Internet Explorer 的安全性驗證有時會造成頁面載入速度比我們指令碼執行的速度還慢。

  * 不幸的是，這種情況會根據瀏覽器版本、電腦速度或瀏覽過的網站而有所不同。 在此情況下，我們建議您連絡支援人員，讓我們能夠修正這個特定應用程式的整合。

此外，**如果您與此應用程式的廠商聯繫，** **請將我們的連絡方式傳送給他們**，讓我們能夠與他們合作，將其應用程式與 Azure Active Directory 進行原生整合。 您可以將廠商引導到[在 Azure Active Directory 應用程式庫中列出您的應用程式](../azuread-dev/howto-app-gallery-listing.md)，讓他們可以立即開始。

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>檢查應用程式的登入頁面最近是否已變更或需要額外的欄位

如果應用程式的登入頁面已大幅變更，有時這會導致我們的整合中斷。 當應用程式廠商將登入欄位、Captcha 或多重要素驗證新增至他們的體驗時，即為一例。 幸運的是，在許多情況下，Microsoft 可以與應用程式廠商合作，快速解決這些問題。

雖然 Microsoft 有技術可在應用程式整合中斷時進行自動偵測，但無法立即找出這些問題，或者問題可能需要一些時間來修正。 若整合未正常運作，您可以開啟支援案例，以便儘快修正。 

此外，**如果您與此應用程式的廠商聯繫，** **請將我們的連絡方式傳送給他們**，讓我們能夠與他們合作，將其應用程式與 Azure Active Directory 進行原生整合。 您可以將廠商引導到[在 Azure Active Directory 應用程式庫中列出您的應用程式](../azuread-dev/howto-app-gallery-listing.md)，讓他們可以立即開始。

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安裝存取面板的瀏覽器延伸模組

若要安裝存取面板的瀏覽器延伸模組，請遵循下列步驟：

1.  在其中一種支援的瀏覽器中開啟[存取面板](https://myapps.microsoft.com)，然後在您的 Azure AD 中以**使用者**身分登入。

2.  按一下存取面板中的 [密碼-SSO 應用程式]****。

3.  在要求安裝軟體的提示中，選取 [立即安裝]****。

4.  系統會根據您的瀏覽器將您導向下載連結。 將延伸模組**新增**到瀏覽器中。

5.  如果您的瀏覽器要求，請選取 [啟用]**** 或 [允許]**** 該延伸模組。

6.  安裝之後，**重新啟動**瀏覽器工作階段。

7.  登錄到訪問面板，看看是否可以**啟動**密碼 SSO 應用程式

您可能也會從下列直接連結中下載適用於 Chrome 和 Firefox 的延伸模組：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 存取面板延伸模組](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)

