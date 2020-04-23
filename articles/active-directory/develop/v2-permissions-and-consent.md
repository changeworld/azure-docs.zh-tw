---
title: Microsoft 識別平台範圍、許可權和同意
description: Microsoft 標識平台終結點中授權的描述,包括作用域、許可權和同意。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: 5495aa6fda189897985ed2f198f6e92c996f6fef
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868372"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft 身分識別平台端點中的權限和同意

與 Microsoft 身分識別平台整合的應用程式，會遵循可讓使用者和系統管理員控制資料存取方式的授權模型。 授權模型的實現已在 Microsoft 標識平台終結點上更新,並改變了應用必須與 Microsoft 標識平臺互動的方式。 本文涵蓋此授權模型的基本概念，包括範圍、權限及同意。

## <a name="scopes-and-permissions"></a>範圍和權限

Microsoft 身分識別平台會實作 [OAuth 2.0](active-directory-v2-protocols.md) 授權通訊協定。 OAuth 2.0 是一種可讓協力廠商應用程式代表使用者存取 Web 主控資源的方法。 任何與 Microsoft 身分識別平台整合的 Web 主控資源都具有資源識別碼 (或稱為「應用程式識別碼 URI」**)。 例如，Microsoft 的 Web 主控資源包括：

* Microsoft Graph：`https://graph.microsoft.com`
* Office 365 郵件 API：`https://outlook.office.com`
* Azure 金鑰保存庫:`https://vault.azure.net`

> [!NOTE]
> 我們強烈建議您使用 Microsoft 圖形而不是 Office 365 郵件 API 等。

這也適用於已與 Microsoft 身分識別平台整合的第三方資源。 任何這些資源也都可以定義一組權限，可用來進一步細分該資源的功能。 例如，[Microsoft Graph](https://graph.microsoft.com) 除了別的之外，還定義了權限來執行下列工作：

* 讀取使用者的行事曆
* 寫入使用者的行事曆
* 以使用者身分傳送郵件

藉由定義這些類型的權限，資源可以更精細地掌控其資料及 API 功能的公開方式。 第三方應用程式可向使用者和系統管理員要求這些權限，且必須在他們核准要求後，應用程式才可存取資料或代表使用者執行動作。 透過將資源的功能切割成較小的權限集，便可將協力廠商應用程式建置成只要求它們執行其功能所需的特定權限。 使用者和管理員可以確切知道應用有權訪問哪些數據,並且他們可以更加確信它不是惡意行為。 開發人員應一律遵守最低權限的概念，而僅就其應用程式運作所需的程度要求權限。

在 OAuth 2.0 中，這些類型的權限也稱為「範圍」**。 它們通常也稱為*權限*。 權限在 Microsoft 身分識別平台中會以字串值表示。 繼續討論 Microsoft Graph 範例，每個權限的字串值如下：

* 使用 `Calendars.Read` 來讀取使用者的行事曆
* 使用 `Calendars.ReadWrite` 來寫入使用者的行事曆
* 使用 `Mail.Send` 來以使用者身分傳送郵件

應用最常通過指定對 Microsoft 標識平台的請求中的範圍來請求這些許可權,從而授權終結點。 但是,某些高許可權許可權只能通過管理員同意授予,並使用[管理員同意終結點](v2-permissions-and-consent.md#admin-restricted-permissions)請求/授予。 繼續閱讀以深入了解。

## <a name="permission-types"></a>權限類型

Microsoft 身分識別平台支援兩種類型的權限：**委派權限**和**應用程式權限**。

* **委派權限**供已有登入使用者的應用程式使用。 對於這些應用,使用者或管理員同意應用請求的許可權,並且應用在調用目標資源時被委派為登錄用戶的許可權。 有些委派權限可由非系統管理使用者同意，但有些較高的特定權限則需要[系統管理員的同意](v2-permissions-and-consent.md#admin-restricted-permissions)。 若要了解哪些系統管理員角色可同意委派權限，請參閱 [Azure AD 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)。

* **應用程式權限**供沒有登入使用者的應用程式在執行時使用；例如，當作背景服務或精靈來執行的應用程式。  應用程式權限只能[由系統管理員同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

_有效權限_ 是應用程式向目標資源提出要求時所將具備的權限。 請務必瞭解授予應用的委派許可權和應用程序許可權之間的區別,以及對目標資源進行調用時其有效許可權之間的區別。

- 就委派權限來說，應用程式的 _有效權限_ 是應用程式 (透過同意) 所獲得授與的委派權限和目前已登入使用者的權限二者的最小權限交集。 應用程式絕對不會擁有已登入使用者權限以外的權限。 在組織內，已登入使用者的權限會由原則或是一或多個系統管理員角色的成員資格決定。 若要了解哪些系統管理員角色可同意委派權限，請參閱 [Azure AD 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)。

   例如，假設已經對您的應用程式授與 _User.ReadWrite.All_ 委派權限。 此權限名義上會對應用程式授與讀取及更新組織中每個使用者設定檔的權限。 如果已登入使用者是全域管理員，應用程式便能夠更新組織中每個使用者的設定檔。 但是,如果登錄使用者不在管理員角色中,則你的應用將只能更新登錄使用者的配置檔。 應用程式有權代表其行事的使用者沒有這些權限，因此應用程式無法更新組織中其他使用者的設定檔。

- 就應用程式權限來說，應用程式的 _有效權限_ 將是權限所隱含的完整層級權限。 例如，具有 _User.ReadWrite.All_ 應用程式權限的應用程式可以更新組織中每個使用者的設定檔。

## <a name="openid-connect-scopes"></a>OpenId Connect 範圍

OpenID Connect 的 Microsoft 識別平台實現具有一些定義良好的作用域,這些作用域不`openid`適用於特定`profile``offline_access``email`資源: 、、 和 。 不支援 `address` 和 `phone` OpenID Connect 範圍。

### <a name="openid"></a>openid

如果應用程式使用 [OpenID Connect](active-directory-v2-protocols.md) 來執行登入，它就必須要求 `openid` 範圍。 `openid` 範圍會在工作帳戶同意頁面上顯示為「將您登入」權限，而在個人 Microsoft 帳戶同意頁面上會顯示為「檢視您的設定檔並使用您的 Microsoft 帳戶連接到應用程式和服務」權限。 有了此權限之後，應用程式便能夠以 `sub` 宣告的形式接收使用者的唯一識別碼。 它也會為應用程式提供 UserInfo 端點的存取權。 該`openid`作用域可以在 Microsoft 標識平台權杖終結點上用於獲取 ID 權杖,該權杖可供應用用於身份驗證。

### <a name="email"></a>電子郵件

`email` 範圍可以與 `openid` 範圍及任何其他範圍搭配使用。 它會以 `email` 宣告的形式為應用程式提供使用者主要電子郵件地址的存取權。 僅當`email`電子郵件地址與使用者帳戶關聯時,才會將聲明包含在令牌中,這種情況並非總是如此。 如果它使用 `email` 範圍，您的應用程式就應該做好準備，以處理權杖中沒有 `email` 宣告的情況。

### <a name="profile"></a>設定檔

`profile` 範圍可以與 `openid` 範圍及任何其他範圍搭配使用。 它會為應用程式提供大量使用者相關資訊的存取權。 它可以造訪的資訊包括但不限於使用者的給定名稱、姓氏、首選使用者名和對象 ID。 有關特定使用者id_tokens參數中可用的設定檔聲明的完整清單,[`id_tokens`請參考參考](id-tokens.md)。

### <a name="offline_access"></a>offline_access

該[`offline_access`範圍](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)允許應用在較長時間內代表使用者訪問資源。 在同意頁面上，此範圍會顯示為「維持存取您可存取的資料」權限。 當使用者批准`offline_access`作用功能時,你的應用可以從 Microsoft 標識平臺令牌終結點接收刷新權杖。 重新整理權杖是長期權杖。 您的應用程式可以在舊存取權杖到期時取得新的存取權杖。

> [!NOTE]
> 此許可權今天出現在所有同意螢幕上,即使對於不提供刷新令牌([隱式流](v2-oauth2-implicit-grant-flow.md))的流也是如此。  這是用於涵蓋客戶端可以在隱式流中開始,然後移動到預期刷新令牌的代碼流中的情況。

在 Microsoft 標識平臺上(向 v2.0 終結點發出的請求),你的應用`offline_access`必須顯式請求 作用域才能接收刷新令牌。 這意謂著當您在 [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md)中兌換授權碼時，您只會從 `/token` 端點收到存取權杖。 存取權杖的有效期短。 存取權杖的有效期通常在一小時內。 屆時，您的應用程式將必須把使用者重新導向回 `/authorize` 端點，以擷取新的授權碼。 在此重新導向期間，視應用程式的類型而定，使用者可能需要重新輸入其認證或重新同意權限。

有關如何獲取和使用刷新權杖的詳細資訊,請參閱[Microsoft 識別平台協定引用](active-directory-v2-protocols.md)。

## <a name="requesting-individual-user-consent"></a>要求個別使用者同意

在 [OpenID Connect 或 OAuth 2.0](active-directory-v2-protocols.md) 授權要求中，應用程式可以使用 `scope` 查詢參數來要求它所需的權限。 例如，當使用者登入應用程式時，應用程式會傳送如以下範例的要求 (加入分行符號是為了增加可讀性)：

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

`scope` 參數是應用程式所要求的委派權限清單 (以空格分隔)。 藉由將權限值附加至資源的識別碼 (應用程式識別碼 URI)，即可指出每個權限。 在要求範例中，應用程式需要權限來讀取使用者的行事曆，以及以使用者身分傳送郵件。

使用者輸入其憑據后,Microsoft 標識平台終結點將檢查*使用者同意*的匹配記錄。 如果用戶過去未同意任何請求的許可權,也未代表整個組織同意管理員使用這些許可權,Microsoft 標識平臺終結點將要求使用者授予請求的許可權。

> [!NOTE]
>此時，`offline_access` (「維持存取您可存取的資料」) 和 `user.read` (「將您登入並讀取您的設定檔」) 權限會自動包含在應用程式的初始同意中。  通常需要這些權限，才能獲得適當的應用程式功能 - `offline_access` 可供應用程式存取重新整理權杖 (對原生和 Web 應用程式都很重要)，而 `user.read` 可供存取 `sub` 宣告，讓用戶端或應用程式能正確地隨著時間識別使用者及存取基本使用者資訊。

![顯示工作帳戶同意的範例螢幕擷取](./media/v2-permissions-and-consent/work_account_consent.png)

當使用者核准權限要求時，系統就會記錄同意，使用者在後續登入應用程式時將不需要重新表示同意。

## <a name="requesting-consent-for-an-entire-tenant"></a>要求整個租用戶的同意

通常，當組織購買應用程式的授權或訂用帳戶時，都會想要主動設定應用程式以供組織的所有成員使用。 在此過程中，系統管理員可以授與同意，讓應用程式代表租用戶中的任何使用者執行動作。 如果系統管理員為整個租用戶授與同意，該組織的使用者便不會看見應用程式的同意頁面。

若要為租用戶中的所有使用者要求委派權限的同意，您的應用程式可以使用系統管理員同意端點。

此外，應用程式必須使用管理員同意端點來要求應用程式權限。

## <a name="admin-restricted-permissions"></a>受管理員限制的權限

Microsoft 生態系統中的某些高特權權限可以設定為「受系統管理員限制」**。 這類權限的範例包括：

* 使用 `User.Read.All` 讀取所有使用者的完整設定檔
* 使用 `Directory.ReadWrite.All` 將資料寫入組織的目錄
* 使用 `Groups.Read.All` 讀取組織目錄中的所有群組

雖然取用者使用者可以為應用程式授與這類資料的存取權，但組織使用者會受到限制，而無法授與同一組機密公司資料的存取權。 如果應用程式請求組織使用者訪問這些許可權之一,則使用者會收到一條錯誤消息,指出他們無權同意應用的許可權。

如果您的應用程式需要存取組織的受系統管理員限制範圍，您應該同樣使用系統管理員同意端點，直接向公司系統管理員要求權限，接下來將會說明。

如果應用程式要求高權限的委派權限，且系統管理員透過管理員同意端點授與了這些權限，則會為租用戶中的所有使用者授與同意。

如果應用程式請求應用程式許可權,並且管理員通過管理員同意終結點授予這些許可權,則此授予不會代表任何特定使用者完成。 此時會*直接*為用戶端應用程式授與權限。 這些類型的許可權僅由守護進程服務在後台運行的其他非互動式應用程式使用。

## <a name="using-the-admin-consent-endpoint"></a>使用系統管理員同意端點

> [!NOTE]
> 請注意,在使用管理員同意終結點授予管理員同意后,您已完成授予管理員同意,使用者無需執行任何其他操作。 授予管理員同意后,使用者可以通過典型的身份驗證流獲取訪問權杖,並且生成的訪問權杖將具有同意的許可權。

如果公司的系統管理員在使用您的應用程式時被導向至授權端點，Microsoft 身分識別平台將會偵測使用者的角色，並詢問他們是否要代表整個租用戶同意您所要求的權限。 不過，還有如果您想要主動要求由系統管理員代表整個租用戶授與權限，您也可以使用專用的管理員同意端點。 使用此終結點對於請求應用程式許可權(無法使用授權終結點請求)也是必需的。

如果您依照這些步驟，您的應用程式便能為租用戶中所有的使用者要求權限，包括受管理員限制的範圍。 這是高權限作業，只有在您的案例有需要時才應執行。

若要查看實作這些步驟的程式碼範例，請參閱[受系統管理員限制的範圍範例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>在應用程式註冊入口網站中要求權限

應用程式能夠記錄在應用註冊門戶中需要哪些許可權(委派和應用程式)。  這允許使用`/.default`作用域和 Azure 門戶的「授予管理員同意」 選項。  通常,最佳做法是確保為給定應用程式靜態定義的許可權是它將動態/增量請求的許可權的超級集。

> [!NOTE]
>應用程式許可權只能通過[`/.default`](#the-default-scope)- 因此,如果應用需要應用程式許可權,請確保它們列在應用註冊門戶中。

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>為應用程式設定靜態要求的權限清單

1. 跳到 Azure 門戶中的應用程式[– 應用程式的註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗,或如果尚未建立應用,則[建立應用](quickstart-register-app.md)。
2. 找到**API 許可權**部分,並在 API 許可權中按一下「添加許可權」。
3. 從可用 API 清單中選擇**Microsoft 圖形**,然後添加應用所需的許可權。
3. [儲存]**** 應用程式註冊。

### <a name="recommended-sign-the-user-into-your-app"></a>推薦:將使用者登錄到你的應用

通常，當您建置使用系統管理員同意端點的應用程式時，應用程式會需要一個可供系統管理員核准應用程式權限的頁面或檢視。 此頁面可以是應用程式註冊流程的一部分、應用程式設定的一部分，或是專用的「連接」流程。 在許多情況下，應用程式只在使用者利用工作或學校 Microsoft 帳戶登入之後顯示此「連接」檢視是很合理的。

將使用者登入應用程式時，您可以先識別系統管理員所屬的組織，然後再要求他們核准必要的權限。 雖然這並非絕對必要，但這麼做可協助您為組織使用者建立更直覺式的體驗。 要登錄使用者,請按照我們的[Microsoft 身份平台協定教程](active-directory-v2-protocols.md)進行操作。

### <a name="request-the-permissions-from-a-directory-admin"></a>向目錄管理員要求權限

當您準備好向組織的管理員請求許可權時,可以將使用者重定向到 Microsoft 標識平臺*管理員同意終結點*。

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
| `tenant` | 必要 | 您想要要求權限的目錄租用戶。 可以以 GUID 或友好名稱格式提供,也可以與示例中的組織進行一般引用。 不要使用"通用",因為個人帳戶不能提供管理員同意,除非是在租戶的上下文中。 為確保與管理租戶的個人帳戶保持最佳相容性,請使用租戶 ID。 |
| `client_id` | 必要 | [Azure 門戶和應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗**的應用程式(用戶端)ID**分配給應用。 |
| `redirect_uri` | 必要 |您想要傳送回應以供應用程式處理的重新導向 URI。 它必須與您在應用程式註冊入口網站中註冊的其中一個重新導向 URI 完全相符。 |
| `state` | 建議 | 同樣會隨權杖回應傳回之要求中所包含的值。 它可以是您想要的任何內容的字串。 請在驗證要求出現之前，先使用此狀態在應用程式中將使用者狀態的相關資訊 (例如他們之前所在的網頁或檢視) 編碼。 |
|`scope`        | 必要        | 定義應用程式請求的許可權集。 這可以是靜態(使用[`/.default`](#the-default-scope))或動態作用域。  這可以包括 OIDC`openid`範圍`profile``email`(、 與 % 。 如果需要應用程式許可權,則必須使用`/.default`請求靜態配置的許可權清單。  |


此時，Azure AD 會要求租用戶系統管理員登入來完成要求。 要求管理員批准您在`scope`參數中請求的所有許可權。  如果您使用了靜態(`/.default`) 值,它將像 v1.0 管理員同意終結點一樣運行,並請求對應用所需許可權中找到的所有作用域進行同意。

#### <a name="successful-response"></a>成功回應

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

如果系統管理員沒有為您的應用程式核准權限，則失敗的回應看起來會像這樣︰

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 描述 |
| --- | --- |
| `error` | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` | 可協助開發人員識別錯誤根本原因的特定錯誤訊息。 |

從系統管理員同意端點收到成功回應之後，您的應用程式便已取得它所要求的權限。 接著，您可以針對您想要的資源要求權杖。

## <a name="using-permissions"></a>使用權限

在使用者同意您的應用程式的權限之後，您的應用程式即可取得存取權杖，而這些權杖代表您的應用程式存取資源的權限。 一個存取權杖只能用於一個單一資源，但存取權杖內部所編碼的是您應用程式針對該資源已獲得的每項權限。 要取得訪問權杖,你的應用可以向 Microsoft 標識平臺權杖終結點發出請求,如下所示:

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

您可以在對資源提出的 HTTP 要求中使用產生的存取權杖。 它會可靠地向資源指出您的應用程式具有可執行特定工作的適當權限。

有關 OAuth 2.0 協定以及如何取得存取權杖的詳細資訊,請參閱[Microsoft 識別平台終結點協定引用](active-directory-v2-protocols.md)。

## <a name="the-default-scope"></a>/.default 範圍

可以使用作用域`/.default`幫助將應用從 v1.0 終結點遷移到 Microsoft 標識平台終結點。 這是每個應用程式的內建範圍，其參考在應用程式註冊時設定的靜態權限清單。 `scope` 值為 `https://graph.microsoft.com/.default` 在功能上與 v1.0 端點 `resource=https://graph.microsoft.com` 相同 - 也就是說，它會對應用程式已在 Azure 入口網站中註冊的 Microsoft Graph 範圍要求權杖。  它使用資源 URI`/.default`* 建構(例如,如果`https://contosoApp.com`資源 URI 為`https://contosoApp.com/.default`,則請求的範圍為 )。  有關必須包含第二個斜杠才能正確請求權杖的情況,請參閱[尾隨斜杠部分](#trailing-slash-and-default)。

/.default 作用功能可用於任何 OAuth 2.0 流,但在[代表流](v2-oauth2-on-behalf-of-flow.md)和[用戶端憑據流](v2-oauth2-client-creds-grant-flow.md)中使用,以及使用 v2 管理員同意終結點請求應用程式許可權時是必需的。

> [!NOTE]
> 用戶端不能在單個請求中組合`/.default`靜態 ( ) 和動態同意。 因此，`scope=https://graph.microsoft.com/.default+mail.read` 會因為範圍類型的組合而導致錯誤。

### <a name="default-and-consent"></a>/.default 與同意

`/.default` 範圍也會對 `prompt=consent` 觸發 v1.0 端點行為。 不論資源為何，它都會要求同意應用程式註冊的所有權限。 如果作為請求的一部分包含在其中,`/.default`則作用域將返回一個令牌,其中包含所請求的資源的範圍。

### <a name="default-when-the-user-has-already-given-consent"></a>使用者已經同意時的 /.default

因為`/.default` 在功能上等同於以 `resource` 為中心的 v1.0 端點行為，所以也會提供 v1.0 端點的同意行為。 也就是說，如果使用者尚未在用戶端與資源之間授與任何權限，`/.default` 只會觸發同意提示。 如果存在這類同意，則會傳回權杖，其中包含該資源的使用者授與的所有範圍。 不過，如果尚未授與權限，或已提供 `prompt=consent` 參數，則會對用戶端應用程式註冊的所有範圍顯示同意提示。

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>範例 1:使用者或租戶管理員已授予許可權

這個功能中,使用者(或租戶管理員)已授予用戶端 Microsoft 圖形`mail.read`權`user.read`限和 。 如果用戶端對 `scope=https://graph.microsoft.com/.default` 提出要求，則不論向 Microsoft Graph 註冊權限的用戶端應用程式的內容為何，都不會顯示同意提示。 系統會傳回一個權杖，其中包含 `mail.read` 和 `user.read` 範圍。

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>範例 2:使用者尚未授予用戶端和資源之間的權限

在此範例中,用戶端和Microsoft圖形之間不存在對使用者的同意。 用戶端已註冊 `user.read` 和 `contacts.read` 權限，以及 Azure Key Vault 範圍 `https://vault.azure.net/user_impersonation`。 當用戶端向 `scope=https://graph.microsoft.com/.default` 要求權杖時，使用者會看見 `user.read`、`contacts.read` 和 Key Vault `user_impersonation` 範圍的同意畫面。 返回的權杖將只有和`user.read``contacts.read`作用域,並且僅可用於針對Microsoft圖形。

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>範例 3:使用者已同意,用戶端請求其他作用域

在此示例中,使用者已同意`mail.read`用戶端。 用戶端已在其註冊中註冊 `contacts.read` 範圍。 當用戶端使用 請求權`scope=https://graph.microsoft.com/.default`杖 並透過`prompt=consent`請求同意時,使用者將看到應用程式註冊的所有(且僅)許可權的同意螢幕。 `contacts.read` 將會呈現在同意畫面中，但 `mail.read` 不會。 傳回的權杖會適用於 Microsoft Graph，而且包含 `mail.read` 和 `contacts.read`。

### <a name="using-the-default-scope-with-the-client"></a>使用 /.default 範圍搭配用戶端

當用戶端要求自己的 `/.default` 範圍時，`/.default` 範圍有特殊案例存在。 下列範例示範此案例。

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

這會對所有已註冊的權限產生同意畫面 (根據上述的同意和 `/.default` 描述，如果適用的話)，然後傳回 id_token，而不是存取權杖。  此行為存在於從 ADAL 移動到 MSAL 的某些舊用戶端,**並且不應**由針對 Microsoft 識別平臺終結點的新用戶端使用。

### <a name="trailing-slash-and-default"></a>尾隨斜線與 /.預設值

某些資源URI具有尾隨斜杠`https://contoso.com/`(`https://contoso.com`而不是 ),這可能會導致令牌驗證出現問題。  這主要發生在為 Azure 資源管理`https://management.azure.com/`() 請求權杖時,該令牌在其資源 URI 上具有尾隨斜杠,並且要求在請求權杖時存在該標記。  因此,當請求權杖`https://management.azure.com/`和使用`/.default`時,您必須請求`https://management.azure.com//.default`- 注意雙斜杠!

通常 - 如果您已驗證正在頒發權杖,並且權杖被應接受它的 API 拒絕,請考慮添加第二個斜杠,然後重試。 這是因為登入伺服器發出`scope`與 參數中URI匹配的受眾的權杖 -`/.default`從末尾刪除。  如果刪除尾隨斜杠,登錄伺服器仍會處理請求並針對資源 URI 對其進行驗證,即使它們不再匹配 - 這是非標準,應用程式不應依賴。

## <a name="troubleshooting-permissions-and-consent"></a>針對權限和同意進行疑難排解

如果您或應用程式的使用者在同意過程中看到意外錯誤,請參閱本文,瞭解故障排除步驟:[執行應用程式同意時出現意外錯誤](../manage-apps/application-sign-in-unexpected-user-consent-error.md)。
