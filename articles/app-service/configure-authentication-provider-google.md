---
title: 設定 Google 驗證
description: 瞭解如何設定 Google 驗證做為 App Service 或 Azure Functions 應用程式的身分識別提供者。
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519935"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>設定您的 App Service 或 Azure Functions 應用程式以使用 Google 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何將 Azure App Service 或 Azure Functions 設定為使用 Google 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。 若要建立新的 Google 帳戶，請前往 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register-your-application-with-google"></a><a name="register"> </a>向 Google 註冊您的應用程式

1. 依照 google 登[入伺服器端應用程式](https://developers.google.com/identity/sign-in/web/server-side-flow)的 google 檔，建立用戶端識別碼和用戶端密碼。 不需要進行任何程式碼變更。 只要使用下列資訊：
    - 針對**授權的 JavaScript 來源**， `https://<app-name>.azurewebsites.net`請在應用程式名稱中* \<* 使用與您的應用程式名稱>。
    - 針對**授權**的重新導向 URI `https://<app-name>.azurewebsites.net/.auth/login/google/callback`，請使用。
1. 複製 [應用程式識別碼] 和 [應用程式密碼] 的值。

    > [!IMPORTANT]
    > 應用程式密碼是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>將 Google 資訊新增至應用程式

1. 在[Azure 入口網站]中，移至您的 App Service 應用程式。
1. 選取 [**設定** > ] [**驗證/授權**]，並確定**App Service 驗證**已**開啟**。
1. 選取 [ **Google**]，然後貼上您先前取得的 [應用程式識別碼] 和 [應用程式密碼] 值。 啟用應用程式所需的任何範圍。
1. 選取 [確定]  。

   App Service 提供驗證，但不會限制對您網站內容和 Api 的已授權存取。 如需詳細資訊，請參閱[授權或拒絕使用者](app-service-authentication-how-to.md#authorize-or-deny-users)。

1. 選擇性若要將網站存取限制為只有 Google 驗證的使用者，請將 [**要求未經驗證時所採取的動作**] 設定為 [ **google**]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 它也會將所有未經驗證的要求重新導向至 Google 進行驗證。

    > [!CAUTION]
    > 以這種方式限制存取適用于對您應用程式的所有呼叫，這對於具有公開可用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，讓應用程式手動啟動驗證。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]  。

現在，您已可在應用程式中使用 Google 進行驗證。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 入口網站]: https://portal.azure.com/

