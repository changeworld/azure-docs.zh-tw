---
title: 設定 Microsoft 驗證
description: 瞭解如何將 Microsoft 帳戶驗證設定為 App Service 應用程式的身分識別提供者。
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842328"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>設定您的 App Service 應用程式以使用 Microsoft 帳戶登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service 使用 AAD 來支援個人 Microsoft 帳戶登入。

> [!NOTE]
> 個人 Microsoft 帳戶和組織帳戶都會使用 AAD 身分識別提供者。 此時，無法設定此身分識別提供者來支援這兩種類型的登入。

## <a name="register-microsoft-account"></a>向 Microsoft 帳戶註冊您的應用程式

1. 移至 Azure 入口網站中的[**應用程式註冊**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)。 如有需要，請使用您的 Microsoft 帳戶登入。
1. 選取 [**新增註冊**]，然後輸入應用程式名稱。
1. 在 [**支援的帳戶類型**] 底下，選取**任何組織目錄中的帳戶（任何 Azure AD 目錄-多租使用者）和個人 Microsoft 帳戶（例如 Skype、Xbox）**
1. 在 [重新**導向 uri**] 中，選取 [ **Web**]，然後輸入 `https://<app-domain-name>/.auth/login/aad/callback`。 以應用程式的功能變數名稱取代 *\<的應用程式功能變數名稱 >* 。  例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 請務必在 URL 中使用 HTTPS 配置。

1. 選取 [註冊]。
1. 複製**應用程式（用戶端）識別碼**。 稍後您將會用到此資訊。
1. 從左側窗格中，選取 **憑證 & 密碼** > 新的**用戶端密碼**。 輸入 [描述]，選取 [有效期間]，然後選取 [**新增**]。
1. 複製 [**憑證 & 密碼**] 頁面上顯示的值。 離開頁面之後，將不會再次顯示。

    > [!IMPORTANT]
    > 用戶端密碼值（密碼）是重要的安全性認證。 請勿與任何人共用密碼，或在用戶端應用程式內散佈密碼。

## <a name="secrets"></a>將 Microsoft 帳戶資訊新增至您的 App Service 應用程式

1. 在[Azure 入口網站]中，移至您的應用程式。
1. 選取 [**設定**] > [**驗證/授權**]，並確定**App Service 驗證**已**開啟**。
1. 在 [驗證提供者] 底下，選取 [Azure Active Directory]。 選取 [**管理模式]** 底下的 [ **Advanced** ]。 貼上您稍早取得的應用程式（用戶端）識別碼和用戶端密碼。 使用 [**簽發者 Url** ] 欄位的 **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** 。
1. 選取 [確定]。

   App Service 提供驗證，但不會限制對您網站內容和 Api 的已授權存取。 您必須在應用程式程式碼中授權使用者。

1. 選擇性若要限制 Microsoft 帳戶使用者的存取權，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Azure Active Directory 登入**]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 它也會將所有未驗證的要求重新導向為使用 AAD 進行驗證。 請注意，因為您已將**簽發者 Url**設定為使用 Microsoft 帳戶租使用者，所以只有個人帳戶 會成功通過驗證。

   > [!CAUTION]
   > 以這種方式限制存取適用于對您應用程式的所有呼叫，這對於具有公開可用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，讓應用程式手動啟動驗證。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]。

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="related-content"></a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 入口網站]: https://portal.azure.com/
