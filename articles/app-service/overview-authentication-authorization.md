---
title: 驗證和授權
description: 瞭解 Azure 應用服務和 Azure 函數中的內建身份驗證和授權支援,以及它如何説明保護應用免受未經授權的訪問。
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: fasttrack-edit
ms.openlocfilehash: f16b10f13c945dd7f1ae4fdc3f4e02dcd7c5a018
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437950"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure 應用程式服務和 Azure 函數中的身份驗證和授權

> [!NOTE]
> 此時,Azure 應用服務和 Azure 函數不支援[Azure 活動目錄 v2.0(](../active-directory/develop/v2-overview.md)包括[MSAL)。](../active-directory/develop/msal-overview.md) 請回來查看更新。
>

Azure App Service 提供內建的驗證和授權支援，因此您在 Web 應用程式、RESTful API 和行動裝置後端以及 [Azure Functions](../azure-functions/functions-overview.md) 中幾乎不需要寫入或完全無需寫入程式碼，即可登入使用者及存取資料。 本文說明 App Service 如何協助您簡化應用程式的驗證和授權。

若要有安全的驗證和授權，必須對安全性有深入了解，包括同盟、加密、[JSON Web 權杖 (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) 管理、[授與類型](https://oauth.net/2/grant-types/)等等。 App Service 會提供這些公用程式，以便您可以將更多的時間和精力花在為客戶提供商務價值上。

> [!IMPORTANT]
> 您無需使用此功能進行身份驗證和授權。 您可以在您選擇的 Web 框架中使用捆綁安全功能,也可以編寫自己的實用程式。 但是,請記住[,Chrome 80 正在對其實施 SameSite Cookie(](https://www.chromestatus.com/feature/5088147346030592)發佈日期在 2020 年 3 月左右)進行重大更改,而自定義遠端身份驗證或其他依賴於跨網站 Cookie 發佈的方案可能會在用戶端 Chrome 瀏覽器更新時中斷。 解決方法很複雜,因為它需要支援不同瀏覽器的不同 SameSite 行為。 
>
> 應用服務託管的ASP.NET酷睿 2.1 及以上版本已針對此重大更改進行了修補,並適當處理 Chrome 80 及較舊的瀏覽器。 此外,整個 2020 年 1 月,ASP.NET 框架 4.7.2 的相同修補程式正在應用服務實例上部署。 有關詳細資訊(包括如何知道應用是否已收到修補程式),請參閱[Azure 應用服務 SameSite Cookie 更新](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/)。
>

如需原生行動應用程式的專屬資訊，請參閱 [Azure App Service 的行動應用程式使用者驗證和授權](../app-service-mobile/app-service-mobile-auth.md)。

## <a name="how-it-works"></a>運作方式

驗證和授權模組會在與應用程式程式碼相同的沙箱中執行。 此模組啟用時，每個連入的 HTTP 要求會先通過此模組，再由應用程式程式碼處理。

![](media/app-service-authentication-overview/architecture.png)

此模組會處理應用程式的幾件事：

- 向指定提供者驗證使用者
- 驗證、儲存及重新整理權杖
- 管理已驗證的工作階段
- 將身分識別資訊插入要求標頭中

此模組會與應用程式程式碼分開執行，並且會使用應用程式設定加以設定。 不需要任何 SDK、特定語言或對應用程式程式碼進行任何變更。 

### <a name="userapplication-claims"></a>使用者/應用程式聲明

對於所有語言框架,App Service 通過將傳入權杖(無論是來自經過身份驗證的最終用戶還是客戶端應用程式)添加到請求標頭中,使代碼中的聲明可用。 在 ASP.NET 4.6 應用程式中，App Service 會使用已驗證的使用者宣告填入 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)，因此您可以遵循標準的 .NET 程式碼模式，包括 `[Authorize]` 屬性。 同樣地，在 PHP 應用程式中，App Service 會填入 `_SERVER['REMOTE_USER']` 變數。 對於 Java 應用程式,可以從[Tomcat servlet 訪問](containers/configure-language-java.md#authenticate-users-easy-auth)聲明。

對於[Azure](../azure-functions/functions-overview.md)`ClaimsPrincipal.Current`函數 ,不填充 .NET 代碼,但仍可以在請求標頭中找到使用者聲明,或從`ClaimsPrincipal`請求上下文甚至通過 綁定參數獲取物件。 有關詳細資訊[,請參閱使用客戶端識別](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)。

如需詳細資訊，請參閱[存取使用者宣告](app-service-authentication-how-to.md#access-user-claims)。

### <a name="token-store"></a>權杖存放區

App Service 會提供內建的權杖存放區，也就是與 Web 應用程式、API 或原生行動應用程式相關聯的權杖存放庫。 當您使用任何提供者啟用驗證時，此權杖存放區就會立即供應用程式使用。 如果應用程式程式碼需要代表使用者存取這些提供者的資料，例如： 

- 張貼至已驗證使用者的 Facebook 時間軸
- 使用 Microsoft 圖形 API 讀取使用者的公司資料

您通常必須撰寫程式碼，才能在應用程式中收集、儲存及重新整理這些權杖。 使用權杖存放區時，您只有在需要權杖時才會[取出權杖](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)，並在權杖失效時才會[告知 App Service 加以重新整理](app-service-authentication-how-to.md#refresh-identity-provider-tokens)。 

ID 權杖、存取權杖和刷新權杖快取用於經過身份驗證的工作階段,並且僅由關聯的使用者存取它們。  

如果您不需要在應用程式中使用權杖，可以將權杖存放區停用。

### <a name="logging-and-tracing"></a>記錄和追蹤

如果您[啟用應用程式記錄](troubleshoot-diagnostic-logs.md)，則會直接在記錄檔中看到驗證和授權追蹤。 如果您看到前所未有的身份驗證錯誤,則可以通過查看現有應用程式日誌來方便地查找所有詳細資訊。 如果您啟用[失敗要求追蹤](troubleshoot-diagnostic-logs.md)，就可以看到驗證和授權模組可能在失敗要求中所扮演的確切角色。 在追蹤記錄中，尋找名為 `EasyAuthModule_32/64` 的模組參考。 

## <a name="identity-providers"></a>識別提供者

App Service 使用[同盟身分識別](https://en.wikipedia.org/wiki/Federated_identity)，由第三方識別提供者為您管理使用者身分識別和驗證流程。 預設可用的識別提供者有五個： 

| 提供者 | 登入端點 |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft 帳戶](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [谷歌](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

當您利用上述其中一個提供者啟用驗證和授權時，其登入端點即可用來驗證使用者，以及用來驗證提供者的驗證權杖。 您可以輕鬆地為使用者提供任何數目的上述登入選項。 您也可以整合其他識別提供者或[您自己的自訂身分識別解決方案][custom-auth]。

## <a name="authentication-flow"></a>驗證流程

所有提供者的驗證流程皆相同，但會根據您是否要使用提供者的 SDK 登入而有所不同：

- 不使用提供者 SDK：應用程式會將同盟登入委派給 App Service。 瀏覽器應用程式通常是這種情況，可以向使用者顯示提供者的登入頁面。 伺服器程式碼會管理登入程序，因此也稱為「伺服器導向流程」__ 或「伺服器流程」__。 此案例適用於瀏覽器應用程式。 它也適用於使用 Mobile Apps 用戶端 SDK 將使用者登入的原生應用程式，因為 SDK 會開啟 Web 檢視，使用 App Service 驗證將使用者登入。 
- 使用提供者 SDK：應用程式會以手動方式將使用者登入提供者，然後將驗證權杖提交給 App Service 進行驗證。 無瀏覽器應用程式通常是這種情況，無法向使用者顯示提供者的登入頁面。 應用程式程式碼會管理登入程序，因此也稱為「用戶端導向流程」__ 或「用戶端流程」__。 此案例適用於 REST API、[Azure Functions](../azure-functions/functions-overview.md)、JavaScript 瀏覽器用戶端，以及在登入程序中需要更多彈性的瀏覽器應用程式。 它也適用於使用提供者 SDK 將使用者登入的原生行動應用程式。

> [!NOTE]
> 可以使用伺服器定向流對應用服務或[Azure 函數](../azure-functions/functions-overview.md)中另一個 REST API 的受信任瀏覽器應用的調用進行身份驗證。 如需詳細資訊，請參閱[自訂 App Service 中的驗證與授權](app-service-authentication-how-to.md)。
>

下表顯示驗證流程的步驟。

| 步驟 | 不使用提供者 SDK | 使用提供者 SDK |
| - | - | - |
| 1. 登入使用者 | 將用戶端重新導向至 `/.auth/login/<provider>`。 | 用戶端程式碼會直接使用提供者的 SDK 將使用者登入，並接收驗證權杖。 如需詳細資訊，請參閱提供者的文件。 |
| 2. 認證後 | 提供者會將用戶端重新導向至 `/.auth/login/<provider>/callback`。 | 用戶端代碼[將權杖從提供程式發佈到](app-service-authentication-how-to.md#validate-tokens-from-providers)`/.auth/login/<provider>`驗證。 |
| 3. 建立經過身份驗證的工作階段 | App Service 會將已驗證的 Cookie 新增至回應。 | App Service 會將自己的驗證權杖傳回至用戶端程式碼。 |
| 4. 提供經過身份認證的內容 | 用戶端會在後續要求中包含驗證 Cookie (瀏覽器會自動處理)。 | 用戶端程式碼會在 `X-ZUMO-AUTH` 標頭中顯示驗證權杖 (Mobile Apps 用戶端 SDK 會自動處理)。 |

對於用戶端瀏覽器，App Service 可以自動將所有未經驗證的使用者導向至 `/.auth/login/<provider>`。 您也可以向使用者顯示一或多個 `/.auth/login/<provider>` 連結，讓其使用選擇的提供者登入您的應用程式。

<a name="authorization"></a>

## <a name="authorization-behavior"></a>授權行為

在[Azure 門戶](https://portal.azure.com)中,可以在傳入請求未經過身份驗證時配置具有許多行為的應用服務授權。

![](media/app-service-authentication-overview/authorization-flow.png)

下列標題會說明可用選項。

### <a name="allow-anonymous-requests-no-action"></a>允許匿名要求(無操作 )

此選項將未經身份驗證的流量的授權延遲到應用程式代碼。 對於已驗證的要求，App Service 也會在 HTTP 標頭中一起傳送驗證資訊。 

此選項會提供更大的彈性來處理匿名要求。 例如，它可讓您向使用者[顯示多個登入提供者](app-service-authentication-how-to.md#use-multiple-sign-in-providers)。 不過，您必須撰寫程式碼。 

### <a name="allow-only-authenticated-requests"></a>僅允許已驗證的要求

選項為 [使用 \<提供者> 登入]****。 App Service 會將所有匿名要求重新導向至您所選提供者的 `/.auth/login/<provider>`。 如果匿名要求來自原生行動應用程式，則傳回的回應是 `HTTP 401 Unauthorized`。

使用此選項時，您不需要在應用程式中撰寫任何驗證程式碼。 您可以藉由檢查使用者的宣告來處理更精細的授權 (例如特定角色授權，請參閱[存取使用者宣告](app-service-authentication-how-to.md#access-user-claims))。

> [!CAUTION]
> 以這種方式限制訪問適用於對應用的所有調用,對於希望擁有公開主頁的應用,這可能不可取,如許多單頁應用程式。

## <a name="more-resources"></a>其他資源

[教學課程：在 Azure App Service 中端對端驗證和授權使用者 (Windows)](app-service-web-tutorial-auth-aad.md)  
[教學課程：在適用於 Linux 的 Azure App Service 中端對端驗證和授權使用者](containers/tutorial-auth-aad.md)  
[自訂 App Service 中的驗證與授權](app-service-authentication-how-to.md)

提供者專屬的使用說明指南：

* [如何設定 App 使用 Azure Active Directory 登入][AAD]
* [如何設定 App 使用 Facebook 登入][Facebook]
* [如何設定 App 以使用 Google 登入][Google]
* [如何設定 App 使用 Microsoft 帳戶登入][MSA]
* [如何設定 App 以使用 Twitter 登入][Twitter]
* [做法：針對應用程式使用自訂驗證][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
