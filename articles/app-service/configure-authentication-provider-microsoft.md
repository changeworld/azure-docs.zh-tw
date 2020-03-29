---
title: 設定 Microsoft 驗證
description: 瞭解如何將 Microsoft 帳戶身份驗證配置為應用服務應用的標識提供程式。
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842328"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>將應用服務應用配置為使用 Microsoft 帳戶登錄

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題介紹如何將 Azure 應用服務配置為使用 AAD 支援個人 Microsoft 帳戶登錄。

> [!NOTE]
> 個人 Microsoft 帳戶和組織帳戶都使用 AAD 標識提供程式。 此時，無法配置此標識提供程式以支援這兩種類型的登錄。

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>使用 Microsoft 帳戶註冊應用程式

1. 轉到 Azure 門戶中[**的應用註冊**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)。 如果需要，請使用 Microsoft 帳戶登錄。
1. 選擇 **"新建註冊**"，然後輸入應用程式名稱。
1. 在 **"支援帳戶類型**"下，選擇**任何組織目錄中的帳戶（任何 Azure AD 目錄 - 多租戶）和個人 Microsoft 帳戶（例如 Skype、Xbox）**
1. 在**重定向 URI 中**，選擇**Web，** 然後`https://<app-domain-name>/.auth/login/aad/callback`輸入 。 將*\<應用功能變數名稱>* 替換為應用的功能變數名稱。  例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 請務必在 URL 中使用 HTTPS 方案。

1. 選取 [註冊]****。
1. 複製**應用程式（用戶端）ID**。 稍後您將會用到此資訊。
1. 從左側窗格中，選擇**證書&機密** > **新用戶端機密**。 輸入說明，選擇有效期，然後選擇 **"添加**"。
1. 複製顯示在**證書&機密**頁上的值。 離開頁面後，它將不會再次顯示。

    > [!IMPORTANT]
    > 用戶端金鑰值（密碼）是一個重要的安全憑據。 請勿與任何人共用密碼，或在用戶端應用程式內散佈密碼。

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>將 Microsoft 帳戶資訊新增至 App Service 應用程式

1. 轉到[Azure 門戶]中的應用程式。
1. 選擇 **"設置** > **身份驗證/授權**"，並確保**應用服務身份驗證****處於打開。**
1. 在 [驗證提供者]**** 底下，選取 [Azure Active Directory]****。 選擇 **"管理模式下的先進****"。** 粘貼到您之前獲得的應用程式（用戶端）ID 和用戶端金鑰中。 用於**https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0****頒發者 Url**欄位。
1. 選取 [確定]****。

   應用服務提供身份驗證，但不限制對網站內容和 API 的授權訪問。 您必須在應用程式程式碼中授權使用者。

1. （可選）要限制對 Microsoft 帳戶使用者的訪問，請設置 **"操作"，當請求未通過身份驗證**時，請**使用 Azure 活動目錄 登錄**。 設置此功能時，應用需要對所有請求進行身份驗證。 它還重定向所有未經身份驗證的請求，以便使用 AAD 進行身份驗證。 請注意，由於已將**頒發者 URL**配置為使用 Microsoft 帳戶租戶，因此只有個人身份驗證。

   > [!CAUTION]
   > 以這種方式限制訪問適用于對應用的所有調用，對於具有公開可用主頁的應用（如許多單頁應用程式中）來說，這可能不可取。 對於此類應用程式，可能首選**允許匿名請求（不執行任何操作），** 以便應用手動啟動身份驗證本身。 有關詳細資訊，請參閱[身份驗證流](overview-authentication-authorization.md#authentication-flow)。

1. 選取 [儲存]****。

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 門戶]: https://portal.azure.com/
