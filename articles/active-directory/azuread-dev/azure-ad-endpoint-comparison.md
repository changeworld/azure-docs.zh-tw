---
title: 為何要更新至 Microsoft 身分識別平臺 (v2.0) |蔚藍
description: 瞭解 Microsoft 身分識別平臺 (v2.0) 端點和 Azure Active Directory (Azure AD) v1.0 端點之間的差異，並瞭解更新至 v2.0 的優點。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8f6170de65ae5e1ca8ecb5f7cc8a78f4f194ac41
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055285"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>為何要更新至 Microsoft 身分識別平台 (v2.0)？

開發新的應用程式時，請務必瞭解 Microsoft 身分識別平臺 (2.0 版) 和 Azure Active Directory (v1.0) 端點之間的差異。 本文涵蓋端點與 Microsoft 身分識別平臺的一些現有限制之間的主要差異。

## <a name="who-can-sign-in"></a>誰可以登入

![誰可以登入 v1.0 與 v2.0 端點](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* v1.0 端點只允許使用公司和學校帳戶登入您的應用程式 (Azure AD)
* Microsoft 身分識別平臺端點允許 Azure AD 的公司和學校帳戶，以及 (MSA) 的個人 Microsoft 帳戶（例如 hotmail.com、outlook.com 和 msn.com）登入。
* 這兩個端點也會接受針對設定為*[單一租](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* 使用者的應用程式，或針對設定為指向租使用者特定端點 () 之*多租*使用者應用程式之 Azure AD 目錄的*[來賓使用者](../external-identities/what-is-b2b.md)* 登入 `https://login.microsoftonline.com/{TenantId_or_Name}` 。

Microsoft 身分識別平臺端點可讓您撰寫應用程式，以接受來自個人 Microsoft 帳戶的登入，以及公司和學校帳戶。 這可讓您撰寫完全無從驗證帳戶的應用程式。 例如，如果您的應用程式呼叫 [Microsoft Graph](https://graph.microsoft.io) \(英文\)，則公司帳戶可取得一些額外的功能和資料，例如他們的 SharePoint 網站或目錄資料。 但在許多動作 (例如[讀取使用者的郵件](/graph/api/user-list-messages)) 中，相同的程式碼可以存取個人及公司和學校帳戶的電子郵件。

針對 Microsoft 身分識別平臺端點，您可以使用 Microsoft 驗證程式庫 (MSAL) 來取得取用者、教育和企業世界的存取權。 Azure AD v1.0 端點只接受來自公司和學校帳戶的登入。

## <a name="incremental-and-dynamic-consent"></a>增量和動態同意

使用 Azure AD v1.0 端點的應用程式必須事先指定其所需的 OAuth 2.0 權限，例如：

![顯示許可權註冊 UI 的範例](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

直接在應用程式註冊上設定的權限是**靜態的**。 雖然在 Azure 入口網站中定義應用程式的靜態使用權限，可讓程式碼保持簡潔，但它可能為開發人員帶來一些問題：

* 應用程式必須在使用者第一次登入時要求可能會需要的所有使用權限。 這可能會是一長串的使用權限，因此讓使用者不願意在初始登入時核准應用程式的存取。

* 應用程式必須事先知道可能會存取的所有資源。 很難建立能夠存取任意數目的資源的應用程式。

使用 Microsoft 身分識別平臺端點時，您可以忽略 Azure 入口網站中的應用程式註冊資訊中所定義的靜態許可權，並以累加方式要求許可權，這表示在客戶使用其他應用程式功能時，事先要求一組最少的許可權，並隨時間增加。 若要這樣做，您隨時可以在要求存取權杖時於 `scope` 參數中納入新的範圍，以指定您應用程式所需的範圍，而不需要在應用程式註冊資訊中預先定義它們。 如果使用者尚未同意新增至要求的新範圍，則系統只會提示他們同意新的使用權限。 若要深入了解，請參閱[使用權限、同意和範圍](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

允許應用程式透過 `scope` 參數動態要求權限，讓開發人員可以完全掌控使用者的體驗。 您也可以選擇將您的同意體驗提前，並在一個初始授權要求中要求所有使用權限。 如果您的應用程式需要大量的使用權限，您可以在他們嘗試使用應用程式的某些功能時，以累加的方式逐漸向使用者收集這些使用權限。

代表組織進行的管理員同意，仍然需要為應用程式註冊靜態使用權限，因此如果您需要系統管理員代表整個組織提供同意，您應在應用程式註冊入口網站中，設定應用程式的那些使用權限。 這可減少組織系統管理員設定應用程式所需的週期。

## <a name="scopes-not-resources"></a>範圍，而非資源

若為使用 v1.0 端點的應用程式，應用程式能以**資源**或權杖收件者的方式運作。 資源可以定義它所了解的許多**範圍**或 **oAuth2Permissions**，讓用戶端應用程式得以針對特定一組範圍向該資源要求權杖。 請將 Microsoft Graph API 視為資源的範例：

* 資源識別碼，或 `AppID URI`：`https://graph.microsoft.com/`
* 範圍，或 `oAuth2Permissions`：`Directory.Read`、`Directory.Write` 等。

這適用于 Microsoft 身分識別平臺端點。 應用程式仍可作為資源、定義範圍並依據 URI 識別。 用戶端應用程式仍可要求存取這些範圍。 不過，用戶端要求這些許可權的方式已經變更。

對於 v1.0 端點，Azure AD 的 OAuth 2.0 授權要求可能如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

其中 **resource** 參數指出用戶端應用程式要求授權的資源。 Azure AD 根據 Azure 入口網站中的靜態設定計算應用程式所需的權限，並據以發出權杖。

針對使用 Microsoft 身分識別平臺端點的應用程式，相同的 OAuth 2.0 授權要求如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

其中 **scope** 參數指出用戶端應用程式要求授權的資源和使用權限。 所需的資源仍存在於要求中，它包含在 scope 參數的每個值中。 以這種方式使用範圍參數，可讓 Microsoft 身分識別平臺端點比 OAuth 2.0 規格更符合規範，並與常見的產業實務更緊密地對齊。 此外，它也可讓應用程式在應用程式需要時，才需要要求許可權，而不需要[事先處理。](#incremental-and-dynamic-consent)

## <a name="well-known-scopes"></a>知名的範圍

### <a name="offline-access"></a>離線存取

使用 Microsoft 身分識別平臺端點的應用程式可能需要針對應用程式使用新的知名許可權- `offline_access` 範圍。 如果應用程式需要長期代表使用者存取資源，則所有應用程式都需要要求此權限，即使使用者可能不會主動使用此應用程式亦然。 在同意對話方塊中， `offline_access` 範圍會對使用者顯示為 [隨時存取您的資料]****，而使用者必須加以同意。 要求 `offline_access` 許可權可讓您的 web 應用程式從 Microsoft 身分識別平臺端點接收 OAuth 2.0 refresh_tokens。 重新整理權杖屬於長效權杖，可用來交換新的 OAuth 2.0 存取權杖以延長存取期間。

如果您的應用程式未要求 `offline_access` 範圍，則不會收到重新整理權杖。 這表示當您在 OAuth 2.0 授權碼流程中兌換授權碼時，只會從 `/token` 端點接收一個存取權杖。 該存取權杖會短時間維持有效 (通常是一小時)，但最後終將過期。 屆時，您的應用程式必須將使用者重新導向回到 `/authorize` 端點以擷取新的授權碼。 在此重新導向期間，視應用程式的類型而定，使用者或許不需要再次輸入其認證或重新同意使用權限。

若要深入瞭解 OAuth 2.0、 `refresh_tokens` 和 `access_tokens` ，請參閱 Microsoft 身分 [識別平臺通訊協定參考](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

### <a name="openid-profile-and-email"></a>OpenID、設定檔和電子郵件

在過去，使用 Microsoft 身分識別平臺的最基本 OpenID Connect 登入流程，會在產生的 *id_token*中提供大量的使用者相關資訊。 id_token 中的宣告可以包含使用者的名稱、慣用使用者名稱、電子郵件地址、物件識別碼等等。

現在會限制 `openid` 範圍可提供應用程式存取權的資訊。 `openid` 範圍只允許您的應用程式將使用者登入，並接收使用者的應用程式特定識別碼。 如果您想要取得有關應用程式中使用者的個人資料，您的應用程式便需要向使用者要求其他使用權限。 兩個新範圍 (`email` 和 `profile`) 可讓您要求其他使用權限。

* `email`範圍可讓您的應用程式透過 id_token 中的宣告存取使用者的主要電子郵件地址 `email` ，假設使用者有可定址的電子郵件地址。
* `profile`範圍可讓您的應用程式存取使用者的所有其他基本資訊，例如其名稱、慣用的使用者名稱、物件識別碼等等，在 id_token 中。

這可讓您以最低洩漏的方式撰寫應用程式，所以您只可以向使用者要求應用程式執行其作業所需的資訊集。 如需這些範圍的詳細資訊，請參閱 [Microsoft 身分識別平臺範圍參考](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

## <a name="token-claims"></a>權杖宣告

Microsoft 身分識別平臺端點預設會在其權杖中發出一組較小的宣告，以保持較小的承載。 如果您的應用程式和服務相依于 v1.0 權杖中的特定宣告，而該宣告在 Microsoft 身分識別平臺權杖中預設為不提供，請考慮使用 [選擇性宣告](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 功能來包含該宣告。

> [!IMPORTANT]
> v1.0 和 v2.0 權杖都可以由 v1.0 和 v2.0 端點發出！ id_tokens *永遠* 符合要求的端點，而且存取權杖 *一律* 符合用戶端將使用該權杖呼叫的 Web API 所預期的格式。  因此，如果您的應用程式使用 v2.0 端點來取得權杖，以呼叫 Microsoft Graph （預期是 v1.0 格式存取權杖），您的應用程式將會收到 v1.0 格式的權杖。

## <a name="limitations"></a>限制

使用 Microsoft 身分識別平臺時，有一些限制需要注意。

當您建立與 Microsoft 身分識別平臺整合的應用程式時，您必須決定 Microsoft 身分識別平臺端點和驗證通訊協定是否符合您的需求。 V1.0 端點和平臺仍受到完整的支援，而且在某些方面比 Microsoft 身分識別平臺的功能更豐富。 不過，Microsoft 身分識別平臺為開發人員 [帶來了很大的好處](azure-ad-endpoint-comparison.md) 。

以下是為開發人員提供的簡化建議：

* 如果您想要或需要在您的應用程式中支援個人 Microsoft 帳戶，或您正在撰寫新的應用程式，請使用 Microsoft 身分識別平臺。 但在這樣做之前，請務必了解此文章中討論的限制。
* 如果您要遷移或更新依賴 SAML 的應用程式，則無法使用 Microsoft 身分識別平臺。 相反地，請參閱 [Azure AD 1.0 版指南](v1-overview.md)。

Microsoft 身分識別平臺端點將會進化，以排除此處所列的限制，因此您只需要使用 Microsoft 身分識別平臺端點。 在此同時，請使用本文來判斷 Microsoft 身分識別平臺端點是否適合您。 我們將持續更新本文，以反映 Microsoft 身分識別平臺端點的目前狀態。 回頭檢查以針對 Microsoft 身分識別平臺功能重新評估您的需求。

### <a name="restrictions-on-app-registrations"></a>應用程式註冊的限制

針對您想要與 Microsoft 身分識別平臺端點整合的每個應用程式，您可以在 Azure 入口網站的新 [**應用程式註冊** 體驗](https://aka.ms/appregistrations) 中建立應用程式註冊。 現有的 Microsoft 帳戶應用程式與入口網站不相容，但所有 Azure AD 應用程式都是，不論它們在何處或何時註冊。

支援公司和學校帳戶及個人帳戶的應用程式註冊有下列注意事項：

* 每個應用程式識別碼只允許兩個應用程式密碼。
* 沒有在租用戶中註冊的應用程式，只能由註冊該應用程式的帳戶管理。 它無法與其他開發人員共用。 在應用程式註冊入口網中使用個人 Microsoft 帳戶註冊的大部分應用程式都是此情況。 如果您想要與多個開發人員共用您的應用程式註冊，請使用 Azure 入口網站的新 **應用程式註冊** 區段，在租使用者中註冊應用程式。
* 所允許的重新導向 URL 的格式有幾項限制。 如需有關重新導向 URL 的詳細資訊，請參閱下一節。

### <a name="restrictions-on-redirect-urls"></a>重新導向 URL 的限制

如需有關為 Microsoft 身分識別平臺註冊的應用程式重新導向 Url 限制的最新資訊，請參閱 Microsoft 身分識別平臺檔中的重新 [導向 URI/回復 URL 限制和限制](../develop/reply-url.md) 。

若要瞭解如何註冊應用程式以與 Microsoft 身分識別平臺搭配使用，請參閱 [使用新的應用程式註冊體驗來註冊應用程式](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

### <a name="restrictions-on-libraries-and-sdks"></a>程式庫和 SDK 的限制

目前，Microsoft 身分識別平臺端點的程式庫支援受限。 如果您想要在實際執行應用程式中使用 Microsoft 身分識別平臺端點，您有下列選項：

* 如果您要建立 web 應用程式，您可以安全地使用正式運作的伺服器端中介軟體來進行登入和權杖驗證。 這些包括了適用於 ASP.NET 的 OWIN OpenID Connect 中介軟體和 Node.js Passport 外掛程式。 如需使用 Microsoft 中介軟體的程式碼範例，請參閱 [microsoft 身分識別平臺入門](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) 一節。
* 如果您要建立桌面或行動應用程式，可以使用其中一個 Microsoft 驗證程式庫 (MSAL) 。 這些程式庫已正式運作，或在生產環境支援的預覽中，因此可安全地在生產應用程式中使用。 您可以在[驗證程式庫參考](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)中，閱讀更多和預覽條款與可用程式庫相關的詳細資訊。
* 針對 Microsoft 程式庫未涵蓋的平臺，您可以直接在應用程式程式碼中傳送和接收通訊協定訊息，以與 Microsoft 身分識別平臺端點整合。 OpenID Connect 和 OAuth 通訊協定 [會明確記載](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) ，以協助您進行這類整合。
* 最後，您可以使用開放原始碼 OpenID Connect 和 OAuth 程式庫來與 Microsoft 身分識別平臺端點整合。 Microsoft 身分識別平臺端點應該與許多開放原始碼通訊協定程式庫相容，而不需要變更。 這類的程式庫的可用性會依語言和平台而有所不同。 [OpenID Connect](https://openid.net/connect/) \(英文\) 和 [OAuth 2.0](https://oauth.net/2/) \(英文\) 網站保有一份常用的實作清單。 如需詳細資訊，請參閱 [microsoft 身分識別平臺和驗證程式庫](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)，以及已使用 microsoft 身分識別平臺端點測試的開放原始碼用戶端程式庫和範例的清單。
* 如需參考， `.well-known` Microsoft 身分識別平臺通用端點的端點是 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` 。 使用您的租用戶識別碼來取代 `common`，以取得您租用戶特有的資料。

### <a name="protocol-changes"></a>通訊協定變更

Microsoft 身分識別平臺端點不支援 SAML 或 WS-同盟;它僅支援 OpenID Connect 和 OAuth 2.0。  對於 1.0 版端點 OAuth 2.0 通訊協定的重大變更為：

* 如果設定選擇性宣告**或**在要求中指定 scope=email，則會傳回 `email` 宣告。
* 現已支援代替 `resource` 參數的 `scope` 參數。
* 已修改許多回應，使它們更符合 OAuth 2.0 的規範，例如正確地將 `expires_in` 傳回為 Int 而不是字串。

若要進一步瞭解 Microsoft 身分識別平臺端點所支援的通訊協定功能範圍，請參閱 [OpenID Connect 和 OAuth 2.0 通訊協定參考](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

#### <a name="saml-usage"></a>SAML 使用方式

如果您已在 Windows 應用程式中使用 Active Directory 驗證程式庫 (ADAL) ，您可能已利用 Windows 整合式驗證，它會使用安全性聲明標記語言 (SAML) 判斷提示授與。 有了此授與之後，同盟 Azure AD 租用戶的使用者便可向其內部部署 Active Directory 執行個體以無訊息方式進行驗證，而不需輸入認證。 雖然 [SAML 仍是支援的通訊協定](../develop/active-directory-saml-protocol-reference.md) ，可與企業使用者搭配使用，v2.0 端點只適用于 OAuth 2.0 應用程式。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請 [參閱 Microsoft 身分識別平臺檔](../develop/index.yml)。
