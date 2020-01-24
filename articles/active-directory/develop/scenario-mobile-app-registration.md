---
title: 註冊呼叫 web Api 的行動應用程式 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立呼叫 web Api 的行動應用程式（應用程式的程式碼設定）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 33510015f4f05661ad2ea041b1fd3da0e8bfb1ed
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702074"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>呼叫 web Api 的行動應用程式-應用程式註冊

本文包含用來建立行動應用程式的應用程式註冊指示。

## <a name="supported-accounts-types"></a>支援的帳戶類型

行動應用程式中支援的帳戶類型取決於您想要啟用的體驗，以及您想要使用的流程。

### <a name="audience-for-interactive-token-acquisition"></a>取得互動式權杖的物件

大部分的行動應用程式都使用互動式驗證。 如果是您的情況，您可以從任何[帳戶類型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登入使用者

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>整合式驗證、使用者名稱/密碼和 B2C 的物件

- 如果您想要使用整合式 Windows 驗證（在 UWP 應用程式中可能）或使用者名稱/密碼，您的應用程式必須在您自己的租使用者（LOB 開發人員）或 Azure Active directory 組織（ISV 案例）中登入使用者。 Microsoft 個人帳戶不支援這些驗證流程
- 如果您使用社交身分識別來登入使用者，並傳遞 B2C 授權單位和原則，您只能使用互動式和使用者名稱密碼驗證。 使用者名稱-目前只有在 Xamarin. iOS、Xamarin 和 UWP 上才支援密碼。

如需完整的圖片，請參閱[案例和支援的驗證流程](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)和[案例，以及支援的平臺和語言](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>平臺設定和重新導向 Uri  

### <a name="interactive-authentication"></a>互動式驗證

使用互動式驗證來建立行動應用程式時，最重要的註冊步驟是重新導向 URI。 您可以透過[驗證](https://aka.ms/MobileAppReg) 分頁中的 [平臺設定] 來設定此選項。

此體驗可讓您的應用程式透過 Microsoft Authenticator （和 Android 上的 Intune 公司入口網站）取得單一登入（SSO），並支援裝置管理原則。

請注意，應用程式註冊入口網站中有預覽體驗，可協助您計算適用于 iOS 和 Android 應用程式的代理回復 URI：

1. 在應用程式註冊中, 選擇 **驗證** 及選擇 **試用新的體驗** 
   ![image](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 選取 **新增平臺** 
   ![image](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 當支援平臺清單時, 請選取 **iOS** 
   ![image](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 依要求輸入您的配套識別碼, 然後按下 **註冊** 
   ![image](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. 系統會為您計算重新導向 URI。
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

如果您想要手動設定重新導向 URI，您可以透過應用程式資訊清單來執行此動作。 建議的格式如下：

- ***iOS***： `msauth.<BUNDLE_ID>://auth` （例如 "msauth. yourcompany .com：//auth"）
- ***Android***： `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - 您可以透過 KeyTool 命令，使用 release 或 debug 金鑰來產生 Android 簽名雜湊。

### <a name="username-password"></a>使用者名稱密碼

如果您的應用程式只使用使用者名稱/密碼，您就不需要為應用程式註冊重新導向 URI。 事實上，此流程會執行 Microsoft 身分識別平臺 v2.0 端點的來回行程，而您的應用程式將不會在任何特定的 URI 上呼叫。 不過，您需要表達應用程式是公用用戶端應用程式。 這項設定的達成方式是前往您應用程式的 [**驗證**] 區段，然後在 [ **Advanced settings** ] 子節中，選擇 [**是]** ，將 [將**應用程式視為公用用戶端**] （在**預設的用戶端類型**段落）

## <a name="api-permissions"></a>API 權限

行動應用程式代表已登入的使用者呼叫 Api。 您的應用程式需要要求委派的許可權，也稱為範圍。 視所需的經驗而定，這可以透過 Azure 入口網站以靜態方式完成，或在執行時間以動態方式執行。 靜態註冊許可權可讓系統管理員輕鬆地核准您的應用程式，並建議您這麼做。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [程式碼設定](scenario-mobile-app-configuration.md)
