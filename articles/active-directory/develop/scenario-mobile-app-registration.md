---
title: 註冊呼叫 web Api 的行動應用程式 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立呼叫 web Api 的行動應用程式（應用程式的程式碼設定）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: f24b3c9256f759b87c705aae9b93c2d6bc74d30c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82652632"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>註冊呼叫 web Api 的行動應用程式

本文包含的指示可協助您註冊正在建立的行動應用程式。

## <a name="supported-account-types"></a>支援的帳戶類型

您的行動應用程式支援的帳戶類型取決於您想要啟用的體驗，以及您想要使用的流程。

### <a name="audience-for-interactive-token-acquisition"></a>取得互動式權杖的物件

大部分的行動應用程式都使用互動式驗證。 如果您的應用程式使用這種形式的驗證，您可以從任何[帳戶類型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登入使用者。

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>整合式 Windows 驗證、使用者名稱-密碼和 B2C 的物件

如果您有通用 Windows 平臺（UWP）應用程式，您可以使用整合式 Windows 驗證來登入使用者。 若要使用整合式 Windows 驗證或使用者名稱-密碼驗證，您的應用程式必須在您自己的企業營運（LOB）開發人員租使用者中登入使用者。 在獨立軟體廠商（ISV）案例中，您的應用程式可以在 Azure Active Directory 組織中登入使用者。 Microsoft 個人帳戶不支援這些驗證流程。

您也可以使用通過 B2C 授權單位和原則的社交身分識別來登入使用者。 若要使用此方法，您只能使用互動式驗證和使用者名稱-密碼驗證。 使用者名稱-密碼驗證目前僅在 Xamarin. iOS、Xamarin 和 UWP 上受到支援。

如需詳細資訊，請參閱[案例和支援的驗證流程](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)和[案例，以及支援的平臺和語言](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)。

## <a name="platform-configuration-and-redirect-uris"></a>平臺設定和重新導向 Uri  

### <a name="interactive-authentication"></a>互動式驗證

當您建立使用互動式驗證的行動應用程式時，最重要的註冊步驟就是重新導向 URI。 您可以透過 [驗證] 分頁[上的 [平臺**Authentication** ](https://aka.ms/MobileAppReg)設定] 來設定互動式驗證。

此體驗可讓您的應用程式透過 Microsoft Authenticator （在 Android 上為 Intune 公司入口網站）取得單一登入（SSO）。 它也會支援裝置管理原則。

應用程式註冊入口網站會提供預覽體驗，以協助您計算適用于 iOS 和 Android 應用程式的代理回復 URI：

1. 在應用程式註冊入口網站中，選取 [**驗證**]  >  **試用新的體驗**。

   ![[驗證] 分頁，您可以在其中選擇新的體驗](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 選取 [**新增平臺**]。

   ![新增平臺](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 當支援平臺清單時，請選取 [ **iOS**]。

   ![選擇行動應用程式](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 輸入您的套件組合識別碼，然後選取 [**註冊**]。

   ![輸入您的套件組合識別碼](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

當您完成這些步驟時，系統會為您計算 [重新導向 URI]，如下圖所示。

![產生的重新導向 URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

如果您想要手動設定重新導向 URI，您可以透過應用程式資訊清單來執行此動作。 以下是建議的資訊清單格式：

- **iOS**：`msauth.<BUNDLE_ID>://auth` 
  - 例如，輸入 `msauth.com.yourcompany.appName://auth`
- **Android**：`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - 您可以透過 KeyTool 命令，使用發行金鑰或 debug 金鑰來產生 Android 簽章雜湊。

### <a name="username-password-authentication"></a>使用者名稱-密碼驗證

如果您的應用程式只使用使用者名稱密碼驗證，則不需要為您的應用程式註冊重新導向 URI。 此流程會執行 Microsoft 身分識別平臺版本2.0 端點的往返。 您的應用程式不會在任何特定的 URI 上回呼。 

不過，您需要將應用程式識別為公用用戶端應用程式。 若要這麼做，請從應用程式的 [**驗證**] 區段開始。 在 [ **Advanced settings** ] 子區段的 [**預設用戶端類型**] 段落中，針對 [將**應用程式視為公用用戶端**] 問題選取 [**是]**。

## <a name="api-permissions"></a>API 權限

行動應用程式代表已登入的使用者呼叫 Api。 您的應用程式需要要求委派的許可權。 這些許可權也稱為「範圍」。 視您想要的經驗而定，您可以透過 Azure 入口網站以靜態方式要求委派的許可權。 或者，您可以在執行時間動態要求它們。 

藉由靜態登入權利，您可以讓系統管理員輕鬆地核准您的應用程式。 建議使用靜態註冊。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [程式碼設定](scenario-mobile-app-configuration.md)
