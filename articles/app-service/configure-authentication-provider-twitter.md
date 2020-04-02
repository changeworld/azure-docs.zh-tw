---
title: 設定 Twitter 驗證
description: 瞭解如何將 Twitter 身份驗證配置為應用服務或 Azure 功能應用的標識提供者。
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519902"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>套用服務或 Azure 功能應用設定為使用 Twitter 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文演示如何將 Azure 應用服務或 Azure 函數配置為使用 Twitter 作為身份驗證提供者。

要完成本文中的過程,您需要一個具有已驗證電子郵件地址和電話號碼的 Twitter 帳戶。 若要建立新的 Twitter 帳戶，請前往 [twitter.com]。

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>向 Twitter 註冊您的應用程式

1. 登錄到 Azure[門戶]並轉到應用程式。 複製您的 **URL**。 您將使用它來配置您的 Twitter 應用。
1. 跳到[Twitter 開發人員]網站,使用 Twitter 帳戶憑據登入,然後選擇「**創建應用**」 。
1. 輸入新**應用程式的應用程式與****應用程式 。** 將應用程式的**URL**貼上到**網站網址欄**位中。 在 **「回檔 URL」** 部份中,輸入應用服務應用的 HTTPS 網址 並附加路徑`/.auth/login/twitter/callback`。 例如： `https://contoso.azurewebsites.net/.auth/login/twitter/callback` 。
1. 在頁面底部,在 **「告訴我們如何使用此應用程式**」中鍵入至少 100 個字元,然後選擇「**創建**」。 。 在彈出視窗中再次按下 **「再次創建**」。 將顯示應用程式詳細資訊。
1. 選取 [ **金鑰和存取權杖** ] 索引標籤。

   記下這些值:
   - API 金鑰
   - API 金鑰

   > [!NOTE]
   > API 密鑰是一個重要的安全憑據。 請勿將此密碼告訴任何人或隨應用程式一起散發。

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>將 Twitter 資訊新增至應用程式

1. 轉到[Azure 門戶]中的應用程式。
1. 選擇 **「設定** > **身份驗證/授權**」,並確保**應用服務身份驗證****處於打開。**
1. 選擇**推特**。
1. 粘貼之前`API key`獲得`API secret key`的 和 值。
1. 選取 [確定]  。

   ![移動應用推特設定的螢幕截圖][1]

   默認情況下,應用服務提供身份驗證,但不限制對網站內容和 API 的授權訪問。 您必須在應用程式程式碼中授權使用者。

1. (選擇性) 若要限制只有透過 Twitter 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作]**** 設為 [Twitter]****。 設置此功能時,應用需要對所有請求進行身份驗證。 它還將所有未經身份驗證的請求重定向到 Twitter 進行身份驗證。

   > [!CAUTION]
   > 以這種方式限制訪問適用於對應用的所有調用,對於具有公開可用主頁的應用(如許多單頁應用程式中)來說,這可能不可取。 對於此類應用程式,可能首選**允許匿名請求(不執行任何操作),** 以便應用手動啟動身份驗證本身。 有關詳細資訊,請參閱[身份驗證流](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]  。

現在，您已可在應用程式中使用 Twitter 進行驗證。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure 入口網站]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
