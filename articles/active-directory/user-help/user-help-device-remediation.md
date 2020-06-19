---
title: 修正「您無法從這裡完成」錯誤 - Azure AD
description: 尋找為何會收到「您無法從這裡完成」錯誤訊息的可能修正。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: b76e84f6ae2449347604ab823fde5b74d5c68f07
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741003"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>「您無法從這裡完成」錯誤訊息的可能修正

存取貴組織的內部 Web 應用程式或服務時收到錯誤訊息，指出：**您無法從這裡完成**。 此訊息表示貴組織已讓原則就位，防止您的裝置存取貴組織的資源。 雖然最終您可能還是需要連絡技術服務人員來修正此問題，但是以下是您可以先嘗試的一些動作。

## <a name="make-sure-youre-using-a-supported-browser"></a>請確定您使用的是受支援的瀏覽器
如果您收到**您無法從這裡完成**訊息，指出您嘗試從不受支援的瀏覽器存取貴組織的網站，請檢查您執行的是哪一個瀏覽器。

![與瀏覽器支援相關的錯誤訊息](media/user-help-device-remediation/browser-version.png)

若要修正此問題，您必須根據您的作業系統，安裝並執行支援的瀏覽器。 如果您使用 Windows 10，支援的瀏覽器包括 Microsoft Edge、Internet Explorer 和 Google Chrome。 如果您使用不同的作業系統，可以檢查[支援的瀏覽器](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)的完整清單。

## <a name="make-sure-youre-using-a-supported-operating-system"></a>請確定您使用支援的作業系統
請確定您執行支援版本的作業系統，包括：

- **Windows 用戶端。** Windows 7 或更新版本。

- **Windows Server。** Windows Server 2008 R2 或更新版本。

- **macOS。** macOS X 或更新版本

- **Android 和 iOS。** 最新版本的 Android 和 iOS 行動裝置作業系統

若要修正此問題，您必須安裝並執行支援的作業系統。

## <a name="make-sure-your-device-is-joined-to-your-network"></a>請確定您的裝置已加入您的網路
如果您收到**您無法從這裡完成**訊息，指出您的裝置不符合貴組織的存取原則，請確定您已將裝置加入貴組織的網路。

![與您是否在網路上相關的錯誤訊息](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>若要檢查您的裝置是否已加入您的網路
1. 使用您的公司或學校帳戶，登入 Windows。 例如： alain@contoso.com 。

2. 透過虛擬私人網路 (VPN) 或 DirectAccess 連線到貴組織的網路。

3. 連線之後，按 **Windows 標誌鍵 + L 鍵**來鎖定您的裝置。

4. 使用您的公司或學校帳戶來解除鎖定裝置，然後再次嘗試存取有問題的應用程式或服務。

    如果您還是看到**您無法從這裡完成**錯誤訊息，請選取 [更多詳細資料] 連結，然後連絡技術服務人員以取得詳細資料。

### <a name="to-join-your-device-to-your-network"></a>若要將裝置加入您的網路
如果您的裝置未加入貴組織的網路，可以執行下列其中一項操作：

- **加入工作裝置。** 將您工作用的 Windows 10 裝置加入組織的網路，如此就能存取可能受限的資源。 如需詳細資訊和逐步指示，請參閱[將工作裝置加入貴組織的網路](user-help-join-device-on-network.md)。

- **註冊用於工作的個人裝置。** 在組織的網路上註冊您的個人裝置，通常是手機或平板電腦。 註冊您的裝置之後，該裝置就可以存取組織的受限資源。 如需詳細資訊和逐步指示，請參閱[在貴組織的網路上註冊您的個人裝置](user-help-register-device-on-network.md)。

## <a name="next-steps"></a>後續步驟
- [什麼是 MyApps 入口網站？](active-directory-saas-access-panel-introduction.md)

- [使用您的電話而不是您的密碼登入](user-help-auth-app-sign-in.md)
