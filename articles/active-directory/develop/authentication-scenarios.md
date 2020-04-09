---
title: Microsoft 身分識別平台中的驗證 | Azure
description: 瞭解 Microsoft 標識平臺 (v2.0) 中的身份驗證基礎知識。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: e78f822a88b093992f065a509c2250e6a5c0dec2
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885560"
---
# <a name="authentication-basics"></a>驗證基本概念

## <a name="what-is-authentication"></a>什麼是認證

本文介紹了創建受保護的 Web 應用、Web API 或調用受保護 Web API 的應用需要瞭解的許多身份驗證概念。 如果您看到一個不熟悉的術語,請嘗試我們的[詞彙表](developer-glossary.md)或我們的[Microsoft 身份平台視頻](identity-videos.md),這些視頻涵蓋了基本概念。

**身份驗證**是證明您是您所說的身份的過程。 驗證 (Authentication) 有時會被簡稱為 AuthN。

**授權**是授予經過身份驗證的一方做某事的許可權的行為。 它指定允許您存取哪些資料以及您可以對這些資料執行哪些操作。 授權 (Authorization) 有時會被簡稱為 AuthZ。

應用可以將該責任委派給集中式標識供應商,而不是創建每個應用都維護自己的使用者名和密碼資訊的應用,而這些應用在需要跨多個應用添加或刪除使用者時會承擔很高的管理負擔。

Azure 活動目錄 (Azure AD) 是雲端中的集中式識別提供者。 將身份驗證和授權委派給它,可以執行條件訪問策略(要求使用者位於特定位置的條件存取策略、使用多重身份驗證,以及允許使用者登錄一次,然後自動登錄到共用同一集中目錄的所有 Web 應用)。 此功能稱為單點打開 (SSO)。

對於使用者遍佈全球、不一定從企業網路登錄的應用而言,集中式標識提供程式更為重要。 Azure AD 對使用者進行身份驗證並提供訪問權杖。 [訪問權杖](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token)是由授權伺服器頒發的安全權杖。 它包含有關使用者和令牌用途的應用的資訊;可用於訪問 Web API 和其他受保護資源。

Microsoft 身份平台透過提供身份即服務來簡化應用程式開發人員的身份驗證,支援產業標準協定(如[OAuth 2.0](https://oauth.net/2/)和[OpenID Connect),](https://openid.net/connect/)以及針對不同平臺的開源庫,以説明您快速開始編碼。 它允許開發人員構建登錄所有 Microsoft 標識的應用程式,獲取令牌來調用[Microsoft Graph、](https://developer.microsoft.com/graph/)其他 Microsoft API 或開發人員已經構建的 API。 有關詳細資訊,請參閱[Microsoft 標識平台的演進](about-microsoft-identity-platform.md)。

### <a name="tenants"></a>租用戶

雲標識提供者為許多組織提供服務。 為了將不同組織的使用者分開,Azure AD 被分區為租戶,每個組織有一個租戶。

租戶跟蹤使用者及其關聯的應用。 Microsoft 識別平台還支援使用個人 Microsoft 帳戶登錄的使用者。

Azure AD 還提供 Azure 活動目錄 B2C,以便組織可以使用 Google 帳戶等社交身份登錄使用者(通常是客戶)。 關於詳細資訊,請參閱[Azure 活動目錄 B2C 文件](https://docs.microsoft.com/azure/active-directory-b2c)。

### <a name="security-tokens"></a>安全性權杖

安全令牌包含有關用戶和應用的資訊。 Azure AD 使用包含聲明的基於 JSON 的權杖 (JWT)。

聲明向另一個實體(如[資源伺服器](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server))提供有關一個實體(如[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)或[資源擁有者](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner))的斷言。

聲明是中繼有關權杖主題的事實的名稱/值對。 例如,聲明可能包含有關[由授權伺服器](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server)進行身份驗證的安全主體的事實。 給定權杖中存在的聲明取決於許多內容,包括權杖類型、用於驗證主題的憑據類型、應用程式配置等。

應用程式可以將聲明用於各種任務,例如:

* 驗證權杖
* 標識權杖主體的租戶
* 顯示使用者資訊
* 確定主體的授權

宣告由提供以下資訊的鍵值對組成:

* 產生權杖的安全權杖伺服器
* 產生權杖日期
* 主題(如使用者 -守護行程除外)
* 存取群體,這是為其產生權杖的應用程式
* 請求權杖的應用(用戶端)。 在 Web 應用中,這可能與受眾相同

關於詳細資訊的聲明資訊,請參考[權限存取權杖](access-tokens.md)與 ID[權限 。](id-tokens.md)

它由生成權杖的應用、登錄使用者的 Web 應用或正在調用的 Web API 來驗證權杖。 令牌由安全權杖伺服器 (STS) 使用私鑰簽名。 STS 發佈相應的公開金鑰。 要驗證權杖,應用使用STS公鑰驗證簽名,以驗證簽名是否使用私鑰創建。

令牌僅在有限的時間內有效。 通常 STS 提供一對權杖:存取應用程式或受保護資源的訪問權杖,以及造訪權杖接近過期時用於刷新訪問權杖的刷新權杖。

訪問權杖作為`Authorization`標頭中的承載權杖傳遞到 Web API。 應用可以為 STS 提供刷新權杖,如果使用者對應用的訪問未被吊銷,它將返回新的訪問權杖和新的刷新權杖。 這是如何處理某人離開企業的情況。 當 STS 收到刷新權杖時,如果使用者不再獲得授權,它將不會頒發另一個有效的訪問權杖。

### <a name="how-each-flow-emits-tokens-and-codes"></a>每個流如何發出權杖和代碼

根據用戶端的建構方式,它可以使用 Azure AD 支援的身份驗證流的一個(或多個)。 這些流可以生成各種權杖(id_tokens、刷新權杖、存取權杖)以及授權代碼,並且需要不同的權杖才能使其正常工作。 此圖表提供概述:

|Flow | 需要 | id_token | 存取權杖 | 重新載入權杖 | 授權碼 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授權代碼流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[隱含流程](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 串流](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[刷新權杖兌換](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 重新載入權杖 | x | x | x| |
|[代理者流程](v2-oauth2-on-behalf-of-flow.md) | 存取權杖| x| x| x| |
|[用戶端認證](v2-oauth2-client-creds-grant-flow.md) | | | x(僅限套用)| | |

通過隱式模式發出的權杖由於透過 URL(`response_mode``query``fragment`或) 傳回瀏覽器,因此存在長度限制。  某些瀏覽器對 URL 的大小有限制,可以放入瀏覽器列中,如果時間過長,則失敗。  因此,這些令牌沒有`groups`或`wids`聲明。 

現在,您已經瞭解了基礎知識,請繼續閱讀以了解標識應用模型和 API,瞭解預配在 Azure AD 中的工作原理,並獲取有關 Azure AD 支援的常見方案的詳細資訊的連結。

## <a name="application-model"></a>應用程式模型

應用程式可以自行登錄使用者或將登錄委託給標識提供程式。 請參閱[身份驗證流和應用方案](authentication-flows-app-scenarios.md),以瞭解 Azure AD 支援的登錄方案。

對於標識提供程式知道使用者有權訪問特定應用,使用者和應用程式都必須向標識提供程式註冊。 在 Azure AD 註冊應用程式時,為應用程式提供標識配置,允許它與 Azure AD 整合。 註冊應用程式還允許您:

* 在登錄對話框中自定義應用程式的品牌。 這一點很重要,因為這是使用者首次體驗你的應用。
* 決定是否允許使用者僅在使用者屬於您的組織時才登錄。 這是一個租戶應用程式。 或者允許使用者使用任何工作或學校帳戶登錄。 這是一個多租戶應用程式。 您還可以允許個人 Microsoft 帳戶,或來自LinkedIn、Google 等的社交帳戶。
* 請求作用域許可權。 例如,您可以請求"使用者.read"範圍,該範圍授予讀取登錄使用者的配置檔的許可權。
* 定義定義對 Web API 的訪問的範圍。 通常,當應用想要訪問 API 時,需要請求對所定義的作用域的許可權。
* 與 Azure AD 共享機密,該機密向 Azure AD 證明應用的身份。  這在應用是機密客戶端應用程式的情況下相關。 機密用戶端應用程式是一個可以安全地保存憑據的應用程式。 它們需要受信任的後端伺服器來存儲憑據。

註冊後,應用程式將獲得一個唯一標識符,該標識符是應用在請求令牌時與 Azure AD 共用的。 如果應用程式是[機密用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application),它也將共用機密或公鑰*-取決於是否使用了證書或機密。

Microsoft 識別平臺使用滿足兩個主要功能的模型表示應用程式:

* 依其支援的身份驗證協定識別應用
* 提供認證所需的識別碼、URL、機密和相關資訊

微軟身份平臺:

* 儲存執行時支援認證的資料
* 保存所有資料,以決定應用可能需要存取哪些資源,以及應在什麼情況下滿足給定請求
* 提供用於在應用程式開發人員的租戶中實現應用預配以及任何其他 Azure AD 租戶的基礎結構
* 在權杖請求期間處理使用者同意,並方便跨租戶動態預配應用

同意是資源擁有者授權用戶端應用程式代表資源擁有者在特定許可權下訪問受保護資源的過程。 微軟身份平臺:

* 讓使用者與系統管理員能夠針對應用程式是否能代表他們存取資源，動態地授與或拒絕同意。
* 授與管理員決定允許應用程式執行哪些作業、有哪些使用者可以使用特定應用程式，以及目錄資源之存取方式的最終決定權。

在 Microsoft 識別平臺中,[應用程式物件](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object)描述應用程式。 在部署時,Microsoft 標識平臺使用應用程式物件作為藍圖來創建[服務主體](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object),它表示目錄或租戶中應用程式的具體實例。 服務主體定義應用在特定目標目錄中的實際可以執行哪些操作、誰可以使用它、它有權訪問哪些資源,等等。 Microsoft 標識平台通過**同意**從應用程式物件創建服務主體。

下圖顯示由同意驅動的簡化 Microsoft 身分識別平台佈建流程。 它顯示兩個租戶:A 和B. 租戶A擁有應用程式。 租戶 B 正在通過服務主體實例化應用程式。  

![由同意驅動的簡化佈建流程](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

在這個佈建流程中：

1. 租用戶 B 的使用者嘗試透過應用程式登入，授權端點會要求應用程式提供權杖。
1. 獲取並驗證使用者憑據以進行身份驗證。
1. 系統會提示使用者為應用提供訪問租戶 B 的同意。
1. Microsoft 標識平臺使用租戶 A 中的應用程式物件作為在租戶 B 中創建服務主體的藍圖。
1. 使用者收到請求的權杖。

您可以為其他租戶重複此過程。 租用戶 A 會保留應用程式的藍圖 (應用程式物件)。 獲得應用同意的所有其他租戶的使用者和管理員通過每個租戶中的相應服務主體物件控制允許應用程式執行哪些操作。 如需詳細資訊，請參閱 [Microsoft 身分識別平台中的應用程式和服務主體物件](app-objects-and-service-principals.md)。

## <a name="web-app-sign-in-flow-with-azure-ad"></a>使用 Azure AD 的 Web 應用登入串流

當使用者在瀏覽器中導航到 Web 應用時,將發生以下情況:

* Web 應用確定使用者是否經過身份驗證。
* 如果使用者未經過身份驗證,則 Web 應用將委派到 Azure AD 以登錄使用者。 該登錄將符合組織的策略,這可能意味著要求使用者輸入其憑據、使用多重身份驗證或根本不使用密碼(例如使用 Windows Hello)。
* 要求使用者同意用戶端應用所需的訪問許可權。 這就是為什麼客戶端應用需要在 Azure AD 中註冊,以便 Azure AD 可以傳遞表示使用者已同意的訪問的權杖。

當使用者成功進行身份驗證時:

* Azure AD 向 Web 應用發送權杖。
* 將保存與 Azure AD 網域關聯的 Cookie,其中包含瀏覽器 Cookie jar 中使用者的身份。 下次應用使用瀏覽器導航到 Azure AD 授權終結點時,瀏覽器將呈現 Cookie,以便使用者不必再次登錄。 這也是實現 SSO 的方式。 Cookie 由 Azure AD 生成,只能由 Azure AD 理解。
* 然後,Web 應用驗證權杖。 如果驗證成功,Web 應用將顯示受保護的頁面,並將會話 Cookie 保存在瀏覽器的 Cookie 罐中。 當用戶導航到另一個頁面時,Web 應用知道使用者已根據會話 Cookie 進行身份驗證。

以下序列圖總結了此互動:

![Web 應用程式驗證程序](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web 應用程式如何確定使用者是否經過認證

Web 應用開發人員可以指示所有頁面或僅需要身份驗證。 例如,在ASP.NET/ASP.NET核心中,這是通過將`[Authorize]`屬性添加到控制器操作來實現的。 

此屬性導致ASP.NET檢查是否存在包含用戶標識的會話 Cookie。 如果 Cookie 不存在,ASP.NET 將身份驗證重定向到指定的標識提供者。 如果識別提供者是 Azure AD,則 Web`https://login.microsoftonline.com`應用程式會將驗證重定向到,該身份驗證將顯示登入對話框。

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Web 應用程式如何將登入委託 Azure AD 並取得權杖

使用者身份驗證通過瀏覽器進行。 OpenID 協定使用標準 HTTP 協定訊息。
* Web 應用向瀏覽器發送 HTTP 302(重定向)以使用 Azure AD。
* 使用者經過身份驗證後,Azure AD 將使用瀏覽器重定向將令牌發送到 Web 應用。
* 重定向由 Web 應用以重定向 URI 的形式提供。 此重定向 URI 註冊到 Azure AD 應用程式物件。 可以有多個重定向URI,因為應用程式可能部署在多個 URL 中。 因此,Web 應用還需要指定要使用的重定向 URI。
* Azure AD 驗證 Web 應用發送的重定向 URI 是應用的已註冊重定向 URI 之一。

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>使用 Azure AD 的桌面和行動應用程式登入串流

上述流程適用於桌面和移動應用程式,但略有不同。

桌面和移動應用程式可以使用嵌入式 Web 控制器或系統瀏覽器進行身分驗證。 下圖顯示了桌面或移動應用如何使用 Microsoft 身份驗證庫 (MSAL) 獲取存取權杖和呼叫 Web API。

![桌面應用程式如何看起來](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 使用瀏覽器獲取權杖。 與 Web 應用一樣,身份驗證將委派給 Azure AD。

由於 Azure AD 在瀏覽器中保存與 Web 應用相同的標識 Cookie,因此如果本機應用或行動應用使用系統瀏覽器,它將立即使用相應的 Web 應用獲取 SSO。

預設情況下,MSAL 使用系統瀏覽器。 例外情況是 .NET Framework 桌面應用程式,其中嵌入式控制項用於提供更整合的使用者體驗。

## <a name="next-steps"></a>後續步驟

* 請參閱[Microsoft 標識平台開發人員術語錶](developer-glossary.md),以熟悉常用術語。
* 請參閱[身份驗證流和應用方案](authentication-flows-app-scenarios.md),瞭解有關 Microsoft 標識平台支援的其他方案,以驗證使用者。
* 請參閱[MSAL 庫](msal-overview.md),瞭解 Microsoft 庫,這些庫可説明您在單個簡化的程式設計模型中開發與 Microsoft 帳戶、Azure AD 帳戶和 Azure AD B2C 使用者一起工作的應用程式。
* 請參閱[將應用服務與 Microsoft 標識平臺集成](/azure/app-service/configure-authentication-provider-aad),瞭解如何為應用服務應用配置身份驗證。
