---
title: 設定 Facebook 驗證
description: 瞭解如何將 Facebook 身份驗證配置為應用服務或 Azure 功能應用的標識提供程式。
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438009"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>將應用服務或 Azure 功能應用設定為使用 Facebook 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文演示如何將 Azure 應用服務或 Azure 功能配置為使用 Facebook 作為身份驗證提供者。

要完成本文中的過程,您需要一個 Facebook 帳戶,該帳戶具有經過驗證的電子郵寄地址和手機號碼。 若要建立新的 Facebook 帳戶，請前往 [facebook.com]。

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>向 Facebook 註冊您的應用程式

1. 轉到[Facebook 開發人員]網站,並登錄您的 Facebook 帳戶認證。

   如果您沒有面向開發人員的 Facebook 帳戶,請選擇 **「開始」** 並按照註冊步驟操作。
1. 選擇 **「我的應用** > **添加新應用**」。
1. 在 **「顯示名稱」** 欄位中:
   1. 鍵入應用的唯一名稱。
   1. 提供您的**聯絡電子郵件**。
   1. 選擇 **「創建應用 ID」。**
   1. 完成安全檢查。

   將打開新 Facebook 應用的開發人員儀錶板。
1. 選擇**儀表板** > **Facebook 登入** > **設定** > **網路**。
1. 在**Facebook 登錄**下的左側導航中,選擇 **"設置**"。
1. 在 **''oAuth 重定向 URI**`https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`欄位中,輸入 。 請記住,請`<app-name>`替換為 Azure 應用服務應用的名稱。
1. 選取 [Save Changes] \(儲存變更\)****。
1. 在左側窗格中,選擇 **「設置** > **基本**」。。 
1. 在 **「應用機密」** 欄位中,選擇 **「顯示**」 。 複製**應用識別碼****和應用程式**的值。 稍後使用它們在 Azure 中配置應用服務應用。

   > [!IMPORTANT]
   >  用戶端密碼是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。
   >

1. 您用於註冊應用程式的 Facebook 帳戶是應用程式的管理員。 此時,只有管理員才能登錄到此應用程式。

   要驗證其他 Facebook 帳戶,請選擇 **「應用審閱**」並啟用**\<「使應用名稱>公開**」,使普通公眾能夠使用 Facebook 身份驗證存取應用。

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>將 Facebook 資訊加入應用程式

1. 登錄到 Azure[門戶]並導航到應用服務應用。
1. 選擇 **「設定** > **身份驗證/授權**」,並確保**應用服務身份驗證****處於打開。**
1. 選擇**Facebook**,然後粘貼到您以前獲得的應用 ID 和應用機密值中。 啟用應用程式所需的任何作用域。
1. 選取 [確定]  。

   ![移動應用 Facebook 設定的螢幕截圖][0]

    默認情況下,應用服務提供身份驗證,但不會限制對網站內容和 API 的授權訪問。 您需要授權應用程式代碼中的使用者。
1. ( 選擇性的 )要僅限制通過 Facebook 身份驗證的使用者訪問,請在未向**Facebook****驗證請求時設置"操作**"。 設置此功能時,應用需要對所有請求進行身份驗證。 它還將所有未經身份驗證的請求重定向到 Facebook 進行身份驗證。

   > [!CAUTION]
   > 以這種方式限制訪問適用於對應用的所有調用,對於具有公開可用主頁的應用(如許多單頁應用程式中)來說,這可能不可取。 對於此類應用程式,可能首選**允許匿名請求(不執行任何操作),** 以便應用手動啟動身份驗證本身。 有關詳細資訊,請參閱[身份驗證流](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]  。

現在,您已準備好在應用中使用 Facebook 進行身份驗證。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 開發人員]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 入口網站]: https://portal.azure.com/
