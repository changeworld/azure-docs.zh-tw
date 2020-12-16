---
title: Microsoft 身分識別平臺的最佳作法 |蔚藍
description: 瞭解與 Microsoft 身分識別平臺整合時的最佳作法、建議和一般 oversights。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 47c3c091e767cac63bb814469397ef1a0aeca3a4
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516500"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft 身分識別平台最佳做法和建議

本文將重點說明與 Microsoft 身分識別平臺整合時的最佳作法、建議和一般 oversights。  這份檢查清單會引導您進行高品質且安全的整合。 定期檢查這份清單，以確保您保有應用程式與身分識別平臺整合的品質和安全性。 檢查清單不適合用來檢查您的整個應用程式。 當我們對平臺進行改善時，檢查清單的內容可能會變更。

如果您剛開始使用，請參閱 Microsoft 身分 [識別平臺檔](index.yml) ，瞭解有關驗證的基本概念、microsoft 身分識別平臺中的應用程式案例等等。

使用下列檢查清單，以確保您的應用程式能夠有效地與 [Microsoft 身分識別平臺](./index.yml)整合。

> [!TIP]
> Azure 入口網站中的 [ *整合助理* ] 可協助您套用許多這些最佳作法和建議。 在 Azure 入口網站中選取您的任何 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ，然後選取 [ **整合助理** ] 功能表項目以開始使用助理。

## <a name="basics"></a>基本

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) ：閱讀並瞭解 [Microsoft 平臺原則](/legal/microsoft-identity-platform/terms-of-use)。 確定您的應用程式符合所述的條款，因為它們是設計來保護使用者和平臺。

## <a name="ownership"></a>擁有權

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 請確定與您用來註冊及管理應用程式的帳戶相關聯的資訊是最新的。

## <a name="branding"></a>商標

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) 符合 [應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) ：為您的應用程式提供有意義的名稱和標誌。 此資訊會出現在 [應用程式的同意提示](application-consent-experience.md)中。 確定您的名稱和標誌都代表您的公司/產品，讓使用者能夠做出明智的決策。 確定您未違反任何商標。

## <a name="privacy"></a>隱私權

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 提供應用程式的服務條款和隱私權聲明的連結。

## <a name="security"></a>安全性

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) 管理您的重新導向 uri： <ul><li>維護所有重新導向 Uri 的擁有權，並將這些 Uri 的 DNS 記錄維持在最新狀態。</li><li>請勿在您的 Uri 中使用萬用字元 ( * ) 。</li><li>針對 web 應用程式，請確定所有 Uri 都是安全和加密 (例如，使用 HTTPs 配置) 。</li><li>若為公用用戶端，請使用平臺特定的重新導向 Uri （如果適用 (主要適用于 iOS 和 Android) ）。 否則，請使用具有大量隨機性的重新導向 Uri，以防止回呼至您的應用程式時發生衝突。</li><li>如果您的應用程式是從隔離的 web 代理程式使用，您可以使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 。</li><li>定期檢查並修剪所有未使用或不必要的重新導向 Uri。</li></ul>

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 如果您的應用程式已在目錄中註冊，請將應用程式註冊擁有者的清單降至最低並手動監視。

![](./media/active-directory-integration-checklist/checkbox-two.svg)除非明確要求，否則核取方塊不會啟用[OAuth2 隱含授與流程](v2-oauth2-implicit-grant-flow.md)的支援。 您將在 [這裡](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)瞭解有效的案例。

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) 的移動超過使用者名稱/密碼。 請勿使用 [資源擁有者密碼認證流程 (ROPC) ](v2-oauth-ropc.md)，直接處理使用者的密碼。 此流程需要高度信任和使用者暴露程度，而且只應在其他更安全的流程無法使用時使用。 在某些情節中，仍然需要此流程 (例如 DevOps)，但請注意，使用它會對您的應用程式施加限制式。  針對更新式的方法，請閱讀 [驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。

![核取方塊可 ](./media/active-directory-integration-checklist/checkbox-two.svg) 保護和管理 web 應用程式、Web api 和 daemon apps 的機密應用程式認證。 使用 [憑證認證，](active-directory-certificate-credentials.md)而不是 (用戶端密碼) 的密碼認證。 如果您必須使用密碼認證，請不要手動設定它。 請勿將認證儲存在程式碼或設定中，也不允許人類處理。 可能的話，請使用 [適用于 Azure 資源的受控](../managed-identities-azure-resources/overview.md) 識別或 [Azure Key Vault](../../key-vault/general/basic-concepts.md) 來儲存和定期輪替您的認證。

![核取方塊可 ](./media/active-directory-integration-checklist/checkbox-two.svg) 確保您的應用程式會要求最低許可權。 只要求您的應用程式絕對需要的許可權，而且只有在需要時才需要。 瞭解不同 [類型的許可權](v2-permissions-and-consent.md#permission-types)。 只有在必要時才使用應用程式許可權;可能的話，請使用委派的許可權。 如需 Microsoft Graph 許可權的完整清單，請參閱此 [許可權參考](/graph/permissions-reference)。

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 如果您要使用 Microsoft 身分識別平臺保護 API，請仔細思考其公開的許可權。 請考慮您解決方案的正確細微度，以及 (s) 需要系統管理員同意的許可權。 進行任何授權決策之前，請先檢查傳入權杖中是否有預期的許可權。

## <a name="implementation"></a>實作

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) 使用新式驗證解決方案 (OAuth 2.0， [OpenID Connect](v2-protocols-oidc.md)) 安全地登入使用者。

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) 不會直接對通訊協定（例如 OAuth 2.0 和 OPEN ID）進行程式設計。 相反地，請利用 [Microsoft 驗證程式庫 (MSAL) ](msal-overview.md)。 MSAL 程式庫會安全地將安全性通訊協定包裝在容易使用的程式庫中，您可以取得條件式 [存取](../conditional-access/overview.md) 案例的內建支援、 [ (SSO) ](../manage-apps/what-is-single-sign-on.md)的全裝置單一登入，以及內建的權杖快取支援。 如需詳細資訊，請參閱 Microsoft 支援的 [用戶端程式庫](reference-v2-libraries.md#microsoft-supported-client-libraries) 和 [中介軟體程式庫](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) 清單，以及 [相容的協力廠商用戶端程式庫](reference-v2-libraries.md#compatible-client-libraries)清單。<br/><br/>如果您必須為驗證通訊協定編寫程式碼，您應該遵循 [MICROSOFT SDL](https://www.microsoft.com/sdl/default.aspx)這類方法。 請密切注意每個通訊協定的標準規格中的安全性考慮。

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) ，將現有的應用程式從 [Azure Active Directory authentication LIBRARY (ADAL) ](../azuread-dev/active-directory-authentication-libraries.md) 遷移至 [Microsoft 驗證程式庫](msal-overview.md)。 MSAL 是 Microsoft 最新的身分識別平臺解決方案，而且是 ADAL 慣用的解決方案。 它適用于 .NET、JavaScript、Android、iOS、macOS，也適用于 Python 和 JAVA 的公開預覽。 深入瞭解如何遷移 [ADAL.NET](msal-net-migration.md)、 [ADAL.js](msal-compare-msal-js-and-adal-js.md)和 [ADAL.NET 和 iOS broker](msal-net-migration-ios-broker.md) 應用程式。

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 針對 mobile apps，請使用應用程式註冊體驗來設定每個平臺。 為了讓您的應用程式能夠利用 Microsoft Authenticator 或 Microsoft 公司入口網站進行單一登入，您的應用程式需要設定「broker 重新導向 URI」。 這可讓 Microsoft 在驗證之後將控制權交還給您的應用程式。 設定每個平臺時，應用程式註冊體驗將引導您完成此程式。 使用快速入門下載可運作的範例。 在 iOS 上，請盡可能使用訊息代理程式和系統 web 程式。

![](./media/active-directory-integration-checklist/checkbox-two.svg)在 web 應用程式或 Web api 中的核取方塊，每個帳戶保留一個權杖快取。  針對 web 應用程式，權杖快取應以帳戶識別碼作為索引鍵。  針對 web Api，帳戶應該以用來呼叫 API 的權杖雜湊作為索引鍵。 MSAL.NET 會在 .NET Framework 和 .NET Core subplatforms 中提供自訂權杖快取序列化。 基於安全性和效能的考慮，建議您為每個使用者序列化一個快取。 如需詳細資訊，請參閱權杖快取 [序列化](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)。

![](./media/active-directory-integration-checklist/checkbox-two.svg)如果您的應用程式所需的資料可透過[Microsoft Graph](https://developer.microsoft.com/graph)使用，請勾選此核取方塊。使用 Microsoft Graph 端點（而不是個別 API）要求此資料的許可權。

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 請勿查看存取權杖的值，或嘗試將它剖析為用戶端。  他們可以變更值、格式，甚至在沒有警告的情況下進行加密-如果您的用戶端需要瞭解使用者的相關資訊，請一律使用 id_token，或呼叫 Microsoft Graph。  只有 web Api 可以剖析 (的存取權杖，因為它們是定義格式及設定加密金鑰) 的權杖。

## <a name="end-user-experience"></a>使用者體驗

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) 可 [瞭解同意體驗](application-consent-experience.md) ，並設定應用程式的同意提示，讓終端使用者和系統管理員有足夠的資訊來判斷他們是否信任您的應用程式。

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 藉由嘗試無訊息驗證 (在互動式流程之前) 取得無訊息的權杖取得，將使用者在使用您的應用程式時必須輸入登入認證的次數降到最低。

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 請勿在每次登入時使用「提示 = 同意」。 如果您已確定需要要求其他許可權的同意，請只使用 prompt = 同意 (例如，如果您已變更應用程式的必要許可權) 。

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) （如果適用的話），利用使用者資料豐富您的應用程式。 使用 [MICROSOFT GRAPH API](https://developer.microsoft.com/graph) 是一個簡單的方法。 可協助您開始使用的 [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 工具。

![核取方塊 ](./media/active-directory-integration-checklist/checkbox-two.svg) 可註冊您的應用程式所需的完整許可權集，讓系統管理員可以輕鬆地將同意授與其租使用者。 在執行時間使用累加 [式同意](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) ，以協助使用者瞭解您的應用程式在第一次啟動時，要求可能會關注或混淆使用者的許可權。

![核取方塊會 ](./media/active-directory-integration-checklist/checkbox-two.svg) 執行 [全新的單一登出體驗](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)。 它是一種隱私權和安全性需求，可提供更好的使用者體驗。

## <a name="testing"></a>測試

![](./media/active-directory-integration-checklist/checkbox-two.svg)針對可能會影響使用者使用應用程式能力的[條件式存取原則](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)，進行核取方塊測試。

![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) 使用您打算支援的所有可能帳戶來測試您的應用程式 (例如，公司或學校帳戶、個人 Microsoft 帳戶、子帳戶和主權帳戶) 。

## <a name="additional-resources"></a>其他資源

探索有關 v2.0 的深入資訊：

* [Microsoft 身分識別平臺 (2.0 版總覽) ](v2-overview.md)
* [Microsoft 身分識別平臺通訊協定參考](active-directory-v2-protocols.md)
* [存取權杖參考](access-tokens.md)
* [識別碼權杖參考](id-tokens.md)
* [驗證程式庫參考](reference-v2-libraries.md)
* [Microsoft 身分識別平台中的權限和同意](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
