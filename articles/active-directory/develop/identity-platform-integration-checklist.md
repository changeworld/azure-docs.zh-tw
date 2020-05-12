---
title: Microsoft 身分識別平臺的最佳做法 |Azure
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
ms.openlocfilehash: c7c26780cdba37a2bddc201928dbcbd5f86e0170
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115892"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft 身分識別平臺最佳做法和建議

本文將重點放在與 Microsoft 身分識別平臺整合時的最佳作法、建議和一般 oversights。  此檢查清單會引導您進行高品質且安全的整合。 請定期審查此清單，以確保您可以維持應用程式與身分識別平臺整合的品質和安全性。 檢查清單並不是要用來檢查您的整個應用程式。 當我們對平臺進行改善時，檢查清單的內容可能會有所變更。

如果您剛開始使用，請參閱 Microsoft 身分[識別平臺檔](index.yml)，以瞭解驗證基本概念、microsoft 身分識別平臺中的應用程式案例等等。

使用下列檢查清單，確保您的應用程式與[Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/)有效整合。

> [!TIP]
> Azure 入口網站中的 [*整合助理*] 可協助您套用許多這些最佳作法和建議。 在 Azure 入口網站中選取您的任何[應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)，然後選取 [**整合助理（預覽）** ] 功能表項目以開始使用 [助理]。

## <a name="basics"></a>基本概念

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 閱讀並瞭解[Microsoft 平臺原則](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)。 請確定您的應用程式遵守其設計用來保護使用者和平臺的詞彙。 |

## <a name="ownership"></a>擁有權

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 請確定與您用來註冊及管理應用程式的帳戶相關聯的資訊是最新的。 |

## <a name="branding"></a>Branding

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 遵守[應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 為您的應用程式提供有意義的名稱和標誌。 此資訊會出現在您[應用程式的同意提示](application-consent-experience.md)中。 請確定您的姓名和標誌代表您的公司/產品，讓使用者能夠做出明智的決策。 請確定您不會違反任何商標。 |

## <a name="privacy"></a>隱私權

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 提供您應用程式服務條款和隱私權聲明的連結。 |

## <a name="security"></a>安全性

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 管理您的重新導向 Uri： <ul><li>維護所有重新導向 Uri 的擁有權，並將它們的 DNS 記錄保持在最新狀態。</li><li>請勿在您的 Uri 中使用萬用字元（*）。</li><li>針對 web 應用程式，請確定所有 Uri 都是安全且加密的（例如，使用 HTTPs 架構）。</li><li>若為公用用戶端，請使用平臺特定的重新導向 Uri （主要適用于 iOS 和 Android）。 否則，請使用具有大量隨機性的重新導向 Uri，以避免在回呼至您的應用程式時發生衝突。</li><li>如果您的應用程式是從隔離的 web 代理程式中使用，您可以使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 。</li><li>定期檢查並修剪所有未使用或不必要的重新導向 Uri。</li></ul> |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果您的應用程式已在目錄中註冊，請將應用程式註冊擁有者的清單最小化並手動監視。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 除非明確需要，否則請勿啟用[OAuth2 隱含授與流程](v2-oauth2-implicit-grant-flow.md)的支援。 瞭解[這裡](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)的有效案例。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 移至使用者名稱/密碼之外。 請勿使用[資源擁有者密碼認證流程（ROPC）](v2-oauth-ropc.md)，這會直接處理使用者的密碼。 此流程需要高程度的信任和使用者暴露，而且只有在無法使用其他、更安全的流程時才使用。 在某些情節中，仍然需要此流程 (例如 DevOps)，但請注意，使用它會對您的應用程式施加限制式。  如需更現代化的方法，請參閱[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 保護及管理 web 應用程式、web Api 和 daemon 應用程式的機密應用程式認證。 使用[憑證認證，](active-directory-certificate-credentials.md)而不是密碼認證（用戶端密碼）。 如果您必須使用密碼認證，請勿手動設定。 請勿將認證儲存在程式碼或設定中，而且不允許人類處理。 可能的話，請使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別或[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)來儲存並定期輪替您的認證。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 請確定您的應用程式要求最低許可權許可權。 只會詢問您的應用程式絕對需要的許可權，而且只有在需要時才會要求。 瞭解不同[類型的許可權](v2-permissions-and-consent.md#permission-types)。 只有在必要時才使用應用程式許可權;可能的話，請使用委派的許可權。 如需 Microsoft Graph 許可權的完整清單，請參閱此[許可權參考](https://docs.microsoft.com/graph/permissions-reference)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果您要使用 Microsoft 身分識別平臺來保護 API，請仔細考慮應公開的許可權。 請考慮您的解決方案有哪些正確的資料細微性，以及哪些許可權需要系統管理員同意。 在進行任何授權決策之前，請先檢查連入權杖中是否有預期的許可權。 |

## <a name="implementation"></a>實作

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 使用新式驗證解決方案（OAuth 2.0、 [OpenID connect](v2-protocols-oidc.md)）來安全地登入使用者。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  不要直接針對 OAuth 2.0 和 Open ID 這類通訊協定進行程式設計。 請改為利用[Microsoft 驗證程式庫（MSAL）](msal-overview.md)。 MSAL 程式庫會將安全性通訊協定安全地包裝在便於使用的程式庫中，您可以取得條件式[存取](/azure/active-directory/conditional-access/overview)案例、全裝置[單一登入（SSO）](/azure/active-directory/manage-apps/what-is-single-sign-on)和內建權杖快取支援的內建支援。 如需詳細資訊，請參閱 Microsoft 支援的[用戶端程式庫](reference-v2-libraries.md#microsoft-supported-client-libraries)和[中介軟體](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)程式庫清單，以及[相容的協力廠商用戶端程式庫](reference-v2-libraries.md#compatible-client-libraries)清單。<br/><br/>如果您必須撰寫驗證通訊協定的程式碼，您應該遵循[MICROSOFT SDL](https://www.microsoft.com/sdl/default.aspx)之類的方法。 請密切注意每個通訊協定的標準規格中的安全性考慮。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  將現有的應用程式從[Azure Active Directory Authentication library （ADAL）](../azuread-dev/active-directory-authentication-libraries.md)遷移至[Microsoft 驗證程式庫](msal-overview.md)。 MSAL 是 Microsoft 的最新身分識別平臺解決方案，而且偏好用於 ADAL。 其適用于 .NET、JavaScript、Android、iOS、macOS，而且在 Python 和 JAVA 中也是公開預覽。 深入瞭解遷移[ADAL.NET](msal-net-migration.md)、 [ADAL](msal-compare-msal-js-and-adal-js.md)和[ADAL.NET 和 iOS broker](msal-net-migration-ios-broker.md)應用程式。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  若為行動應用程式，請使用應用程式註冊體驗來設定每個平臺。 為了讓您的應用程式利用 Microsoft Authenticator 或 Microsoft 公司入口網站來進行單一登入，您的應用程式需要設定「broker 重新導向 URI」。 這可讓 Microsoft 在驗證之後將控制權交還給您的應用程式。 在設定每個平臺時，應用程式註冊體驗會引導您完成整個過程。 使用快速入門來下載實用的範例。 在 iOS 上，請盡可能使用訊息代理程式和系統 web 程式。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  在 web 應用程式或 web Api 中，每個帳戶保留一個權杖快取。  針對 web 應用程式，權杖快取應該以帳戶識別碼做為索引鍵。  針對 web Api，此帳戶應使用用來呼叫 API 之權杖的雜湊來做為索引鍵。 MSAL.NET 提供 .NET Framework 和 .NET Core subplatforms 中的自訂權杖快取序列化。 基於安全性和效能的考慮，我們建議您將每個使用者的一個快取序列化。 如需詳細資訊，請參閱權杖快取[序列化](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果您的應用程式所需的資料可透過[Microsoft Graph](https://developer.microsoft.com/graph)取得，請使用 Microsoft Graph 端點（而不是個別 API）來要求此資料的許可權。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |請不要查看 [存取權杖] 值，或嘗試將它剖析為用戶端。  如果您的用戶端需要瞭解使用者的相關資訊，或呼叫 Microsoft Graph，他們就可以變更值、格式，甚至是在不發出警告的情況下，一律使用 id_token。  只有 web Api 會剖析存取權杖（因為它們是定義格式和設定加密金鑰的）。 |

## <a name="end-user-experience"></a>使用者體驗

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | [瞭解同意體驗](application-consent-experience.md)，並設定應用程式的同意提示，讓使用者和系統管理員有足夠的資訊來判斷他們是否信任您的應用程式。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 在互動式流程之前嘗試無訊息驗證（無訊息權杖取得），將使用者在使用您的應用程式時需要輸入登入認證的次數降到最低。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 針對每個登入，請勿使用「提示 = 同意」。 如果您判斷需要同意其他許可權（例如，如果您已變更應用程式的必要許可權），請只使用 prompt = 同意。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 適用時，請使用使用者資料來擴充您的應用程式。 使用[MICROSOFT GRAPH API](https://developer.microsoft.com/graph)是執行此動作的簡單方法。 可協助您開始使用的[Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)工具。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 註冊應用程式所需的完整許可權集，讓系統管理員可以輕鬆地將同意授與其租使用者。 在執行時間使用累加[式同意](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)，以協助使用者瞭解當第一次啟動時要求應用程式時，可能會顧慮或混淆使用者的許可權。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 實行[全新的單一登出體驗](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)。 這是隱私權和安全性需求，可提供良好的使用者體驗。 |

## <a name="testing"></a>測試

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 測試[條件式存取原則](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)，這可能會影響您的使用者使用您的應用程式的能力。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 使用您打算支援的所有可能帳戶（例如工作或學校帳戶、個人 Microsoft 帳戶、子帳戶和主權帳戶）來測試您的應用程式。 |

## <a name="additional-resources"></a>其他資源

探索有關 v2.0 的深入資訊：

* [Microsoft 身分識別平臺（v2.0 總覽）](v2-overview.md)
* [Microsoft 身分識別平臺通訊協定參考](active-directory-v2-protocols.md)
* [存取權杖參考](access-tokens.md)
* [識別碼權杖參考](id-tokens.md)
* [驗證程式庫參考](reference-v2-libraries.md)
* [Microsoft 身分識別平臺中的許可權和同意](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
