---
title: Microsoft 身分識別平臺範圍、許可權 & 同意
description: 深入瞭解 Microsoft 身分識別平臺端點中的授權，包括範圍、許可權和同意。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: aa8c00d1ee2a0dc3d019cc75b4e411ede984e74a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756053"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Microsoft 身分識別平台中的權限和同意

與 Microsoft 身分識別平臺整合的應用程式會遵循授權模型，讓使用者和系統管理員控制資料的存取方式。 已在 Microsoft 身分識別平臺上更新授權模型的實作為。 它會變更應用程式必須與 Microsoft 身分識別平臺互動的方式。 本文涵蓋此授權模型的基本概念，包括範圍、權限及同意。

## <a name="scopes-and-permissions"></a>範圍和權限

Microsoft 身分識別平台會實作 [OAuth 2.0](active-directory-v2-protocols.md) 授權通訊協定。 OAuth 2.0 是一種可讓協力廠商應用程式代表使用者存取 Web 主控資源的方法。 任何與 Microsoft 身分識別平臺整合的 web 主控資源都具有資源識別碼或 *應用程式識別碼 URI*。 

以下是 Microsoft web 託管資源的一些範例：

* Microsoft Graph：`https://graph.microsoft.com`
* Microsoft 365 Mail API： `https://outlook.office.com`
* Azure Key Vault： `https://vault.azure.net`

這也適用於已與 Microsoft 身分識別平台整合的第三方資源。 任何這些資源也都可以定義一組權限，可用來進一步細分該資源的功能。 例如，[Microsoft Graph](https://graph.microsoft.com) 除了別的之外，還定義了權限來執行下列工作：

* 讀取使用者的行事曆
* 寫入使用者的行事曆
* 以使用者身分傳送郵件

因為這些類型的許可權定義，資源對於其資料和 API 功能的公開程度有細微的控制。 第三方應用程式可向使用者和系統管理員要求這些權限，且必須在他們核准要求後，應用程式才可存取資料或代表使用者執行動作。 

當資源的功能被分割成小型許可權集時，可以建立協力廠商應用程式，只要求其執行其功能所需的許可權。 使用者和系統管理員可以知道應用程式可以存取哪些資料。 而且可以更確信應用程式不會有惡意意圖。 開發人員應一律遵守最小許可權的原則，只要求其應用程式運作所需的許可權。

在 OAuth 2.0 中，這些類型的許可權集合稱為「範圍」（ *scope*）。 它們也經常稱為 *許可權*。 在 Microsoft 身分識別平臺中，許可權會以字串值表示。 針對 Microsoft Graph 範例，以下是每個許可權的字串值：

* 使用 `Calendars.Read` 來讀取使用者的行事曆
* 使用 `Calendars.ReadWrite` 來寫入使用者的行事曆
* 使用 `Mail.Send` 來以使用者身分傳送郵件

應用程式最常要求這些許可權的方法，是在 Microsoft 身分識別平臺授權端點的要求中指定範圍。 不過，某些高許可權許可權只能透過系統管理員同意來授與。 您可以使用 [系統管理員同意端點](#admin-restricted-permissions)來要求或授與它們。 繼續閱讀以深入瞭解。

## <a name="permission-types"></a>權限類型

Microsoft 身分識別平臺支援兩種類型的許可權： *委派的許可權* 和 *應用程式許可權*。

* **委派權限** 供已有登入使用者的應用程式使用。 針對這些應用程式，使用者或系統管理員會同意應用程式所要求的許可權。 應用程式會委派許可權，以在呼叫目標資源時作為已登入的使用者。 

    某些委派的許可權可由非管理員同意。 但某些高許可權許可權需要 [系統管理員的同意](#admin-restricted-permissions)。 若要瞭解哪些系統管理員角色可以同意委派的許可權，請參閱 [Azure Active Directory (Azure AD) 中的系統管理員角色許可權 ](../roles/permissions-reference.md)。

* **應用程式許可權** 是在沒有登入使用者的情況下執行的應用程式所使用，例如，以背景服務或守護程式執行的應用程式。 只有 [系統管理員可以同意](#requesting-consent-for-an-entire-tenant) 應用程式許可權。

_有效許可權_ 是您的應用程式對目標資源提出要求時，所擁有的許可權。 請務必瞭解委派的許可權和應用程式授與應用程式許可權之間的差異，以及您的應用程式在呼叫目標資源時授與的有效許可權。

- 針對委派的許可權，應用程式的 _有效許可權_ 是由同意) 和目前登入使用者的許可權授與的委派許可權的最低許可權交集 (。 應用程式絕對不會擁有已登入使用者權限以外的權限。 

   在組織內，已登入使用者的許可權可以由原則或一或多個系統管理員角色中的成員資格來決定。 若要了解哪些系統管理員角色可同意委派權限，請參閱 [Azure AD 中的系統管理員角色權限](../roles/permissions-reference.md)。

   例如，假設已經對您的應用程式授與 _User.ReadWrite.All_ 委派權限。 此權限名義上會對應用程式授與讀取及更新組織中每個使用者設定檔的權限。 如果已登入的使用者是全域管理員，則您的應用程式可以更新組織中每位使用者的設定檔。 不過，如果登入的使用者沒有系統管理員角色，則您的應用程式只能更新已登入使用者的設定檔。 它無法更新組織中其他使用者的設定檔，因為該使用者有權代表其採取動作的使用者沒有這些許可權。

- 針對應用程式許可權，應用程式的 _有效許可權_ 是許可權所隱含的完整層級許可權。 例如，具有 _User.ReadWrite.All_ 應用程式權限的應用程式可以更新組織中每個使用者的設定檔。

## <a name="openid-connect-scopes"></a>OpenId Connect 範圍

OpenID Connect 的 Microsoft 身分識別平臺會有一些定義完善的範圍，這些範圍也裝載于 Microsoft Graph： `openid` 、 `email` 、 `profile` 和 `offline_access` 。 `address` `phone` 不支援和 OpenID Connect 範圍。

如果您要求 OpenID Connect 範圍和權杖，您將會取得權杖來呼叫 [使用者資訊端點](userinfo.md)。

### <a name="openid"></a>openid

如果應用程式使用 [OpenID Connect](active-directory-v2-protocols.md)登入，則必須要求 `openid` 範圍。 `openid`範圍會在工作帳戶同意頁面上顯示為 [登 **入**] 許可權。 在 [個人 Microsoft 帳戶同意] 頁面上，它會顯示為 [ **查看您的設定檔]，並使用您的 Microsoft 帳戶許可權連接至應用程式和服務** 。 

藉由使用此許可權，應用程式可以使用宣告的形式接收使用者的唯一識別碼 `sub` 。 許可權也可讓應用程式存取使用者資訊端點。 `openid`範圍可用於 Microsoft 身分識別平臺權杖端點，以取得識別碼權杖。 應用程式可以使用這些權杖進行驗證。

### <a name="email"></a>電子郵件

`email`範圍可以搭配 `openid` 範圍和任何其他範圍使用。 它會以 `email` 宣告的形式為應用程式提供使用者主要電子郵件地址的存取權。 

`email`只有當電子郵件地址與使用者帳戶相關聯時，才會將宣告包含在權杖中，這種情況不一定是如此。 如果您的應用程式使用 `email` 範圍，應用程式必須能夠處理權杖中沒有宣告的情況 `email` 。

### <a name="profile"></a>profile

`profile`範圍可以搭配 `openid` 範圍和任何其他範圍使用。 它可讓應用程式存取大量的使用者相關資訊。 它可以存取的資訊包括但不限於使用者的名字、姓氏、慣用的使用者名稱，以及物件識別碼。 

如需 `profile` 特定使用者之參數中可用宣告的完整清單 `id_tokens` ，請參閱[ `id_tokens` 參考](id-tokens.md)。

### <a name="offline_access"></a>offline_access

[ `offline_access` 範圍](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)可讓您的應用程式代表使用者存取資源，延長一段時間。 在 [同意] 頁面上，此範圍會顯示為 [ **維護存取權給您的資料存取** 權] 許可權。 

當使用者核准範圍時 `offline_access` ，您的應用程式可以接收來自 Microsoft 身分識別平臺權杖端點的重新整理權杖。 重新整理權杖是長期權杖。 您的應用程式可以在舊存取權杖到期時取得新的存取權杖。

> [!NOTE]
> 此許可權目前顯示在所有同意頁面上，即使是未提供重新整理權杖的流程 (例如 [隱含流程](v2-oauth2-implicit-grant-flow.md)) 。 這項設定解決了用戶端可以在隱含流程內開始，然後移至預期重新整理權杖的程式碼流程的案例。

在 Microsoft 身分識別平臺上 (對 v2.0 端點提出的要求) ，您的應用程式必須明確要求 `offline_access` 範圍，才能接收重新整理權杖。 因此，當您在 [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md)中兌換授權碼時，您只會收到來自端點的存取權杖 `/token` 。 

存取權杖的有效期短。 它通常會在一小時內到期。 屆時，您的應用程式將必須把使用者重新導向回 `/authorize` 端點，以擷取新的授權碼。 在此重新導向期間，視應用程式的類型而定，使用者可能需要重新輸入其認證或重新同意權限。

如需如何取得及使用重新整理權杖的詳細資訊，請參閱 [Microsoft 身分識別平臺通訊協定參考](active-directory-v2-protocols.md)。

## <a name="requesting-individual-user-consent"></a>要求個別使用者同意

在 [OpenID Connect 或 OAuth 2.0](active-directory-v2-protocols.md) 授權要求中，應用程式可以使用 `scope` 查詢參數來要求它所需的權限。 例如，當使用者登入應用程式時，應用程式會傳送如下範例所示的要求。  (換行，以提供可讀性。 ) 

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 參數是應用程式所要求的委派權限清單 (以空格分隔)。 藉由將許可權值附加至資源的識別碼， (應用程式識別碼 URI) 來指出每個許可權。 在要求範例中，應用程式需要權限來讀取使用者的行事曆，以及以使用者身分傳送郵件。

在使用者輸入其認證之後，Microsoft 身分識別平臺會檢查是否有相符的 *使用者同意* 記錄。 如果使用者過去尚未同意任何要求的許可權，而且系統管理員未代表整個組織同意這些許可權，Microsoft 身分識別平臺會要求使用者授與所要求的許可權。

目前， `offline_access` ( 「維持存取權給您存取您的資料」 ) 許可權，並 ( 「將 `user.read` 您登入並讀取您的設定檔」 ) 許可權會自動包含在應用程式的初始同意中。  這些許可權通常是適當的應用程式功能所需的許可權。 此 `offline_access` 許可權可讓應用程式存取對原生應用程式和 web 應用程式而言非常重要的權杖。 `user.read`許可權可提供宣告的存取權 `sub` 。 它可讓用戶端或應用程式在一段時間內正確地識別使用者，並存取基本的使用者資訊。

![顯示工作帳戶同意的範例螢幕擷取畫面。](./media/v2-permissions-and-consent/work_account_consent.png)

當使用者核准許可權要求時，就會記錄同意。 使用者稍後登入應用程式時，不需要再次同意。

## <a name="requesting-consent-for-an-entire-tenant"></a>要求整個租用戶的同意

當組織購買應用程式的授權或訂用帳戶時，組織通常會想要主動設定應用程式，以供組織的所有成員使用。 在此過程中，系統管理員可以授與同意，讓應用程式代表租用戶中的任何使用者執行動作。 如果系統管理員為整個租使用者授與同意，則組織的使用者看不到該應用程式的同意頁面。

若要為租用戶中的所有使用者要求委派權限的同意，您的應用程式可以使用系統管理員同意端點。

此外，應用程式必須使用系統管理員同意端點來要求應用程式許可權。

## <a name="admin-restricted-permissions"></a>受管理員限制的權限

Microsoft 資源中的某些高許可權許可權可以設定為系統 *管理員受限*。 以下是這類許可權的一些範例：

* 使用 `User.Read.All` 讀取所有使用者的完整設定檔
* 使用 `Directory.ReadWrite.All` 將資料寫入組織的目錄
* 使用 `Groups.Read.All` 讀取組織目錄中的所有群組

雖然取用者使用者可能會授與應用程式存取這類資料，但組織使用者無法將存取權授與同一組機密公司資料。 如果您的應用程式向組織使用者要求這其中一個權限的存取權，使用者將會收到錯誤訊息，指出他們未獲授權而無法對您應用程式的權限表示同意。

如果您的應用程式需要管理受限許可權的範圍，則組織的系統管理員必須代表組織的使用者同意這些範圍。 若要避免對要求同意但無法授與許可權的使用者顯示提示，您的應用程式可以使用系統管理員同意端點。 下一節會涵蓋系統管理員同意端點。

如果應用程式要求高許可權委派許可權，且系統管理員透過管理員同意端點授與這些許可權，則會為租使用者中的所有使用者授與同意。

如果應用程式要求應用程式許可權，且系統管理員透過管理員同意端點授與這些許可權，則不會代表任何特定使用者來執行此授與。 此時會 *直接* 為用戶端應用程式授與權限。 這些類型的許可權只會由 daemon 服務和在背景中執行的其他非互動式應用程式使用。

## <a name="using-the-admin-consent-endpoint"></a>使用系統管理員同意端點

使用系統管理員同意端點來授與系統管理員同意之後，您就完成了。 使用者不需要採取任何進一步的動作。 授與系統管理員同意之後，使用者可以透過一般驗證流程取得存取權杖。 產生的存取權杖具有已同意的許可權。

當公司系統管理員使用您的應用程式，並將其導向至授權端點時，Microsoft 身分識別平臺會偵測使用者的角色。 它會詢問公司系統管理員是否想要代表整個租使用者同意您所要求的許可權。 您可以改為使用專用的系統管理員同意端點，主動要求系統管理員代表整個租使用者授與許可權。 要求應用程式許可權也需要此端點。 無法使用授權端點來要求應用程式許可權。

如果您依照這些步驟，您的應用程式便能為租用戶中所有的使用者要求權限，包括受管理員限制的範圍。 這項作業是高許可權。 只有在您的案例需要時，才使用此作業。

若要查看可執行這些步驟的程式碼範例，請參閱 GitHub 中的系統 [管理限制範圍範例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) 。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>在應用程式註冊入口網站中要求權限

在應用程式註冊入口網站中，應用程式可以列出所需的許可權，包括委派的許可權和應用程式許可權。 這項設定可讓您使用 `/.default` 範圍和 Azure 入口網站的 **授與管理員同意** 選項。  

一般情況下，應該針對指定的應用程式以靜態方式定義許可權。 它們應該是應用程式會以動態或增量方式要求的許可權超集合。

> [!NOTE]
>只能透過使用來要求應用程式許可權 [`/.default`](#the-default-scope) 。 因此，如果您的應用程式需要應用程式許可權，請確定它們列在應用程式註冊入口網站中。

若要為應用程式設定以靜態方式要求的權限清單：

1. 移至<a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure 入口網站應用程式註冊 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>快速入門體驗中的應用程式。
1. 選取應用程式，或 [建立](quickstart-register-app.md) 應用程式（如果尚未建立）。
1. 在應用程式的 **[總覽**] 頁面的 [**管理**] 底下，選取 [ **API 許可權**  >  **新增許可權**]。
1. 從可用的 Api 清單中選取 **Microsoft Graph** 。 然後新增您應用程式所需的許可權。
1. 選取 [ **新增許可權**]。

### <a name="recommended-sign-the-user-in-to-your-app"></a>建議︰將使用者登入您的應用程式

通常，當您建置使用系統管理員同意端點的應用程式時，應用程式會需要一個可供系統管理員核准應用程式權限的頁面或檢視。 此頁面可以是：

* 應用程式註冊流程的一部分。
* 應用程式設定的一部分。
* 專用的「連接」流程。 

在許多情況下，只有在使用者使用工作 Microsoft 帳戶或學校 Microsoft 帳戶登入之後，應用程式才會顯示此「連線」視圖是有意義的。

當您將使用者登入應用程式時，您可以先識別系統管理員所屬的組織，然後再要求他們核准必要的許可權。 雖然此步驟並非絕對必要，但可協助您為組織使用者建立更直覺的體驗。 

若要將使用者登入，請遵循 [Microsoft 身分識別平臺通訊協定教學](active-directory-v2-protocols.md)課程。

### <a name="request-the-permissions-from-a-directory-admin"></a>向目錄管理員要求權限

當您準備好向組織的系統管理員要求許可權時，您可以將使用者重新導向至 Microsoft 身分識別平臺系統管理員同意端點。

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| 參數        | 條件        | 描述                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | 必要 | 您想要要求權限的目錄租用戶。 您可以使用 GUID 或易記名稱格式來提供。 也可以一般地與組織參考，如範例中所示。 請勿使用「一般」，因為個人帳戶無法在租使用者的內容中提供系統管理員同意。 若要確保與管理租使用者的個人帳戶具有最佳相容性，請盡可能使用租使用者識別碼。 |
| `client_id` | 必要 | 應用程式 (用戶端) 識別碼， [Azure 入口網站–應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) 體驗指派給您的應用程式。 |
| `redirect_uri` | 必要 |您想要傳送回應以供應用程式處理的重新導向 URI。 它必須與您在應用程式註冊入口網站中註冊的其中一個重新導向 URI 完全相符。 |
| `state` | 建議 | 包含在要求中的值，也會在權杖回應中傳回。 它可以是您想要的任何內容的字串。 請在驗證要求出現之前，先使用此狀態在應用程式中將使用者狀態的相關資訊 (例如他們之前所在的網頁或檢視) 編碼。 |
|`scope`        | 必要        | 定義應用程式所要求的許可權集合。 範圍可以是靜態 (使用 [`/.default`](#the-default-scope)) 或動態。  此集合可以包含 (`openid` 、) OpenID Connect 範圍 `profile` `email` 。 如果您需要應用程式許可權，您必須使用 `/.default` 來要求靜態設定的許可權清單。  |


此時，Azure AD 會要求租用戶系統管理員登入來完成要求。 系統會要求系統管理員核准您在參數中要求的擁有權限 `scope` 。  如果您使用靜態 (`/.default`) 值，它的運作方式就像 v1.0 系統管理員同意端點，而且會要求應用程式所需許可權中的所有範圍要求同意。

#### <a name="successful-response"></a>成功的回應

如果系統管理員為您的應用程式核准權限，則成功的回應看起來會像這樣︰

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 參數 | 描述 |
| --- | --- |
| `tenant` | 將應用程式所要求的權限授與應用程式的目錄租用戶 (採用 GUID 格式)。 |
| `state` | 一個包含在要求中而將一併在權杖回應中傳回的值。 它可以是您想要的任何內容的字串。 此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| `admin_consent` | 將設定為 `True`。 |

#### <a name="error-response"></a>錯誤回應

如果系統管理員未核准您應用程式的許可權，失敗的回應看起來會像這樣：

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 描述 |
| --- | --- |
| `error` | 可用於將發生的錯誤分類的錯誤碼字串。 它也可以用來回應錯誤。 |
| `error_description` | 可協助開發人員識別錯誤根本原因的特定錯誤訊息。 |

從系統管理員同意端點收到成功回應之後，您的應用程式便已取得它所要求的權限。 接著，您可以針對您想要的資源要求權杖。

## <a name="using-permissions"></a>使用權限

當使用者同意您應用程式的許可權之後，您的應用程式就可以取得存取權杖，以代表應用程式存取某些容量資源的許可權。 存取權杖只能用於單一資源。 但在存取權杖內編碼的是您的應用程式為該資源授與的每個許可權。 若要取得存取權杖，您的應用程式可以向 Microsoft 身分識別平臺權杖端點提出要求，如下所示：

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

您可以在對資源提出的 HTTP 要求中使用產生的存取權杖。 它會可靠地向資源指出您的應用程式有適當的許可權可以進行特定工作。

如需 OAuth 2.0 通訊協定以及如何取得存取權杖的詳細資訊，請參閱 [Microsoft 身分識別平臺端點通訊協定參考](active-directory-v2-protocols.md)。

## <a name="the-default-scope"></a>/.default 範圍

您可以使用此 `/.default` 範圍來協助將您的應用程式從 v1.0 端點遷移至 Microsoft 身分識別平臺端點。 `/.default`針對應用程式註冊上設定之許可權的靜態清單，每個應用程式都內建範圍。 

的 `scope` 值與 v1.0 `https://graph.microsoft.com/.default` 端點上的功能相同 `resource=https://graph.microsoft.com` 。 藉由在 `https://graph.microsoft.com/.default` 要求中指定範圍，您的應用程式會要求存取權杖，其中包含您在應用程式註冊入口網站中為應用程式選取的每個 Microsoft Graph 許可權的範圍。 範圍是使用資源 URI 和來建立 `/.default` 。 因此，如果資源 URI 為 `https://contosoApp.com` ，則要求的範圍為 `https://contosoApp.com/.default` 。  如果您必須包含第二個斜線才能正確地要求權杖，請參閱 [尾端斜線的章節](#trailing-slash-and-default)。

`/.default`範圍可用於任何 OAuth 2.0 流程。 但在代理者 [流程](v2-oauth2-on-behalf-of-flow.md) 和 [用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)中是必要的。 當您使用 v2 系統管理員同意端點來要求應用程式許可權時，也需要它。

用戶端無法 `/.default` 在單一要求中結合靜態 () 同意和動態同意。 因此 `scope=https://graph.microsoft.com/.default+mail.read` 會產生錯誤，因為它會結合範圍類型。

### <a name="default-and-consent"></a>/.default 與同意

`/.default` 範圍也會對 `prompt=consent` 觸發 v1.0 端點行為。 無論資源為何，它都會要求應用程式註冊的擁有權限的同意。 如果包含在要求中，範圍會傳回 `/.default` 權杖，其中包含所要求資源的範圍。

### <a name="default-when-the-user-has-already-given-consent"></a>使用者已經同意時的 /.default

`/.default`範圍在功能上與以 `resource` 中心1.0 端點的行為相同。 它也會攜帶 v1.0 端點的同意行為。 也就是說， `/.default` 只有在使用者未在用戶端與資源之間授與許可權時，才會觸發同意提示。 

如果有任何這類同意存在，則傳回的權杖會包含使用者為該資源授與的所有範圍。 但是，如果未授與任何許可權，或已 `prompt=consent` 提供該參數，則會針對用戶端應用程式註冊的所有範圍顯示同意提示。

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>範例1：使用者（或租使用者管理員）已授與許可權

在此範例中，使用者或租使用者系統管理員已將 `mail.read` 和 `user.read` Microsoft Graph 許可權授與用戶端。 

如果用戶端要求 `scope=https://graph.microsoft.com/.default` ，不論用戶端應用程式的 Microsoft Graph 的註冊許可權內容為何，都不會顯示任何同意提示。 傳回的權杖包含範圍 `mail.read` 和 `user.read` 。

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>範例2：使用者未在用戶端與資源之間授與許可權

在此範例中，使用者尚未在用戶端與 Microsoft Graph 之間授與同意。 用戶端已註冊許可權 `user.read` 和 `contacts.read` 。 它也註冊了 Azure Key Vault 範圍 `https://vault.azure.net/user_impersonation` 。 

當用戶端要求的權杖時， `scope=https://graph.microsoft.com/.default` 使用者會看到 `user.read` 範圍、 `contacts.read` 範圍和 Key Vault 範圍的同意頁面 `user_impersonation` 。 傳回的權杖只包含 `user.read` 和 `contacts.read` 範圍。 它只能用於 Microsoft Graph。

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>範例3：使用者已同意，且用戶端要求更多範圍

在此範例中，使用者已同意 `mail.read` 用戶端。 用戶端已註冊 `contacts.read` 範圍。 

當用戶端使用要求權杖 `scope=https://graph.microsoft.com/.default` ，並要求同意時 `prompt=consent` ，使用者會看到所有 (的同意頁面，而且只會) 應用程式註冊的許可權。 `contacts.read`範圍是在同意頁面上，但 `mail.read` 不是。 傳回的 token 適用于 Microsoft Graph。 它包含 `mail.read` 和 `contacts.read` 。

### <a name="using-the-default-scope-with-the-client"></a>使用 /.default 範圍搭配用戶端

在某些情況下，用戶端可以要求自己的 `/.default` 範圍。 下列範例示範此案例。

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

此程式碼範例會針對所有已註冊的許可權產生同意頁面（如果上述同意描述並套用 `/.default` 至案例）。 然後，程式碼會傳回 `id_token` ，而不是存取權杖。  

此行為可容納一些舊版用戶端，這些用戶端會從 Azure AD 驗證程式庫 (ADAL) 移至 Microsoft 驗證程式庫 (MSAL) 。 以 Microsoft 身分識別平臺為目標的新用戶端不 *應* 使用此設定。

### <a name="client-credentials-grant-flow-and-default"></a>用戶端認證授與流程和/.default  

的另一個用途 `/.default` 是要求應用程式許可權 (或在非互動式應用程式中) *角色* ，例如使用 [用戶端認證](v2-oauth2-client-creds-grant-flow.md) 授與流程來呼叫 web API 的 daemon 應用程式。

若要建立 web API)  (角色的應用程式許可權，請參閱 [在您的應用程式中新增](howto-add-app-roles-in-azure-ad-apps.md)應用程式角色。

用戶端應用程式中的用戶端認證要求 *必須* 包含 `scope={resource}/.default` 。 這裡 `{resource}` 是您的應用程式想要呼叫的 WEB API。 *不* 支援使用個別應用程式許可權 (角色) 發出用戶端認證要求。 已授與該 web API 的所有應用程式許可權 (角色) 都會包含在傳回的存取權杖中。

若要授與您所定義之應用程式許可權的存取權，包括授與應用程式的系統管理員同意，請參閱 [設定用戶端應用程式以存取 WEB API](quickstart-configure-app-access-web-apis.md)。

### <a name="trailing-slash-and-default"></a>尾端斜線和/.default

某些資源 Uri 的尾端斜線，例如，而不是 `https://contoso.com/` `https://contoso.com` 。 尾端的斜線可能會導致權杖驗證發生問題。 Azure Resource Manager () 要求權杖時，主要是發生問題 `https://management.azure.com/` 。 在此情況下，資源 URI 上的尾端斜線表示要求權杖時必須有斜線。  因此，當您要求 `https://management.azure.com/` 和使用權杖時 `/.default` ，您必須要求 `https://management.azure.com//.default` (注意到雙斜線！ ) 。 一般情況下，如果您確認權杖正在發行，而且如果 API 已拒絕該權杖接受該權杖，請考慮新增第二個正斜線，然後再試一次。 

## <a name="troubleshooting-permissions-and-consent"></a>針對權限和同意進行疑難排解

如需疑難排解步驟，請 [在對應用程式執行同意時](../manage-apps/application-sign-in-unexpected-user-consent-error.md)看到未預期的錯誤。

## <a name="next-steps"></a>後續步驟

* [Microsoft 身分識別平臺中的識別碼權杖](id-tokens.md)
* [存取 Microsoft 身分識別平臺中的權杖](access-tokens.md)
