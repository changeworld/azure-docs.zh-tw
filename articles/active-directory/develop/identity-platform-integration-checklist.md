---
title: 微軟身份平臺的最佳實踐 |蔚藍
description: 在與 Microsoft 標識平臺集成時，瞭解最佳實踐、建議和常見疏忽。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050487"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>微軟身份平臺最佳實踐和建議

本文重點介紹了與 Microsoft 標識平臺集成時的最佳做法、建議和常見疏忽。  此檢查表將指導您進行高品質和安全的集成。 定期查看此清單，以確保保持應用與標識平臺集成的品質和安全性。 該清單無意查看整個應用程式。 隨著我們對平臺進行改進，檢查表的內容可能會發生變化。

如果您剛剛開始，請查看[Microsoft 標識平臺文檔](index.yml)，瞭解身份驗證基礎知識、Microsoft 標識平臺中的應用程式方案等。

使用以下檢查表確保應用程式有效地與[Microsoft 標識平臺](https://docs.microsoft.com/azure/active-directory/develop/)集成。

## <a name="basics"></a>基本概念

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 閱讀並理解[微軟平臺策略](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)。 確保應用程式遵守為保護使用者和平臺而設計的術語。 |

## <a name="ownership"></a>擁有權

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 確保與用於註冊和管理應用的帳戶關聯的資訊是最新的。 |

## <a name="branding"></a>商標

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 遵守[應用程式的品牌指南](howto-add-branding-in-azure-ad-apps.md)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 為您的應用程式提供有意義的名稱和徽標。 此資訊將顯示在您的[申請的同意提示上](application-consent-experience.md)。 確保您的姓名和徽標代表您的公司/產品，以便使用者可以做出明智的決策。 確保您沒有侵犯任何商標。 |

## <a name="privacy"></a>隱私權

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 提供指向應用服務條款和隱私權聲明的連結。 |

## <a name="security"></a>安全性

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 管理重定向 URI： <ul><li>維護所有重定向 URI 的擁有權，並保持其 DNS 記錄的最新。</li><li>不要在 URI 中使用萬用字元 （*）。</li><li>對於 Web 應用，請確保所有 URI 都是安全的和加密的（例如，使用 HTTPs 方案）。</li><li>對於公共用戶端，如果適用，請使用特定于平臺的重定向 URI（主要用於 iOS 和 Android）。 否則，請使用具有高隨機性的重定向 URI，以防止在回叫到應用時發生衝突。</li><li>如果應用是從孤立的 Web 代理使用的，則可以使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。</li><li>定期查看和修剪所有未使用或不必要的重定向 URI。</li></ul> |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果應用在目錄中註冊，請最小化並手動監視應用註冊擁有者的清單。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 除非顯式要求，否則不要啟用對[OAuth2 隱式授予流](v2-oauth2-implicit-grant-flow.md)的支援。 [在此處](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)瞭解有效方案。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 超越使用者名/密碼。 不要使用直接處理使用者密碼[的資源擁有者密碼憑據流 （ROPC）。](v2-oauth-ropc.md) 此流需要高度的信任和使用者公開，並且只有在其他更安全的流不能使用時才應使用。 在某些情節中，仍然需要此流程 (例如 DevOps)，但請注意，使用它會對您的應用程式施加限制式。  對於更現代的方法，請閱讀[身份驗證流和應用程式方案](authentication-flows-app-scenarios.md)。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 保護和管理 Web 應用、Web API 和守護進程應用的機密應用憑據。 使用[證書憑據](active-directory-certificate-credentials.md)，而不是密碼憑據（用戶端機密）。 如果必須使用密碼憑據，請不要手動設置密碼憑據。 不要將憑據存儲在代碼或配置中，也絕不允許由人類處理。 如果可能，請使用[Azure 資源的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)或[Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)來存儲並定期輪換憑據。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 確保應用程式請求的許可權最少。 僅請求應用程式絕對需要的許可權，並且僅在您需要許可權時請求許可權。 瞭解[不同類型的許可權](v2-permissions-and-consent.md#permission-types)。 如有必要，僅使用應用程式許可權;盡可能使用委派的許可權。 有關 Microsoft 圖形許可權的完整清單，請參閱此[許可權引用](https://docs.microsoft.com/graph/permissions-reference)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果您使用 Microsoft 標識平臺保護 API，請仔細考慮它應該公開的許可權。 考慮解決方案的正確細微性，以及哪些許可權需要管理員同意。 在作出任何授權決定之前，請檢查傳入權杖中的預期許可權。 |

## <a name="implementation"></a>實作

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 使用現代身份驗證解決方案 （OAuth 2.0， [OpenID 連接](v2-protocols-oidc.md)） 安全地登錄使用者。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  不要直接針對 OAuth 2.0 和打開 ID 等協定進行程式設計。 相反，利用[微軟身份驗證庫 （MSAL）。](msal-overview.md) MSAL 庫在便於使用的庫中安全地包裝安全協定，您將獲得對[條件訪問](/azure/active-directory/conditional-access/overview)方案、設備範圍[單一登入 （SSO）](/azure/active-directory/manage-apps/what-is-single-sign-on)和內置權杖緩存支援的內置支援。 有關詳細資訊，請參閱 Microsoft 支援的[用戶端庫](reference-v2-libraries.md#microsoft-supported-client-libraries)和[中介軟體庫](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)的清單以及[相容的協力廠商用戶端庫](reference-v2-libraries.md#compatible-client-libraries)的清單。<br/><br/>如果必須為身份驗證協定提供代碼，則應遵循[Microsoft SDL](https://www.microsoft.com/sdl/default.aspx)等方法。 密切關注每個協定的標準規範中的安全注意事項。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  將現有應用從[Azure 活動目錄身份驗證庫 （ADAL）](../azuread-dev/active-directory-authentication-libraries.md)遷移到[Microsoft 身份驗證庫](msal-overview.md)。 MSAL 是微軟最新的身份平臺解決方案，是 ADAL 的首選。 它在 .NET、JavaScript、Android、iOS、macOS 上提供，並且在 Python 和 JAVA 的公開預覽版中提供。 閱讀有關遷移[ADAL.NET](msal-net-migration.md) [ADAL.js](msal-compare-msal-js-and-adal-js.md)和[ADAL.NET和 iOS 代理](msal-net-migration-ios-broker.md)應用程式的更多資訊。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  對於移動應用，請使用應用程式註冊體驗配置每個平臺。 為了使應用程式能夠利用 Microsoft 身份驗證器或 Microsoft 公司門戶進行單次登錄，你的應用需要配置"代理重定向 URI"。 這允許 Microsoft 在身份驗證後將控制權返回到您的應用程式。 配置每個平臺時，應用註冊體驗將指導您完成此過程。 使用快速入門下載工作示例。 在 iOS 上，盡可能使用代理和系統 Web 視圖。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |  在 Web 應用或 Web API 中，每個帳戶保留一個權杖緩存。  對於 Web 應用，權杖緩存應按帳戶 ID 進行鍵控。  對於 Web API，帳戶應按用於調用 API 的權杖的雜湊鍵。 MSAL.NET在 .NET 框架和 .NET Core 子平臺中提供自訂權杖緩存序列化。 出於安全性和性能原因，我們建議為每個使用者序列化一個緩存。 有關詳細資訊，請閱讀有關[權杖緩存序列化](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)的資訊。|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果應用所需的資料可通過[Microsoft 圖形](https://developer.microsoft.com/graph)獲得，請使用 Microsoft 圖形終結點請求此資料的許可權，而不是單個 API。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) |不要查看訪問權杖值，或嘗試將其解析為用戶端。  他們可以更改值、格式，甚至無需警告即可加密 - 如果用戶端需要瞭解使用者的資訊，則始終使用id_token，或致電 Microsoft Graph。  只有 Web API 應分析訪問權杖（因為它們是定義格式和設置加密金鑰的權杖）。 |

## <a name="end-user-experience"></a>使用者體驗

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | [瞭解同意體驗](application-consent-experience.md)並配置應用同意提示部分，以便最終使用者和管理員有足夠的資訊來確定他們是否信任你的應用。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 通過在互動式流之前嘗試靜默身份驗證（靜默權杖獲取），將使用者在使用應用時需要輸入登錄憑據的次數降至最低。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 不要對每次登錄使用"提示同意"。 僅當您確定需要請求同意以尋求其他許可權（例如，如果您更改了應用所需的許可權）時，才使用提示 @同意。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 在適用的情況下，使用使用者資料豐富應用程式。 使用[Microsoft 圖形 API](https://developer.microsoft.com/graph)是執行此操作的一種簡單方法。 [圖形資源管理器](https://developer.microsoft.com/graph/graph-explorer)工具，可説明您入門。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 註冊應用所需的完整許可權集，以便管理員可以輕鬆地向其租戶授予同意。 在運行時使用[增量同意](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)，説明使用者理解為什麼你的應用請求的許可權，這些許可權可能會引起使用者的關注或混淆， |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 實施[乾淨的單一簽收體驗](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)。 它是隱私和安全要求，並帶來良好的使用者體驗。 |

## <a name="testing"></a>測試

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 測試可能影響使用者使用應用程式能力[的條件訪問策略](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 使用計畫支援的所有可能帳戶（例如，工作或學校帳戶、個人 Microsoft 帳戶、子帳戶和主權帳戶）測試應用程式。 |

## <a name="additional-resources"></a>其他資源

探索有關 v2.0 的深入資訊：

* [微軟身份平臺（v2.0概述）](v2-overview.md)
* [微軟標識平臺協定引用](active-directory-v2-protocols.md)
* [存取權杖參考](access-tokens.md)
* [識別碼權杖參考](id-tokens.md)
* [身份驗證庫引用](reference-v2-libraries.md)
* [Microsoft 身份平臺中的許可權和同意](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
