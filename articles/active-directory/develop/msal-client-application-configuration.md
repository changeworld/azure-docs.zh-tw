---
title: 用戶端應用程式設定 (MSAL) |蔚藍
titleSuffix: Microsoft identity platform
description: 深入瞭解使用 Microsoft 驗證程式庫 (MSAL) 的公用用戶端和機密用戶端應用程式的設定選項。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 910007109e4751cf2fd509d1d568c66ae2a22cd2
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92200826"
---
# <a name="application-configuration-options"></a>應用程式設定選項

在您的程式碼中，您會初始化新的公用或機密用戶端應用程式 (或使用者代理程式，以便 MSAL.js) 來驗證和取得權杖。 當您在 Microsoft 驗證程式庫 (MSAL) 中初始化用戶端應用程式時，可以設定許多設定選項。 這些選項分為兩個群組：

- 註冊選項，包括：
    - [授權](#authority) (是由應用程式的身分識別提供者 [實例](#cloud-instance) 和登入物件所組成，而且可能是租 [使用者](#application-audience) 識別碼) 。
    - [用戶端識別碼](#client-id)。
    - 重新[導向 URI](#redirect-uri)。
    - ) 的機密用戶端應用程式的[用戶端密碼](#client-secret) (。
- [記錄選項](#logging)，包括記錄層級、個人資料的控制，以及使用程式庫的元件名稱。

## <a name="authority"></a>授權單位

授權單位是一個 URL，指出 MSAL 可以要求權杖的目錄。 一般授權單位為：

- HTTPs \: //login.microsoftonline.com/ \<tenant\> /，其中 &lt; 租使用者 &gt; 是 Azure Active Directory (Azure AD) 租使用者的租使用者識別碼，或與此 Azure AD 租使用者相關聯的網域。 僅用來登入特定組織的使用者。
- HTTPs \: //login.microsoftonline.com/common/。 用來登入具有公司和學校帳戶或個人 Microsoft 帳戶的使用者。
- HTTPs \: //login.microsoftonline.com/organizations/。 用來以公司和學校帳戶登入使用者。
- HTTPs \: //login.microsoftonline.com/consumers/。 用來登入只有個人 Microsoft 帳戶的使用者 (先前稱為 Windows Live ID 帳戶) 。

授權單位設定必須與應用程式註冊入口網站中宣告的內容一致。

授權單位 URL 由實例和物件組成。

授權單位可以是：
- Azure AD 的雲端授權單位。
- Azure AD B2C 授權單位。 請參閱 [B2C 細節](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)。
- Active Directory 同盟服務 (AD FS) 授權單位。 請參閱 [AD FS 支援](https://aka.ms/msal-net-adfs-support)。

Azure AD cloud 授權單位有兩個部分：
- 身分識別提供者 *實例*
- 應用程式的登入*物件*

實例和物件可以串連並提供為授權單位 URL。 在 MSAL 3 之前的 MSAL.NET 版本中。*x*，您必須根據想要鎖定的雲端和登入物件，自行撰寫授權單位。  下圖顯示授權單位 URL 的組成方式：

![授權單位 URL 的組成方式](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>雲端實例

*實例*可用來指定您的應用程式是從 Azure 公用雲端或從國家雲端簽署使用者。 在您的程式碼中使用 MSAL，您可以使用列舉來設定 Azure 雲端實例，或將 URL 傳遞至 [國家雲端實例](authentication-national-cloud.md#azure-ad-authentication-endpoints) 作為 `Instance` 成員 (如果您知道它) 。

如果同時 `Instance` 指定了和，則 MSAL.NET 將擲回明確的例外狀況 `AzureCloudInstance` 。

如果您未指定實例，您的應用程式將會以 (URL) 的 Azure 公用雲端實例為目標 `https://login.onmicrosoftonline.com` 。

## <a name="application-audience"></a>應用程式物件

登入物件取決於您應用程式的商務需求：
- 如果您是企業營運 (LOB) 開發人員，您可能會產生只會在您的組織中使用的單一租使用者應用程式。 在這種情況下，您需要指定組織，其租使用者識別碼 (Azure AD 實例的識別碼) 或與 Azure AD 實例相關聯的功能變數名稱。
- 如果您是 ISV，您可能會想要以任何組織中的公司和學校帳戶登入使用者，或是在某些組織中使用 (多租使用者應用程式) 來登入使用者。 但您也可以讓使用者使用他們的個人 Microsoft 帳戶登入。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>如何在您的程式碼/設定中指定物件

在程式碼中使用 MSAL，您可以使用下列其中一個值來指定物件：
- Azure AD 授權物件列舉
- 租使用者識別碼，可以是：
  - GUID () 的單一租使用者應用程式的 Azure AD 實例識別碼
  - 與您的 Azure AD 實例相關聯的功能變數名稱 (也適用于單一租使用者應用程式) 
- 其中一個預留位置作為租使用者識別碼，以取代 Azure AD 授權物件列舉：
    - `organizations` 針對多租使用者應用程式
    - `consumers` 使用個人帳戶登入使用者
    - `common` 使用其公司和學校帳戶或個人 Microsoft 帳戶登入使用者

如果您同時指定 Azure AD 授權物件和租使用者識別碼，MSAL 會擲回有意義的例外狀況。

如果您未指定物件，您的應用程式將以 Azure AD 和個人 Microsoft 帳戶為目標，作為物件。  (也是一樣，它的行為就如同 `common` 指定的。 ) 

### <a name="effective-audience"></a>有效物件

如果您在應用程式中設定的物件和應用程式註冊中指定的物件有交集) ，您應用程式的有效物件將會是最小 (。 事實上， [應用程式註冊](https://aka.ms/appregistrations) 的體驗可讓您為應用程式 () 支援的帳戶類型，指定物件。 如需詳細資訊，請參閱 [快速入門：使用 Microsoft 身分識別平臺註冊應用程式](quickstart-register-app.md)。

目前，只讓應用程式使用個人 Microsoft 帳戶登入使用者的唯一方法，就是設定這兩項設定：
- 將應用程式註冊物件設定為 `Work and school accounts and personal accounts` 。
- 在您的程式碼/設定中將物件設定為 `AadAuthorityAudience.PersonalMicrosoftAccount` (或 = 「取用 `TenantID` 者」 ) 。

## <a name="client-id"></a>用戶端識別碼

用戶端識別碼是在註冊應用程式時 Azure AD 指派給您的應用程式 (用戶端) 識別碼的唯一應用程式。

## <a name="redirect-uri"></a>重新導向 URI

重新導向 URI 是身分識別提供者將用來傳送安全性權杖的 URI。

### <a name="redirect-uri-for-public-client-apps"></a>公用用戶端應用程式的重新導向 URI

如果您是使用 MSAL 的公用用戶端應用程式開發人員：
- 您想要 `.WithDefaultRedirectUri()` 在桌面或 UWP 應用程式中使用 (MSAL.NET 4.1 +) 。 這個方法會將公用用戶端應用程式的 [重新導向 uri] 屬性設定為公用用戶端應用程式的預設建議重新導向 uri。

  平台  | 重新導向 URI
  ---------  | --------------
  桌面應用程式 ( .NET FW)  | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | 的值 `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` 。 這可透過將值設定為 WebAuthenticationBroker. GetCurrentApplicationCallbackUri ( # A1 的結果，將值設定為您必須註冊的，以啟用與瀏覽器的 SSO。
  .NET Core | `https://localhost`. 這可讓使用者使用系統瀏覽器進行互動式驗證，因為 .NET Core 目前沒有內嵌 web 視圖的 UI。

- 如果您要建立不支援 broker 的 Xamarin Android 和 iOS 應用程式，則不需要新增重新導向 URI (重新導向 URI 會自動設定為 `msal{ClientId}://auth` Xamarin android 和 ios

- 您必須在 [應用程式註冊](https://aka.ms/appregistrations)中設定重新導向 URI：

   ![應用程式註冊中的重新導向 URI](media/msal-client-application-configuration/redirect-uri.png)

您可以使用 `RedirectUri` 屬性 (（例如，如果您使用訊息代理程式) ）來覆寫重新導向 URI。 以下是該案例的重新導向 Uri 範例：

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc：//com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $ "msauth。{配套識別碼}：//auth ";

如需其他 iOS 詳細資料，請參閱 [將使用 Microsoft Authenticator 的 ios 應用程式從 ADAL.NET 遷移至 MSAL.NET](msal-net-migration-ios-broker.md) ，並 [利用 ios 上的訊息代理](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)程式。
如需其他 Android 詳細資料，請參閱 [android 中](msal-android-single-sign-on.md)的代理驗證。

### <a name="redirect-uri-for-confidential-client-apps"></a>機密用戶端應用程式的重新導向 URI

針對 web 應用程式，重新導向 URI (或回復 URI) 是 Azure AD 將用來將權杖傳回給應用程式的 URI。 如果機密應用程式是其中一種，則此 URI 可以是 web 應用程式/web API 的 URL。 重新導向 URI 必須在應用程式註冊中註冊。 當您部署一開始在本機測試的應用程式時，此註冊特別重要。 然後，您必須在應用程式註冊入口網站中新增已部署應用程式的回復 URL。

針對 daemon 應用程式，您不需要指定重新導向 URI。

## <a name="client-secret"></a>用戶端密碼

此選項會指定機密用戶端應用程式的用戶端密碼。 此密碼 (應用程式密碼) 是由應用程式註冊入口網站提供，或在使用 PowerShell AzureAD、PowerShell AzureRM 或 Azure CLI 的應用程式註冊期間提供給 Azure AD。

## <a name="logging"></a>記錄

其他設定選項可啟用記錄和疑難排解。 請參閱 [記錄](msal-logging.md) 文章，以取得如何使用這些專案的詳細資料。

## <a name="next-steps"></a>後續步驟

瞭解如何 [使用 MSAL.NET 來具現化用戶端應用程式](msal-net-initializing-client-applications.md)。
瞭解如何 [使用 MSAL.js來具現化用戶端應用程式 ](msal-js-initializing-client-applications.md)。
