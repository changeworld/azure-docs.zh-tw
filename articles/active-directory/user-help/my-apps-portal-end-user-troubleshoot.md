---
title: 獲取有關"我的應用"門戶的説明 - Azure 活動目錄*微軟文檔
description: 獲取有關在"我的應用"門戶中登錄和執行常見任務的説明。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253152"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>解決"我的應用"門戶的問題

如果您在登錄或使用 **"我的應用"** 門戶時遇到問題，請嘗試這些疑難排解提示，然後再聯繫説明台或管理員尋求説明。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>安裝"我的應用安全登錄擴展"時遇到問題

如果您在安裝"我的應用安全登錄擴展"時遇到問題：

- 確保您使用的是受支援的瀏覽器，包括：

    - **微軟邊緣。** 在 Windows 10 周年版或更高版本上運行。

    - **谷歌瀏覽器。** 在 Windows 7 或更高版本上運行，在 Mac OS X 或更高版本中運行。

    - **莫齊拉火狐26.0或更高版本。** 在 Windows XP SP2 或更高版本上運行，在 Mac OS X 10.6 或更高版本中運行。

    - **互聯網瀏覽器11。** 在 Windows 7 或更高版本上運行（有限支援）。

- 確保瀏覽器延伸設置已打開。

- 請嘗試重新開機瀏覽器，然後再次登錄到 **"我的應用"** 門戶。

- 請嘗試清除瀏覽器的 Cookie，然後重新開機並再次登錄 **"我的應用"** 門戶。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>無法登錄到 **"我的應用"** 門戶

如果您在登錄 **"我的應用"** 門戶時遇到問題，可以嘗試以下操作：

- 確保您使用的是正確的 URL。 它應該是https://myapps.microsoft.com或組織的自訂頁面，例如https://myapps.microsoft.com/contoso.com。

- 請確保您的密碼正確且未過期。 有關詳細資訊，請參閱[重置工作或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 確保您的驗證資訊是最新且準確的。 有關詳細資訊，請參閱[Azure 多重要素驗證對我意味著什麼？](multi-factor-authentication-end-user.md)或[更改安全資訊方法和資訊](security-info-add-update-methods-overview.md)。

- 將 **"我的應用門戶**URL"添加到**Internet 屬性>安全>受信任的網站**設置。

- 清除瀏覽器的緩存並嘗試重新登錄。

## <a name="my-password-isnt-working"></a>我的密碼不起作用

如果您忘記了密碼、從未從組織收到密碼、帳戶被鎖定或想要更改密碼，請參閱["説明"，我忘記了 Azure AD 密碼](active-directory-passwords-update-your-own-password.md)。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我希望能夠重置自己的密碼

為了能夠重置自己的密碼，管理員必須首先打開組織的功能，然後必須更新和驗證所需的驗證方法。 有關如何更新驗證方法的詳細資訊，請參閱[註冊自助服務密碼重設](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>啟動應用時，我收到"訪問被拒絕"消息

如果您在從 **"我的應用"** 門戶啟動應用後收到 **"訪問被拒絕**"消息，則可以嘗試以下操作：

- 請確保您已安裝["我的應用安全登錄擴展"，](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)並且您使用的是[受支援的瀏覽器](my-apps-portal-end-user-access.md#supported-browsers)。

- 請確保您對應用使用正確的 URL，並且該 URL 位於**Internet 屬性>安全>可信網站**清單中。

- 請確保您的密碼正確且未過期。 有關詳細資訊，請參閱[重置工作或學校密碼](active-directory-passwords-update-your-own-password.md)。

- 確保您的驗證資訊是最新且準確的。 有關詳細資訊，請參閱[Azure 多重要素驗證對我意味著什麼？](multi-factor-authentication-end-user.md)或[更改安全資訊方法和資訊](security-info-add-update-methods-overview.md)。

- 清除瀏覽器的緩存並嘗試重新登錄。

如果在嘗試了這些操作後仍無法訪問你的應用，則必須與組織的説明台聯繫以獲得説明。

## <a name="next-steps"></a>後續步驟

登錄 **"我的應用"** 門戶後，您還可以更新個人資料和帳戶資訊、群組資訊和訪問審閱資訊（如果您具有許可權）。

- [訪問和使用"我的應用"門戶上的應用](my-apps-portal-end-user-access.md)。

- [更改您的個人資料資訊](my-apps-portal-end-user-update-profile.md)。

- [查看和更新與組相關的資訊](my-apps-portal-end-user-groups.md)。

- [執行您自己的訪問評論](my-apps-portal-end-user-access-reviews.md)。
