---
title: 傳取微軟身分的認證器套用到 Microsoft 帳號 - Azure AD
description: 將個人 Microsoft 帳戶(如用於 Outlook.com 或 Xbox LIVE)添加到 Microsoft 身份驗證器應用,以在使用雙重驗證時驗證您的身份。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984729"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>將個人 Microsoft 帳戶新增到 Microsoft 身份驗證器應用

將您的個人 Microsoft 帳戶(如Outlook.com帳戶和 Xbox LIVE 帳戶)添加到 Microsoft 身份驗證器應用,以便進行標準的雙因素驗證過程和無密碼電話登錄。

- **標準雙因素驗證方法。** 在要登入的裝置中鍵入使用者名稱和密碼,然後選擇 Microsoft 身份驗證器應用是否發送通知,或者您是否希望從 Microsoft 身份驗證器應用的 **「帳戶」** 螢幕複製關聯的驗證碼。

- **無密碼登入方法。** 將使用者名鍵入您登錄到使用個人 Microsoft 帳戶的設備中,然後使用行動裝置驗證您使用的是指紋、面部或 PIN。 使用此方法時不需要輸入密碼。

>[!Important]
>您必須先下載並安裝 Microsoft Authenticator 應用程式，才可新增帳戶。 如果尚未這麼做，請依照[下載並安裝應用程式](user-help-auth-app-download-install.md)文章中的步驟。

您可以藉由先開啟雙因素驗證，然後將帳戶新增至應用程式，以新增個人 Microsoft 帳戶。 您不必打開雙因素驗證即可僅對您的帳戶使用無密碼電話登錄,但強烈建議您打開雙因素驗證以進行其他帳戶安全性。

## <a name="turn-on-two-factor-verification"></a>開啟雙因素驗證

1. 在您的電腦上移至[安全性基本概念](https://account.microsoft.com/security)頁面，並使用您的個人 Microsoft 帳戶登入。 例如： alain@outlook.com 。

2. 在 [安全性基本概念]**** 頁面底部，選擇 [更多安全性選項]**** 連結。

    ![反白顯示 [更多安全性選項] 連結的 [安全性基本概念] 頁面](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. 移至 [雙步驟驗證]**** 區段，並選擇**開啟**此功能。 如果您個人的帳戶不再需要使用此功能，您也可以將其關閉。

## <a name="add-your-microsoft-account-to-the-app"></a>將您的 Microsoft 帳戶新增至應用程式

1. 在行動裝置上開啟 Microsoft Authenticator 應用程式。

1. 在 Android 上,從右上角的 **「自定義和控制」** 圖示中選擇 **「添加帳戶**」。。

    ![Android 帳號選擇頁面](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    在 iOS 上,選擇右上角的加號圖示。

    ![iOS 版本的帳戶選擇體驗](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. 在 [新增帳戶]**** 頁面中，選擇 [個人帳戶]****。

1. 選擇 **「使用 Microsoft 登入**」 以新增您的帳戶。 QR 碼可用時可以使用,但您始終可以通過使用使用者名和密碼登錄來添加您的帳戶。

    ![選擇 Microsoft 帳號或可用時掃描 QR 碼](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 使用相應的電子郵件位址(如alain@outlook.com)登錄到您的個人帳戶,然後選擇 **「下一步**」。

    >[!Note]
    >如果您沒有個人 Microsoft 帳號,[則可以建立一個](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)。

1. 輸入密碼,然後選擇 **「登錄」。** 您的個人帳戶會新增至 Microsoft Authenticator 應用程式。

## <a name="next-steps"></a>後續步驟

- 將您的帳戶新增至應用程式之後，您即可在裝置上使用 Authenticator 應用程式登入。 如需詳細資訊，請參閱[使用應用程式登入](user-help-auth-app-sign-in.md)。

- 如果您在獲取個人 Microsoft 帳戶的驗證碼時遇到問題,請參閱[Microsoft 帳戶安全資訊&驗證碼](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文的**故障排除驗證碼問題**部分。

- 對於執行 iOS 的裝置，您也可以將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 如需更多資訊，請參閱[使用 Microsoft Authenticator 應用程式備份和復原](user-help-auth-app-backup-recovery.md)。
