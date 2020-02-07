---
title: 將個人 Microsoft 帳戶新增至 Microsoft Authenticator 應用程式 Azure AD
description: 將個人 Microsoft 帳戶（例如適用于 Outlook.com 或 Xbox LIVE）新增至 Microsoft Authenticator 應用程式，以在使用雙因素驗證時驗證您的身分識別。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 289c00e28fd82b78b321732628ff85d22c64cabf
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062297"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>將個人 Microsoft 帳戶新增至 Microsoft Authenticator 應用程式

將個人 Microsoft 帳戶 (例如 Outlook.com 和 Xbox LIVE 的帳戶) 新增至 Microsoft Authenticator 應用程式，供標準雙因素驗證程序和無密碼手機登入方法使用。

- **標準雙因素驗證方法。** 在您要登入的裝置上輸入您的使用者名稱和密碼，然後選擇 Microsoft Authenticator 應用程式是要傳送通知，還是您希望從 Microsoft Authenticator 應用程式的 [帳戶] 畫面複製相關聯的驗證碼。

- **無密碼登入方法。** 在要登入個人 Microsoft 帳戶的裝置中輸入您的使用者名稱，然後經由行動裝置使用指紋、臉部或 PIN 確認您的身分。 使用此方法時不需要輸入密碼。

>[!Important]
>您必須先下載並安裝 Microsoft Authenticator 應用程式，才可新增帳戶。 如果尚未這麼做，請依照[下載並安裝應用程式](user-help-auth-app-download-install.md)文章中的步驟。

## <a name="add-your-personal-microsoft-account"></a>新增您的個人 Microsoft 帳戶

您可以藉由先開啟雙因素驗證，然後將帳戶新增至應用程式，以新增個人 Microsoft 帳戶。

>[!Note]
>如果您的個人 Microsoft 帳戶只要使用無密碼手機登入，則不需要開啟雙因素驗證。 不過，為了提高帳戶的安全性，建議您開啟雙因素驗證。

### <a name="turn-on-two-factor-verification"></a>開啟雙因素驗證

1. 在您的電腦上移至[安全性基本概念](https://account.microsoft.com/security)頁面，並使用您的個人 Microsoft 帳戶登入。 例如： alain@outlook.com 。

2. 在 [安全性基本概念] 頁面底部，選擇 [更多安全性選項] 連結。

    ![反白顯示 [更多安全性選項] 連結的 [安全性基本概念] 頁面](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. 移至 [雙步驟驗證] 區段，並選擇**開啟**此功能。 如果您個人的帳戶不再需要使用此功能，您也可以將其關閉。

### <a name="add-your-microsoft-account-to-the-app"></a>將您的 Microsoft 帳戶新增至應用程式

1. 在行動裝置上開啟 Microsoft Authenticator 應用程式。

2. 從右上方的 [自訂和控制] 圖示選取 [新增帳戶]。

    ![反白顯示 [自訂和控制] 圖示的 [帳戶] 頁面](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. 在 [新增帳戶] 頁面中，選擇 [個人帳戶]。

4. 使用適當的電子郵件地址 (例如 alain@outlook.com) 登入您的個人帳戶，然後選擇 [下一步]。

    >[!Note]
    >如果您沒有個人 Microsoft 帳戶，您可以在此建立一個。

5. 輸入您的密碼，然後選擇 [登入]。

    您的個人帳戶會新增至 Microsoft Authenticator 應用程式。

## <a name="next-steps"></a>後續步驟

- 將您的帳戶新增至應用程式之後，您即可在裝置上使用 Authenticator 應用程式登入。 如需詳細資訊，請參閱[使用應用程式登入](user-help-auth-app-sign-in.md)。

- 如果您在取得個人 Microsoft 帳戶的驗證碼時遇到問題，請參閱[Microsoft 帳戶安全性資訊 & 驗證碼](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的**疑難排解驗證程式代碼問題**一節。

- 對於執行 iOS 的裝置，您也可以將您的帳戶認證和相關應用程式設定 (例如您的帳戶順序) 備份到雲端。 如需更多資訊，請參閱[使用 Microsoft Authenticator 應用程式備份和復原](user-help-auth-app-backup-recovery.md)。
