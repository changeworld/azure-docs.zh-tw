---
title: Microsoft 身分識別平臺範圍、許可權和同意
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
ms.custom: aaddev, fasttrack-edit, contperfq1, identityplatformtop40
ms.openlocfilehash: 79475414f6785474596beae208fefae81a673dea
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842677"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft 身分識別平台端點中的權限和同意

與 Microsoft 身分識別平台整合的應用程式，會遵循可讓使用者和系統管理員控制資料存取方式的授權模型。 已在 Microsoft 身分識別平臺端點上更新授權模型的執行，並變更應用程式必須與 Microsoft 身分識別平臺互動的方式。 本文涵蓋此授權模型的基本概念，包括範圍、權限及同意。

## <a name="scopes-and-permissions"></a>範圍和權限

Microsoft 身分識別平台會實作 [OAuth 2.0](active-directory-v2-protocols.md) 授權通訊協定。 OAuth 2.0 是一種可讓協力廠商應用程式代表使用者存取 Web 主控資源的方法。 任何與 Microsoft 身分識別平台整合的 Web 主控資源都具有資源識別碼 (或稱為「應用程式識別碼 URI」**)。 例如，Microsoft 的 Web 主控資源包括：

* Microsoft Graph：`https://graph.microsoft.com`
* Microsoft 365 Mail API： `https://outlook.office.com`
* Azure Key Vault： `https://vault.azure.net`

> [!NOTE]
> 強烈建議您使用 Microsoft Graph 而不是 Microsoft 365 Mail API 等等。

這也適用於已與 Microsoft 身分識別平台整合的第三方資源。 任何這些資源也都可以定義一組權限，可用來進一步細分該資源的功能。 例如，[Microsoft Graph](https://graph.microsoft.com) 除了別的之外，還定義了權限來執行下列工作：

* 讀取使用者的行事曆
* 寫入使用者的行事曆
* 以使用者身分傳送郵件

藉由定義這些類型的權限，資源可以更精細地掌控其資料及 API 功能的公開方式。 第三方應用程式可向使用者和系統管理員要求這些權限，且必須在他們核准要求後，應用程式才可存取資料或代表使用者執行動作。 透過將資源的功能切割成較小的權限集，便可將協力廠商應用程式建置成只要求它們執行其功能所需的特定權限。 使用者和系統管理員可以確切地知道應用程式可存取的資料，而且可以更確信它不是在惡意意圖上運作。 開發人員應一律遵守最低權限的概念，而僅就其應用程式運作所需的程度要求權限。

在 OAuth 2.0 中，這些類型的權限也稱為「範圍」**。 它們也經常稱為 *許可權*。 權限在 Microsoft 身分識別平台中會以字串值表示。 繼續討論 Microsoft Graph 範例，每個權限的字串值如下：

* 使用 `Calendars.Read` 來讀取使用者的行事曆
* 使用 `Calendars.ReadWrite` 來寫入使用者的行事曆
* 使用 `Mail.Send` 來以使用者身分傳送郵件

應用程式最常要求這些許可權的方法，是在 Microsoft 身分識別平臺授權端點的要求中指定範圍。 不過，某些高許可權許可權只能透過系統管理員同意授與，並使用 [系統管理員同意端點](#admin-restricted-permissions)來要求/授與。 繼續閱讀以深入了解。

## <a name="permission-types"></a>權限類型

Microsoft 身分識別平台支援兩種類型的權限：**委派權限**和**應用程式權限**。

* **委派權限**供已有登入使用者的應用程式使用。 針對這些應用程式，使用者或系統管理員會同意應用程式所要求的許可權，而應用程式會委派許可權，以在呼叫目標資源時作為已登入的使用者。 有些委派權限可由非系統管理使用者同意，但有些較高的特定權限則需要[系統管理員的同意](#admin-restricted-permissions)。 若要了解哪些系統管理員角色可同意委派權限，請參閱 [Azure AD 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)。

* **應用程式權限**供沒有登入使用者的應用程式在執行時使用；例如，當作背景服務或精靈來執行的應用程式。  應用程式權限只能[由系統管理員同意](#requesting-consent-for-an-entire-tenant)。

_有效權限_ 是應用程式向目標資源提出要求時所將具備的權限。 請務必瞭解您的應用程式被授與的委派和應用程式許可權之間的差異，以及對目標資源進行呼叫時的有效許可權。

- 就委派權限來說，應用程式的 _有效權限_ 是應用程式 (透過同意) 所獲得授與的委派權限和目前已登入使用者的權限二者的最小權限交集。 應用程式絕對不會擁有已登入使用者權限以外的權限。 在組織內，已登入使用者的權限會由原則或是一或多個系統管理員角色的成員資格決定。 若要了解哪些系統管理員角色可同意委派權限，請參閱 [Azure AD 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)。

   例如，假設已經對您的應用程式授與 _User.ReadWrite.All_ 委派權限。 此權限名義上會對應用程式授與讀取及更新組織中每個使用者設定檔的權限。 如果已登入使用者是全域管理員，應用程式便能夠更新組織中每個使用者的設定檔。 不過，如果登入的使用者不是系統管理員角色，您的應用程式將只能更新已登入使用者的設定檔。 應用程式有權代表其行事的使用者沒有這些權限，因此應用程式無法更新組織中其他使用者的設定檔。

- 就應用程式權限來說，應用程式的 _有效權限_ 將是權限所隱含的完整層級權限。 例如，具有 _User.ReadWrite.All_ 應用程式權限的應用程式可以更新組織中每個使用者的設定檔。

## <a name="openid-connect-scopes"></a>OpenId Connect 範圍

OpenID Connect 的 Microsoft 身分識別平臺會有一些定義完善的範圍，這些範圍也裝載于 Microsoft Graph： `openid` 、、 `email` `profile` 和 `offline_access` 。 不支援 `address` 和 `phone` OpenID Connect 範圍。

要求 OIDC 範圍和權杖會為您提供權杖，以呼叫 [使用者資訊端點](userinfo.md)。

### <a name="openid"></a>openid

如果應用程式使用 [OpenID Connect](active-directory-v2-protocols.md) 來執行登入，它就必須要求 `openid` 範圍。 `openid` 範圍會在工作帳戶同意頁面上顯示為「將您登入」權限，而在個人 Microsoft 帳戶同意頁面上會顯示為「檢視您的設定檔並使用您的 Microsoft 帳戶連接到應用程式和服務」權限。 有了此權限之後，應用程式便能夠以 `sub` 宣告的形式接收使用者的唯一識別碼。 它也會為應用程式提供 UserInfo 端點的存取權。 `openid`範圍可用於 Microsoft 身分識別平臺權杖端點，以取得可供應用程式用來進行驗證的識別碼權杖。

### <a name="email"></a>電子郵件

`email` 範圍可以與 `openid` 範圍及任何其他範圍搭配使用。 它會以 `email` 宣告的形式為應用程式提供使用者主要電子郵件地址的存取權。 `email`只有當電子郵件地址與使用者帳戶相關聯時，才會將宣告包含在權杖中，這種情況不一定是如此。 如果它使用 `email` 範圍，您的應用程式就應該做好準備，以處理權杖中沒有 `email` 宣告的情況。

### <a name="profile"></a>profile

`profile` 範圍可以與 `openid` 範圍及任何其他範圍搭配使用。 它會為應用程式提供大量使用者相關資訊的存取權。 它可以存取的資訊包括但不限於使用者的名字、姓氏、慣用的使用者名稱，以及物件識別碼。 如需特定使用者的 id_tokens 參數中可用的設定檔宣告完整清單，請參閱[ `id_tokens` 參考](id-tokens.md)。

### <a name="offline_access"></a>offline_access

[ `offline_access` 範圍](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)可讓您的應用程式代表使用者存取資源，延長一段時間。 在同意頁面上，此範圍會顯示為「維持存取您可存取的資料」權限。 當使用者核准範圍時 `offline_access` ，您的應用程式可以接收來自 Microsoft 身分識別平臺權杖端點的重新整理權杖。 重新整理權杖是長期權杖。 您的應用程式可以在舊存取權杖到期時取得新的存取權杖。

> [!NOTE]
> 此許可權現在會出現在所有同意畫面上，即使是未提供重新整理權杖 ([隱含流程](v2-oauth2-implicit-grant-flow.md)) 的流程。  這是為了涵蓋用戶端可以在隱含流程內開始的案例，然後移至預期需要重新整理權杖的程式碼流程。

在 Microsoft 身分識別平臺上 (對 v2.0 端點提出的要求) ，您的應用程式必須明確要求 `offline_access` 範圍，才能接收重新整理權杖。 這意謂著當您在 [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md)中兌換授權碼時，您只會從 `/token` 端點收到存取權杖。 存取權杖的有效期短。 存取權杖的有效期通常在一小時內。 屆時，您的應用程式將必須把使用者重新導向回 `/authorize` 端點，以擷取新的授權碼。 在此重新導向期間，視應用程式的類型而定，使用者可能需要重新輸入其認證或重新同意權限。

如需如何取得及使用重新整理權杖的詳細資訊，請參閱 [Microsoft 身分識別平臺通訊協定參考](active-directory-v2-protocols.md)。

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

在使用者輸入其認證之後，Microsoft 身分識別平臺端點會檢查是否有相符的 *使用者同意*記錄。 如果使用者未在過去同意任何要求的許可權，也沒有系統管理員代表整個組織同意這些許可權，Microsoft 身分識別平臺端點會要求使用者授與所要求的許可權。

> [!NOTE]
>此時，`offline_access` (「維持存取您可存取的資料」) 和 `user.read` (「將您登入並讀取您的設定檔」) 權限會自動包含在應用程式的初始同意中。  通常需要這些權限，才能獲得適當的應用程式功能 - `offline_access` 可供應用程式存取重新整理權杖 (對原生和 Web 應用程式都很重要)，而 `user.read` 可供存取 `sub` 宣告，讓用戶端或應用程式能正確地隨著時間識別使用者及存取基本使用者資訊。

![顯示工作帳戶同意的範例螢幕擷取畫面](./media/v2-permissions-and-consent/work_account_consent.png)

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

雖然取用者使用者可以為應用程式授與這類資料的存取權，但組織使用者會受到限制，而無法授與同一組機密公司資料的存取權。 如果您的應用程式向組織使用者要求這其中一個權限的存取權，使用者將會收到錯誤訊息，指出他們未獲授權而無法對您應用程式的權限表示同意。

如果您的應用程式需要存取組織的受系統管理員限制範圍，您應該同樣使用系統管理員同意端點，直接向公司系統管理員要求權限，接下來將會說明。

如果應用程式要求高權限的委派權限，且系統管理員透過管理員同意端點授與了這些權限，則會為租用戶中的所有使用者授與同意。

如果應用程式要求應用程式許可權，且系統管理員透過管理員同意端點授與這些許可權，則不會代表任何特定使用者來執行此授與。 此時會*直接*為用戶端應用程式授與權限。 這些類型的許可權只會由 daemon 服務和在背景中執行的其他非互動式應用程式使用。

## <a name="using-the-admin-consent-endpoint"></a>使用系統管理員同意端點

> [!NOTE]
> 請注意，在使用系統管理員同意端點授與系統管理員同意之後，您已完成授與系統管理員同意的許可權，而且使用者不需要執行任何進一步的其他動作。 授與系統管理員同意之後，使用者可以透過一般的驗證流程取得存取權杖，而產生的存取權杖將會擁有同意的許可權。

如果公司的系統管理員在使用您的應用程式時被導向至授權端點，Microsoft 身分識別平台將會偵測使用者的角色，並詢問他們是否要代表整個租用戶同意您所要求的權限。 不過，還有如果您想要主動要求由系統管理員代表整個租用戶授與權限，您也可以使用專用的管理員同意端點。 您也必須使用此端點來要求應用程式許可權， (無法使用授權端點) 來要求這些許可權。

如果您依照這些步驟，您的應用程式便能為租用戶中所有的使用者要求權限，包括受管理員限制的範圍。 這是高權限作業，只有在您的案例有需要時才應執行。

若要查看實作這些步驟的程式碼範例，請參閱[受系統管理員限制的範圍範例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>在應用程式註冊入口網站中要求權限

應用程式可以在應用程式註冊入口網站中，注意 (委派和應用程式) 所需的許可權。  這可讓您使用 `/.default` 範圍和 Azure 入口網站的「授與系統管理員同意」選項。  一般而言，最佳做法是確保針對指定的應用程式所靜態定義的許可權是其將以動態/增量方式要求的許可權超集合。

> [!NOTE]
>您只能透過使用來要求應用程式許可權 [`/.default`](#the-default-scope) ，因此，如果您的應用程式需要應用程式許可權，請確定它們列在應用程式註冊入口網站中。

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>若要設定應用程式的靜態要求許可權清單

1. 在 Azure 入口網站中，移至您的應用程式 [-應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908) 體驗，或 [建立應用](quickstart-register-app.md) 程式（如果尚未這麼做）。
2. 找出 [ **Api 許可權** ] 區段，然後在 [api 許可權] 內按一下 [新增許可權]。
3. 從可用的 Api 清單中選取 **Microsoft Graph** ，然後新增您應用程式所需的許可權。
3. [儲存]**** 應用程式註冊。

### <a name="recommended-sign-the-user-into-your-app"></a>建議：將使用者登入您的應用程式

通常，當您建置使用系統管理員同意端點的應用程式時，應用程式會需要一個可供系統管理員核准應用程式權限的頁面或檢視。 此頁面可以是應用程式註冊流程的一部分、應用程式設定的一部分，或是專用的「連接」流程。 在許多情況下，應用程式只在使用者利用工作或學校 Microsoft 帳戶登入之後顯示此「連接」檢視是很合理的。

將使用者登入應用程式時，您可以先識別系統管理員所屬的組織，然後再要求他們核准必要的權限。 雖然這並非絕對必要，但這麼做可協助您為組織使用者建立更直覺式的體驗。 若要將使用者登入，請遵循我們的 [Microsoft 身分識別平臺通訊協定教學](active-directory-v2-protocols.md)課程。

### <a name="request-the-permissions-from-a-directory-admin"></a>向目錄管理員要求權限

當您準備好向組織的系統管理員要求許可權時，您可以將使用者重新導向至 Microsoft 身分識別平臺系統 *管理員同意端點*。

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
| `tenant` | 必要 | 您想要要求權限的目錄租用戶。 可以用 GUID 或易記名稱格式提供，或是與組織一般參考，如範例中所示。 請勿使用「一般」，因為個人帳戶無法在租使用者的內容中提供系統管理員同意。 若要確保與管理租使用者的個人帳戶具有最佳相容性，請盡可能使用租使用者識別碼。 |
| `client_id` | 必要 | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗指派給您應用程式的**應用程式 (用戶端) 識別碼**。 |
| `redirect_uri` | 必要 |您想要傳送回應以供應用程式處理的重新導向 URI。 它必須與您在應用程式註冊入口網站中註冊的其中一個重新導向 URI 完全相符。 |
| `state` | 建議 | 包含在要求中的值，也會在權杖回應中傳回。 它可以是您想要的任何內容的字串。 請在驗證要求出現之前，先使用此狀態在應用程式中將使用者狀態的相關資訊 (例如他們之前所在的網頁或檢視) 編碼。 |
|`scope`        | 必要        | 定義應用程式所要求的許可權集合。 這可以是使用 [`/.default`](#the-default-scope)) 或動態範圍的靜態 (。  這可以包括 OIDC 範圍 (`openid` 、 `profile` `email`) 。 如果您需要應用程式許可權，您必須使用 `/.default` 來要求靜態設定的許可權清單。  |


此時，Azure AD 會要求租用戶系統管理員登入來完成要求。 系統會要求系統管理員核准您在參數中要求的擁有權限 `scope` 。  如果您已使用靜態 (`/.default`) 值，其運作方式就像 v1.0 系統管理員同意端點，而且會要求應用程式所需許可權中的所有範圍要求同意。

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

在使用者同意您的應用程式的權限之後，您的應用程式即可取得存取權杖，而這些權杖代表您的應用程式存取資源的權限。 一個存取權杖只能用於一個單一資源，但存取權杖內部所編碼的是您應用程式針對該資源已獲得的每項權限。 若要取得存取權杖，您的應用程式可以向 Microsoft 身分識別平臺權杖端點提出要求，如下所示：

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

如需 OAuth 2.0 通訊協定以及如何取得存取權杖的詳細資訊，請參閱 [Microsoft 身分識別平臺端點通訊協定參考](active-directory-v2-protocols.md)。

## <a name="the-default-scope"></a>/.default 範圍

您可以使用此 `/.default` 範圍來協助將您的應用程式從 v1.0 端點遷移至 Microsoft 身分識別平臺端點。 這是每個應用程式的內建範圍，其參考在應用程式註冊時設定的靜態權限清單。 `scope` 值為 `https://graph.microsoft.com/.default` 在功能上與 v1.0 端點 `resource=https://graph.microsoft.com` 相同 - 也就是說，它會對應用程式已在 Azure 入口網站中註冊的 Microsoft Graph 範圍要求權杖。  它是使用資源 URI + `/.default` (例如，如果資源 uri 為，則會 `https://contosoApp.com`) 要求的範圍 `https://contosoApp.com/.default` 。  如需您必須包含第二個斜線才能正確要求權杖的案例，請參閱 [尾端斜線的區段](#trailing-slash-and-default) 。

/.Default 範圍可用於任何 OAuth 2.0 流程，但在代理者 [流程](v2-oauth2-on-behalf-of-flow.md) 和 [用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)中是必要的，而且在使用 v2 系統管理員同意端點來要求應用程式許可權時也是必要的。

> [!NOTE]
> 用戶端無法 `/.default` 在單一要求中結合靜態 () 和動態同意。 因此，`scope=https://graph.microsoft.com/.default+mail.read` 會因為範圍類型的組合而導致錯誤。

### <a name="default-and-consent"></a>/.default 與同意

`/.default` 範圍也會對 `prompt=consent` 觸發 v1.0 端點行為。 不論資源為何，它都會要求同意應用程式註冊的所有權限。 如果納入為要求的一部分，則 `/.default` 範圍會傳回包含所要求資源範圍的權杖。

### <a name="default-when-the-user-has-already-given-consent"></a>使用者已經同意時的 /.default

因為`/.default` 在功能上等同於以 `resource` 為中心的 v1.0 端點行為，所以也會提供 v1.0 端點的同意行為。 也就是說，如果使用者尚未在用戶端與資源之間授與任何權限，`/.default` 只會觸發同意提示。 如果存在這類同意，則會傳回權杖，其中包含該資源的使用者授與的所有範圍。 不過，如果尚未授與權限，或已提供 `prompt=consent` 參數，則會對用戶端應用程式註冊的所有範圍顯示同意提示。

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>範例1：使用者（或租使用者管理員）已授與許可權

在此範例中，使用者 (或租使用者系統管理員) 已將 Microsoft Graph 許可權授與用戶端 `mail.read` `user.read` 。 如果用戶端對 `scope=https://graph.microsoft.com/.default` 提出要求，則不論向 Microsoft Graph 註冊權限的用戶端應用程式的內容為何，都不會顯示同意提示。 系統會傳回一個權杖，其中包含 `mail.read` 和 `user.read` 範圍。

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>範例2：使用者未在用戶端與資源之間授與許可權

在此範例中，用戶端與 Microsoft Graph 之間不會有使用者的同意。 用戶端已註冊 `user.read` 和 `contacts.read` 權限，以及 Azure Key Vault 範圍 `https://vault.azure.net/user_impersonation`。 當用戶端向 `scope=https://graph.microsoft.com/.default` 要求權杖時，使用者會看見 `user.read`、`contacts.read` 和 Key Vault `user_impersonation` 範圍的同意畫面。 傳回的權杖只會有 `user.read` 和 `contacts.read` 範圍，且僅適用于 Microsoft Graph。

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>範例3：使用者已同意且用戶端要求其他範圍

在此範例中，使用者已同意 `mail.read` 用戶端。 用戶端已在其註冊中註冊 `contacts.read` 範圍。 當用戶端使用提出要求權杖 `scope=https://graph.microsoft.com/.default` 並要求同意時 `prompt=consent` ，使用者將會看到所有 (的同意畫面，而且只會) 應用程式所註冊的許可權。 `contacts.read` 將會呈現在同意畫面中，但 `mail.read` 不會。 傳回的權杖會適用於 Microsoft Graph，而且包含 `mail.read` 和 `contacts.read`。

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

這會對所有已註冊的權限產生同意畫面 (根據上述的同意和 `/.default` 描述，如果適用的話)，然後傳回 id_token，而不是存取權杖。  此行為適用于從 ADAL 移至 MSAL 的某些舊版用戶端，而且 **不應該** 由目標為 Microsoft 身分識別平臺端點的新用戶端使用。

### <a name="client-credentials-grant-flow-and-default"></a>用戶端認證授與流程和/.default

另一種用法 `./default` 是在要求應用程式許可權 (或在非互動式應用程式中) *角色* ，例如使用 [用戶端認證](v2-oauth2-client-creds-grant-flow.md) 授與流程來呼叫 web API 的 daemon 應用程式。

若要建立 web API)  (角色的應用程式許可權，請參閱 [如何：在應用程式中新增](howto-add-app-roles-in-azure-ad-apps.md)應用程式角色。

用戶端應用程式中的用戶端認證要求 **必須** 包含 `scope={resource}/.default` ，其中 `{resource}` 是您的應用程式要呼叫的 web API。 **不**支援使用個別應用程式許可權來發出用戶端認證要求 (角色) 。 已授與該 web API 的所有應用程式許可權 (角色) 將會包含在傳回的存取權杖中。

若要授與您所定義之應用程式許可權的存取權，包括授與應用程式的系統管理員同意，請參閱 [快速入門：設定用戶端應用程式以存取 WEB API](quickstart-configure-app-access-web-apis.md)。

### <a name="trailing-slash-and-default"></a>尾端斜線和/.default

某些資源 Uri 的尾端斜線 (`https://contoso.com/` 相對於 `https://contoso.com`) ，這可能會導致權杖驗證發生問題。  這可能會發生在要求 Azure 資源管理的權杖時 (`https://management.azure.com/`) ，其資源 URI 上的尾端斜線為，而且要求權杖時必須要有該權杖。  因此，在要求和使用的權杖時， `https://management.azure.com/` `/.default` 您必須要求 `https://management.azure.com//.default` -記下雙斜線！

一般來說，如果您已驗證要發出權杖，而且 API 已拒絕該權杖接受它，請考慮新增第二個斜線，然後再試一次。 發生這種情況是因為登入伺服器發出權杖，而物件與參數中的 Uri 相符， `scope` 並 `/.default` 已從結尾移除。  如果移除尾端的斜線，登入伺服器仍會處理要求，並針對資源 URI 進行驗證，即使它們不再相符也是一樣-這是非標準的，而且不應該依賴您的應用程式。

## <a name="troubleshooting-permissions-and-consent"></a>針對權限和同意進行疑難排解

如果您或應用程式的使用者在同意程式中看到未預期的錯誤，請參閱這篇文章以取得疑難排解步驟：對 [應用程式執行同意時出現非預期的錯誤](../manage-apps/application-sign-in-unexpected-user-consent-error.md)。

## <a name="next-steps"></a>後續步驟

* [識別碼權杖 |Microsoft 身分識別平臺](id-tokens.md)
* [存取權杖 |Microsoft 身分識別平臺](access-tokens.md)
