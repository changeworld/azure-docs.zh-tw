---
title: 設定 Microsoft 驗證
description: 了解如何將 Microsoft 帳戶驗證設定為 App Service 或 Azure Functions 應用程式的識別提供者。
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 68af882bf240b354bdad1afe322135c048576ed4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83772831"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>將 App Service 或 Azure Functions 應用程式設定為使用 Microsoft 帳戶登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定 Azure App Service 或 Azure Functions，以使用 AAD 來支援個人 Microsoft 帳戶登入。

> [!NOTE]
> 個人 Microsoft 帳戶和組織帳戶都會使用 AAD 識別提供者。 目前無法設定此識別提供者來同時支援這兩種類型的登入。

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>使用 Microsoft 帳戶註冊應用程式

1. 在 Azure 入口網站中移至[**應用程式註冊**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)。 如有需要，請使用您的 Microsoft 帳戶登入。
1. 選取 [新增註冊]，然後輸入應用程式名稱。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶 (任何 Azure AD 目錄 - 多租用戶) 和個人 Microsoft 帳戶 (例如 Skype、Xbox)]
1. 在 [重新導向 URI] 底下，選取 [Web]，然後輸入 `https://<app-domain-name>/.auth/login/aad/callback`。 *\<app-domain-name>* 以您應用程式的功能變數名稱取代。  例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 請務必在 URL 中使用 HTTPS 配置。

1. 選取 [註冊]。
1. 複製 [應用程式 (用戶端) 識別碼]。 稍後您將會用到此資訊。
1. 從左側窗格中，選取 [憑證和祕密] > [新增用戶端密碼]。 輸入描述、選取有效期間，然後選取 [新增]。
1. 複製 [憑證和祕密] 頁面上出現的值。 離開此頁面後，就不會再次顯示該值。

    > [!IMPORTANT]
    > 用戶端密碼值 (密碼) 是重要的安全性認證。 請勿與任何人共用密碼，或在用戶端應用程式內散佈密碼。

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>將 Microsoft 帳戶資訊新增至 App Service 應用程式

1. 在 [Azure 入口網站]中移至您的應用程式。
1. 選取 [設定] > [驗證/授權]，並確定 [App Service 驗證] 為 [開啟]。
1. 在 [驗證提供者] 底下，選取 [Azure Active Directory]。 選取 [管理模式] 底下的 [進階]。 貼上您稍早取得的應用程式 (用戶端) 識別碼和用戶端密碼。 針對 [簽發者 URL] 欄位使用 **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** 。
1. 選取 [確定]。

   App Service 會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。

1. (選擇性) 若要限制 Microsoft 帳戶使用者的存取權，請將 [當要求未經驗證時所要採取的動作] 設定為 [使用 Azure Active Directory 登入]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 其也會將所有未驗證的要求重新導向為使用 AAD 進行驗證。 請注意，因為您已將 [簽發者 URL] 設定為使用 Microsoft 帳戶租用戶，所以只有個人帳戶會成功通過驗證。

   > [!CAUTION]
   > 以這種方式限制存取，適用於對您應用程式的所有呼叫，不建議具有公開可用首頁的應用程式 (如許多單頁應用程式) 這麼做。 對於這類應用程式，您可能偏好 [允許匿名要求 (不執行動作)]，因此應用程式會自行手動啟動驗證。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]。

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 入口網站]: https://portal.azure.com/
