---
title: 設定 Twitter 驗證
description: 瞭解如何將 Twitter 驗證設定為 App Service 應用程式的身分識別提供者。
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: seodec18
ms.openlocfilehash: 794f671b36b5aeb9f19cf5d80e488500cedb1098
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207129"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>設定 App Service 應用程式以使用 Twitter 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文說明如何將 Azure App Service 設定為使用 Twitter 做為驗證提供者。

若要完成本文中的程式，您需要有已驗證電子郵件地址和電話號碼的 Twitter 帳戶。 若要建立新的 Twitter 帳戶，請前往 [twitter.com]。

## <a name="register"></a>向 Twitter 註冊您的應用程式

1. 登入[Azure 入口網站]並移至您的應用程式。 複製您的 **URL**。 您將使用它來設定您的 Twitter 應用程式。
1. 前往[Twitter Developers]網站，使用您的 Twitter 帳號憑證登入，然後選取 [**建立應用程式**]。
1. 輸入新應用程式的 [**應用程式名稱**] 和 [**應用程式描述**]。 在 [**網站 url** ] 欄位中貼上您的應用程式**url** 。 在 [**回呼 url** ] 區段中，輸入 App Service 應用程式的 HTTPS URL，並將路徑附加 `/.auth/login/twitter/callback`。 例如： `https://contoso.azurewebsites.net/.auth/login/twitter/callback` 。
1. 在頁面底部的 [**告訴我們此應用程式的使用方式**] 中，輸入至少100個字元，然後選取 [**建立**]。 在快顯視窗中，再次按一下 [**建立**]。 隨即顯示應用程式詳細資料。
1. 選取 [ **金鑰和存取權杖** ] 索引標籤。

   記下這些值：
   - API 金鑰
   - API 秘密金鑰

   > [!NOTE]
   > API 秘密金鑰是重要的安全性認證。 請勿將此密碼告訴任何人或隨應用程式一起散發。

## <a name="secrets"></a>將 Twitter 資訊新增至您的應用程式

1. 在[Azure 入口網站]中，移至您的應用程式。
1. 選取 [**設定**] > [**驗證/授權**]，並確定**App Service 驗證**已**開啟**。
1. 選取 [ **Twitter**]。
1. 貼上您先前取得的 `API key` 和 `API secret key` 值。
1. 選取 [確定]。

   ![行動應用程式 Twitter 設定的螢幕擷取畫面][1]

   根據預設，App Service 會提供驗證，但不會限制對您網站內容和 Api 的已授權存取。 您必須在應用程式程式碼中授權使用者。

1. (選擇性) 若要限制只有透過 Twitter 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Twitter]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 它也會將所有未經驗證的要求重新導向至 Twitter 以進行驗證。

   > [!CAUTION]
   > 以這種方式限制存取適用于對您應用程式的所有呼叫，這對於具有公開可用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，讓應用程式手動啟動驗證。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]。

現在，您已可在應用程式中使用 Twitter 進行驗證。

## <a name="related-content"></a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure 入口網站]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
