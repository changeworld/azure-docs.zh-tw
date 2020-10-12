---
title: 新增個人 Microsoft 帳戶至 Microsoft Authenticator 應用程式 - Azure AD
description: 將個人 Microsoft 帳戶 (例如 Outlook.com 或 Xbox LIVE 的帳戶) 新增至 Microsoft Authenticator 應用程式以進行雙因素驗證來驗證身分識別。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83741621"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>將個人 Microsoft 帳戶新增至 Microsoft Authenticator 應用程式

將個人 Microsoft 帳戶 (例如 Outlook.com 和 Xbox LIVE 的帳戶) 新增至 Microsoft Authenticator 應用程式，供標準雙因素驗證程序和無密碼手機登入使用。

- **標準雙因素驗證方法。** 在要登入的裝置上鍵入使用者名稱和密碼，然後選擇 Microsoft Authenticator 應用程式是要傳送通知，或希望從 Microsoft Authenticator 應用程式的 [帳戶] 畫面複製相關聯的驗證碼。

- **無密碼登入方法。** 使用個人 Microsoft 帳戶，在要登入的裝置中鍵入使用者名稱，然後使用行動裝置確認是您本人使用指紋、臉部或 PIN。 使用此方法時不需要輸入密碼。

>[!Important]
>您必須先下載並安裝 Microsoft Authenticator 應用程式，才可新增帳戶。 如果尚未這麼做，請依照[下載並安裝應用程式](user-help-auth-app-download-install.md)文章中的步驟。

您可以藉由先開啟雙因素驗證，然後將帳戶新增至應用程式，以新增個人 Microsoft 帳戶。 您不需要只為了針對帳戶使用無密碼手機登入就開啟雙因素驗證，但我們強烈建議開啟雙因素驗證以獲得額外的帳戶安全性。

## <a name="turn-on-two-factor-verification"></a>開啟雙因素驗證

1. 在您的電腦上移至[安全性基本概念](https://account.microsoft.com/security)頁面，並使用您的個人 Microsoft 帳戶登入。 例如： alain@outlook.com 。

2. 在 [安全性基本概念] 頁面底部，選擇 [更多安全性選項] 連結。

    ![反白顯示 [更多安全性選項] 連結的 [安全性基本概念] 頁面](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. 移至 [雙步驟驗證] 區段，並選擇**開啟**此功能。 如果您個人的帳戶不再需要使用此功能，您也可以將其關閉。

## <a name="add-your-microsoft-account-to-the-app"></a>將您的 Microsoft 帳戶新增至應用程式

1. 在行動裝置上開啟 Microsoft Authenticator 應用程式。

1. 在 Android 上，從右上方的**自訂和控制**圖示選取 [新增帳戶]。

    ![Android 帳戶選取頁面](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    在 iOS 上，選取右上方的加號圖示。

    ![帳戶選取體驗的 iOS 版本](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. 在 [新增帳戶] 頁面中，選擇 [個人帳戶]。

1. 選取 [使用 Microsoft 帳戶登入] 以新增帳戶。 可使用 QR 代碼 (如果有的話)，但也可一律以使用者名稱和密碼來登入，以新增帳戶。

    ![選取 Microsoft 帳戶或掃描 QR 代碼 (如果有的話)](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 使用適當的電子郵件地址 (例如 alain@outlook.com) 登入個人帳戶，然後選取 [下一步]。

    >[!Note]
    >如果您沒有個人 Microsoft 帳戶，[則可建立一個](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)。

1. 輸入密碼，然後選取 [登入]。 您的個人帳戶會新增至 Microsoft Authenticator 應用程式。

## <a name="next-steps"></a>後續步驟

- 將您的帳戶新增至應用程式之後，您即可在裝置上使用 Authenticator 應用程式登入。 如需詳細資訊，請參閱[使用應用程式登入](user-help-auth-app-sign-in.md)。

- 如果在取得個人 Microsoft 帳戶的驗證碼時遇到問題，請參閱 [Microsoft 帳戶安全性資訊及驗證碼](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的＜疑難排解驗證碼問題＞一節。

- 對於執行 iOS 的裝置，您也可以將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 如需更多資訊，請參閱[使用 Microsoft Authenticator 應用程式備份和復原](user-help-auth-app-backup-recovery.md)。
