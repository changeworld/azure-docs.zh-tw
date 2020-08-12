---
title: 建立 Azure AD 使用者登入的應用程式
titleSuffix: Microsoft identity platform
description: 示範如何建立可從任何 Azure Active Directory 租使用者登入使用者的多租使用者應用程式。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: d3810d3998f82127fc32327f696a2784382b4537
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117034"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>操作說明：讓任何 Azure Active Directory (AD) 使用者以多租用戶應用程式的模式登入

如果您提供「軟體即服務」(SaaS) 應用程式給許多組織，您可以將應用程式設定為可接受來自任何 Azure Active Directory (Azure AD) 租用戶的登入。 這種設定稱為*讓您的應用程式成為多租使用者*。 任何 Azure AD 租用戶中的使用者在同意搭配您的應用程式使用其帳戶之後，便可登入您的應用程式。

如果您的現有應用程式有自身的帳戶系統，或支援其他雲端提供者的其他登入方式，您就可以輕鬆新增任何租用戶的 Azure AD 登入功能。 只要註冊您的應用程式、透過 OAuth2、OpenID Connect 或 SAML 新增登入程式碼，然後在您的應用程式中放入「[使用 Microsoft 帳戶登入」按鈕][AAD-App-Branding]即可。

> [!NOTE]
> 本文假設您已經熟悉如何為 Azure AD 建置單一租用戶應用程式。 如果您並不熟悉，請由[開發人員指南首頁][AAD-Dev-Guide]的其中一堂快速入門開始。

將您的應用程式轉換成 Azure AD 多租用戶應用程式包含四個簡單的步驟︰

1. [將您的應用程式註冊更新為多租用戶應用程式](#update-registration-to-be-multi-tenant)
2. [更新您的程式碼以將要求傳送給 /common 端點](#update-your-code-to-send-requests-to-common)
3. [更新您的程式碼以處理多個簽發者值](#update-your-code-to-handle-multiple-issuer-values)
4. [取得使用者和系統管理員的同意並進行適當的程式碼變更](#understand-user-and-admin-consent)

讓我們仔細看看每個步驟。 您也可以直接跳到範例[組建使用 Azure AD 和 OpenID connect 呼叫 Microsoft Graph 的多租使用者 SaaS web 應用程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)。

## <a name="update-registration-to-be-multi-tenant"></a>將註冊更新成多租用戶

Azure AD 中的 Web 應用程式/API 註冊預設是單一租用戶。 您可以在[Azure 入口網站][AZURE-portal]中的應用程式註冊的 [**驗證**] 窗格上尋找**支援的帳戶類型**，並將它設定為**任何組織目錄中的帳戶**，藉此讓註冊多租使用者。

在 Azure AD 中，應用程式的「應用程式識別碼 URI」必須具全域唯一性，您才能將其設為多租用戶應用程式。 「應用程式識別碼 URI」是其中一種可在通訊協定訊息中識別應用程式的方式。 在單一租用戶應用程式中，只要該租用戶內有唯一的應用程式識別碼 URI 就已足夠。 就多租用戶應用程式而言，該 URI 則必須具全域唯一性，Azure AD 才能在所有租用戶中找到該應用程式。 系統會透過要求「應用程式識別碼 URI」必須具有與已驗證的 Azure AD 租用戶網域相符的主機名稱，來強制執行全域唯一性。

根據預設，透過 Azure 入口網站建立的應用程式具有在建立應用程式時設定的全域唯一應用程式識別碼 URI，但您可以變更此值。 例如，如果租用戶的名稱是 contoso.onmicrosoft.com，則有效的「應用程式識別碼 URI」會是 `https://contoso.onmicrosoft.com/myapp`。 如果租用戶具有已驗證的網域 `contoso.com`，則有效的「應用程式識別碼 URI」也會是 `https://contoso.com/myapp`。 如果「應用程式識別碼 URI」沒有按照這個模式，將應用程式設定成多租用戶時就會失敗。

> [!NOTE]
> Native client 註冊以及 Microsoft 身分[識別平臺應用程式](./v2-overview.md)預設為多租使用者。 因此，您不需要採取任何動作來將這些應用程式註冊轉換成多租用戶。

## <a name="update-your-code-to-send-requests-to-common"></a>將您的程式碼更新成將要求傳送給 /common

在單一租用戶應用程式中，登入要求會傳送至租用戶的登入端點。 例如，以 contoso.onmicrosoft.com 來說，端點會是：`https://login.microsoftonline.com/contoso.onmicrosoft.com`。 傳送給租用戶端點的要求可以讓該租用戶中的使用者 (或來賓) 登入該租用戶中的應用程式。

使用多租用戶應用程式時，應用程式事先並不知道使用者來自哪個租用戶，因此您無法將要求傳送給租用戶的端點。 反之，其會將要求傳送給在跨所有 Azure AD 租用戶進行多工作業的端點：`https://login.microsoftonline.com/common`

當 Microsoft 身分識別平臺在/common 端點上收到要求時，它會將使用者登入，因此會探索使用者來自哪個租使用者。 /Common 端點適用于 Azure AD 所支援的所有驗證通訊協定： OpenID Connect、OAuth 2.0、SAML 2.0 和 WS-同盟。

然後，傳給應用程式的登入回應會包含代表使用者的權杖。 權杖中的簽發者值會告知應用程式該使用者來自哪個租用戶。 從 /common 端點傳回回應時，權杖中的簽發者值會與使用者的租用戶對應。

> [!IMPORTANT]
> /common 端點不是租用戶，也不是簽發者，而只是一個多工器。 使用 /common 時，必須更新您應用程式中用來驗證權杖的邏輯，以便將這一點納入考量。

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>將您的程式碼更新成可處理多個簽發者值

Web 應用程式和 web Api 會接收並驗證來自 Microsoft 身分識別平臺的權杖。

> [!NOTE]
> 雖然原生用戶端應用程式會要求和接收來自 Microsoft 身分識別平臺的權杖，但他們會這麼做，將它們傳送至 Api，並在其中進行驗證。 原生應用程式不會驗證權杖，而且必須將它們視為不透明。

讓我們看看應用程式如何驗證它從 Microsoft 身分識別平臺收到的權杖。 單一租用戶應用程式通常會採用類似以下的端點值：

```http
    https://login.microsoftonline.com/contoso.onmicrosoft.com
```

並使用它來建構中繼資料 URL (在此例中為 OpenID Connect)，例如︰

```http
    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

以下載用來驗證權杖的兩項關鍵資訊︰租用戶的簽署金鑰和簽發者值。 每個 Azure AD 租用戶都有具有採用下列格式的唯一簽發者值︰

```http
    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

其中的 GUID 值是租用戶的租用戶識別碼重新命名安全版本。 如果您選取上述的 `contoso.onmicrosoft.com` 中繼資料連結，即可在文件中看到這個簽發者值。

當單一租用戶應用程式驗證權杖時，它會根據中繼資料文件中的簽署金鑰來檢查權杖的簽章。 這項檢查可讓它確定權杖中的簽發者值符合中繼資料文件中找到的值。

由於 /common 端點既不對應租用戶也不是簽發者，所以當您檢查 /common 中繼資料中的簽發者值時，它擁有的是一個樣板化的 URL 而不是實際值︰

```http
    https://sts.windows.net/{tenantid}/
```

因此，多租用戶應用程式無法僅透過將中繼資料中的簽發者值與權杖中的 `issuer` 值做比對來驗證權杖。 多租用戶應用程式需要一種邏輯，以根據簽發者值的租用戶識別碼部分，來決定哪些簽發者值有效、哪些簽發者值無效。 

例如，如果多租用戶應用程式只允許從已註冊服務的特定租用戶登入，它就必須檢查權杖中的簽發者值或 `tid` 宣告值，以確認該租用戶在其訂閱者清單中。 如果多租用戶應用程式只處理個人而不根據租用戶做出任何存取決策，則它可以完全忽略簽發者值。

為了讓任何 Azure AD 租用戶都能登入，我們在[多租用戶範例][AAD-Samples-MT]中已停用簽發者驗證。

## <a name="understand-user-and-admin-consent"></a>了解使用者和管理員同意

若要讓使用者登入 Azuer AD 中的應用程式，必須以使用者的租用戶代表該應用程式。 這可讓組織執行一些操作，例如在來自其租用戶的使用者登入應用程式時套用唯一原則。 就單一租用戶應用程式而言，這個註冊程序相當簡單；就是您在 [Azure 入口網站][AZURE-portal]中註冊應用程式時所進行的程序。

就多租用戶應用程式而言，應用程式的初始註冊程序則是在開發人員所使用的 Azure AD 租用戶中進行。 當來自不同租用戶的使用者第一次登入應用程式時，Azure AD 會要求他們同意應用程式所要求的權限。 如果他們同意，系統就會在使用者的租用戶中建立一個稱為「服務主體」** 的應用程式代表，然後登入便可繼續進行。 系統也會在記錄使用者對應用程式之同意意向的目錄中建立委派。 如需應用程式的「應用程式物件」和「服務主體物件」的詳細資料，請參閱[應用程式物件和服務主體物件][AAD-App-SP-Objects]。

![說明如何同意單一層應用程式][Consent-Single-Tier]

這個同意體驗會受到應用程式所要求的權限影響。 Microsoft 身分識別平臺支援兩種類型的許可權：僅限應用程式和委派。

* 委派的權限可讓應用程式能夠充當登入的使用者來執行該使用者所能執行的一部分操作。 例如，您可以授與應用程式委派的權限來讀取登入之使用者的行事曆。
* 僅限應用程式的權限會直接授與應用程式的識別身分。 例如，您可以將僅限應用程式的權限授與應用程式來讀取租用戶中的使用者清單，而且不論是誰登入此應用程式。

有些權限可以由一般使用者同意，有些則需要租用戶系統管理員的同意。 

### <a name="admin-consent"></a>系統管理員同意

僅限應用程式的權限一律需要租用戶系統管理員的同意。 如果您的應用程式要求僅限應用程式的權限，當使用者嘗試登入應用程式時，將會出現錯誤訊息，指出該使用者無法同意。

有些委派的權限也需要租用戶系統管理員的同意。 例如，若要能夠以登入的使用者身分寫回 Azure AD，就需要租用戶系統管理員的同意。 與僅限應用程式的權限一樣，如果一般使用者嘗試登入要求委派權限的應用程式，而該權限需要系統管理員同意，您的應用程式將會收到錯誤。 發佈資源的開發人員可以決定權限是否需要系統管理員的同意，而這項資訊也可在資源文件中找到。 [MICROSOFT GRAPH API][MSFT-Graph-permission-scopes]的許可權檔會指出哪些許可權需要系統管理員同意。

如果您的應用程式使用需要系統管理員同意的權限，您就必須要有相關的表示，例如可供系統管理員起始動作的按鈕或連結。 您的應用程式針對此動作傳送的要求是一個一般的 OAuth2/OpenID Connect 授權要求，其中也包含 `prompt=admin_consent` 查詢字串參數。 在系統管理員同意且系統已在客戶的租用戶中建立服務主體之後，後續的登入要求就不再需要 `prompt=admin_consent` 參數。 由於系統管理員已決定可接受要求的權限，因此從該時間點之後，就不會再提示租用戶中的任何其他使用者行使同意權。

租用戶系統管理員可以停用一般使用者對應用程式行使同意權的能力。 如果停用這項功能，就一律需要系統管理員同意，才能在租用戶中使用應用程式。 如果您想要在停用使用者同意的情況下測試應用程式，您可以在 [**企業應用程式**] 底下的 [**[使用者設定](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)**] 區段中的 [ [Azure 入口網站][AZURE-portal]中找到設定參數。

如果應用程式要求的權限不需要系統管理員同意，則應用程式也可以使用 `prompt=admin_consent` 參數。 這項作業的使用時機範例如下：如果應用程式需要租用戶系統管理員「註冊」一次，之後就不會再提示其他使用者表示同意的情況。

如果應用程式需要系統管理員同意，但系統管理員登入時未傳送 `prompt=admin_consent` 參數，則當系統管理員順利同意此應用程式時，**只會針對其使用者帳戶**套用該參數。 一般使用者將仍然無法登入此應用程式或對其行使同意權。 當您想要先讓租用戶系統管理員能夠瀏覽您的應用程式，然後才允許其他使用者存取時，這個功能相當有用。

> [!NOTE]
> 有些應用程式想要提供一種體驗，讓一般使用者能夠一開始即表示同意，之後應用程式即可讓系統管理員參與操作並要求需要系統管理員同意的權限。 在今天的 Azure AD 中，沒有任何方法可以完成 v1.0 應用程式註冊;不過，使用 Microsoft 身分識別平臺 (v2.0) 端點可讓應用程式在執行時間（而不是在註冊階段）要求許可權，這會啟用此案例。 如需詳細資訊，請參閱[Microsoft 身分識別平臺端點][AAD-V2-Dev-Guide]。

### <a name="consent-and-multi-tier-applications"></a>同意和多層應用程式

您的應用程式可能會有多層，每一層皆由它自己在 Azure AD 中的註冊代表。 例如，一個呼叫 Web API 的原生應用程式，或是一個呼叫 Web API 的 Web 應用程式。 在這兩種情況下，用戶端 (原生應用程式或 Web 應用程式) 都會要求可呼叫資源 (Web API) 的權限。 若要讓用戶端順利獲得客戶同意新增到其租用戶中，則它要求權限的所有資源必須都已經存在於客戶的租用戶中。 如果不符合此條件，Azuer AD 會傳回錯誤，指出必須先新增資源。

#### <a name="multiple-tiers-in-a-single-tenant"></a>單一租用戶中的多層

如果您的邏輯應用程式包含兩個或更多個應用程式註冊 (例如個別的用戶端和資源)，這可能會造成問題。 如何先將資源新增到客戶租用戶中？ Azure AD 涵蓋此情況，支援在單一步驟中同意用戶端和資源。 使用者在同意頁面上會看到用戶端和資源所要求的權限總和。 若要啟用這項行為，在資源的[應用程式資訊清單][AAD-App-Manifest]中，資源的應用程式註冊就必須以 `knownClientApplications` 的形式包含用戶端的「應用程式識別碼」。 例如：

```aad-app-manifest
    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

在本文最後的[相關內容](#related-content)一節中，會使用一個由多層原生用戶端呼叫 Web API 的範例來示範做法。 下圖為針對單一租用戶中註冊的多層應用程式表示同意的概觀。

![說明同意多層式已知的用戶端應用程式][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>多個租用戶中的多層

如果在不同的租用戶中註冊不同的應用程式層，將會發生類似的情況。 例如，想像建置一個會呼叫 Office 365 Exchange Online API 的原生用戶端應用程式的情況。 若要開發此原生應用程式，然後讓此原生應用程式在客戶的租用戶中執行，就必須要有 Exchange Online 服務主體。 在此情況下，開發人員和客戶必須購買 Exchange Online，如此才能在其租用戶中建立服務主體。

如果它是由 Microsoft 以外的組織所建立的 API，則 API 的開發人員必須提供一種方法，讓其客戶同意應用程式進入其客戶的租使用者。 建議的設計是讓協力廠商開發人員建立 API，使其也可以做為 web 用戶端來執行註冊。 若要這樣做：

1. 遵循先前的章節，確保 API 實作多租用戶應用程式註冊/程式碼的需求。
2. 除了公開 API 的範圍/角色，請確定註冊包含預設) 提供的「登入和讀取使用者設定檔」許可權 (。
3. 在 Web 用戶端實作登入/註冊頁面，並依照[管理員同意](#admin-consent)指引操作。
4. 當使用者同意應用程式後，就會在其租用戶中建立服務主體和同意委派連結，而原生應用程式可以取得 API 的權杖。

下圖為針對不同租用戶中註冊的多層應用程式表示同意的概觀。

![說明如何同意多層式多方應用程式][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>撤銷同意

使用者和系統管理員可以隨時撤銷對您應用程式的同意：

* 使用者可藉由將個別應用程式從其[存取面板應用程式][AAD-Access-Panel]清單中移除，來撤銷對這些應用程式的存取權。
* 系統管理員可以藉由使用[Azure 入口網站][AZURE-portal]的 [[企業應用程式](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)] 區段來移除應用程式的存取權。

如果是由系統管理員代表租用戶中的所有使用者對應用程式行使同意權，使用者就不能個別撤銷存取權。 只有系統管理員才能撤銷存取權，並且只能針對整個應用程式撤銷。

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>多租用戶應用程式和快取存取權杖

多租用戶應用程式也可以取得存取權杖來呼叫受 Azure AD 保護的 API。 大多數人在使用多租用戶應用程式和 Active Directory Authentication Library (ADAL) 時容易犯的一個錯誤是：一開始即使用 /common 為使用者要求權杖、接收回應，然後也使用 /common 來為該相同使用者要求後續的權杖。 由於從 Azure AD 傳回的回應是來自租用戶而非 /common，因此 ADAL 在快取權杖時會將它視為來自租用戶。 後續為了為使用者取得存取權杖而進行的 /common 呼叫會遺漏快取項目，因此系統會再次提示使用者登入。 為了避免遺漏快取，請確定後續為已登入之使用者進行的呼叫是對租用戶的端點發出。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何建置可讓使用者從任何 Azure AD 租用戶登入的應用程式。 在啟用應用程式與 Azure AD 之間的單一登入 (SSO) 功能之後，您也可以更新應用程式以存取 Microsoft 資源 (例如 Office 365) 所公開的 API。 這樣一來，您即可在應用程式中提供個人化的體驗；例如，向使用者顯示其設定檔圖片或下一個行事曆約會等內容資訊。 若要深入了解如何對 Azure AD 和 Office 365 服務 (例如 Exchange、SharePoint、OneDrive、OneNote 等) 進行 API 呼叫，請瀏覽 [Microsoft Graph API][MSFT-Graph-overview]。

## <a name="related-content"></a>相關內容

* [多租使用者應用程式範例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [應用程式的商標指導方針][AAD-App-Branding]
* [應用程式物件和服務主體物件][AAD-App-SP-Objects]
* [整合應用程式與 Azure Active Directory][AAD-Integrating-Apps]
* [同意架構的總覽][AAD-Consent-Overview]
* [Microsoft Graph API 權限範圍][MSFT-Graph-permission-scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: /previous-versions/azure/dn645542(v=azure.100)
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken