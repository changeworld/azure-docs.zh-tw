---
title: 驗證與授權 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身分識別平台 (v2.0) 中驗證與授權的基本概念。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 320debcf2b744e949faf7b29dd72ed334fe0feea
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503720"
---
# <a name="authentication-vs-authorization"></a>驗證與授權

本文會定義驗證與授權，並簡要說明如何使用 Microsoft 身分識別平台來驗證和授權 Web 應用程式、Web API 或應用程式 (呼叫受保護 Web API ) 中的使用者。 如果您看到不熟悉的詞彙，請嘗試我們的[詞彙](developer-glossary.md)或我們的 [Microsoft 身分識別平台影片](identity-videos.md)，其中涵蓋基本概念。

## <a name="authentication"></a>驗證

**驗證**是證明您確實是您本人的程序。 驗證 (Authentication) 有時會被簡稱為 AuthN。 Microsoft 身分識別平台會實行 [OpenID Connect](https://openid.net/connect/) 通訊協定來處理驗證。

## <a name="authorization"></a>授權

**授權**是授與已驗證的合作對象權限以執行某些工作的動作。 會指定您可以存取哪些資料，以及可以將該資料用於哪些用途。 授權 (Authorization) 有時會被簡稱為 AuthZ。 Microsoft 身分識別平台會實作 [OAuth 2.0](https://oauth.net/2/) 通訊協定以處理授權。

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>使用 Microsoft 身分識別平台的驗證與授權

應用程式不是建立應用程式，需要個別維護自己的使用者名稱和密碼資訊，因為這樣當您需要跨多個應用程式新增或移除使用者時，會產生高系統管理負擔，而是應用程式可以將該責任委派給集中式識別提供者。

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Azure Active Directory (Azure AD) 是雲端中的集中式識別提供者。 將驗證與授權委派給識別提供者可啟用例如條件式存取原則的案例，該原則會要求使用者位於特定位置、使用[多重要素驗證](../authentication/concept-mfa-howitworks.md) (有時稱為雙因素驗證或 2FA)，以及讓使用者登入一次，然後自動登入共用相同集中式目錄的所有 Web 應用程式。 這項功能稱為**單一登入 (SSO)** 。

Microsoft 身分識別平台可透過以服務的形式提供身分識別來簡化應用程式開發人員的授權與驗證工作，並支援業界標準的通訊協定 (例如 OAuth 2.0 和 OpenID Connect)，以及適用於不同平台的開放原始碼程式庫，以協助您快速開始撰寫程式碼。 可讓開發人員建置應用程式以登入所有 Microsoft 身分識別、取得權杖以呼叫 [Microsoft Graph](https://developer.microsoft.com/graph/)、其他 Microsoft API 或開發人員所建置的 API。

以下是 Microsoft 身分識別平台所使用各種通訊協定的簡短比較：

* **OAuth 與 OpenID Connect**：OAuth 用於授權，而 OpenID Connect (OIDC) 用於驗證。 OpenID Connect 建置於 OAuth 2.0 之上，因此兩者的術語和流程都很類似。 您甚至可以驗證使用者 (使用 OpenID Connect)，並取得授權以在一個要求中存取使用者所擁有 (使用 OAuth 2.0) 的受保護資源。 如需詳細資訊，請參閱 [OAuth 2.0 和 OpenID Connect 通訊協定](active-directory-v2-protocols.md)和 [OpenID Connect 通訊協定](v2-protocols-oidc.md)。
* **OAuth 與 SAML**：OAuth 用於授權，而 SAML 用於驗證。 如需如何搭配使用這兩種通訊協定，同時驗證使用者 (使用 SAML ) 及取得授權以存取受保護的資源 (使用 OAuth 2.0) 的詳細資訊，請參閱 [Microsoft 身分識別平台和 OAuth 2.0 SAML 持有人聲明流程](v2-saml-bearer-assertion.md)。
* **OpenID Connect 與 SAML**：OpenID Connect 和 SAML 都會用來驗證使用者，並用於啟用單一登入。 SAML 驗證常用於識別提供者，例如 Active Directory 同盟服務 (ADFS) 同盟至 Azure AD，因此經常用於企業應用程式中。 OpenID Connect 通常用於純粹在雲端中的應用程式，例如行動應用程式、網站和 Web API。

## <a name="next-steps"></a>後續步驟

如需涵蓋驗證和授權基本概念的其他主題：

* 若要了解存取權杖、重新整理權杖和識別碼權杖如何用於授權和驗證，請參閱[安全性權杖](security-tokens.md)。
* 若要了解註冊應用程式以與 Microsoft 身分識別平台整合的程序，請參閱[應用程式模型](application-model.md)。
* 若要了解 Microsoft 身分識別平台中 Web、傳統型和行動應用程式的登入流程，請參閱[應用程式登入流程](app-sign-in-flow.md)。

* 若要深入了解 Microsoft 身分識別平台所實行的通訊協定，請參閱 [Microsoft 身分識別平台上的 OAuth 2.0 和 OpenID Connect 通訊協定](active-directory-v2-protocols.md)。
* 如需 Microsoft 身分識別平台如何支援單一登入的詳細資訊，請參閱[單一登入 SAML 通訊協定](single-sign-on-saml-protocol.md)。
* 如需您可以在應用程式中實作單一登入的不同方式詳細資訊，請參閱 [Azure Active Directory 中的應用程式單一登入](../manage-apps/what-is-single-sign-on.md)。
