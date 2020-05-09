---
title: 驗證與授權 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 身分識別平臺（v2.0）中驗證和授權的基本概念。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: b905b8f3fe99b83bafdd61b1daa25549354c5275
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926777"
---
# <a name="authentication-vs-authorization"></a>驗證與授權

本文會定義驗證和授權，並簡要說明如何使用 Microsoft 身分識別平臺來驗證和授權 web 應用程式、web Api 或呼叫受保護 web Api 的應用程式中的使用者。 如果您看到不熟悉的詞彙，請嘗試我們的[詞彙](developer-glossary.md)或我們的[Microsoft 身分識別平臺](identity-videos.md)影片，其中涵蓋了基本概念。

## <a name="authentication"></a>驗證

**驗證**是證明您是誰所說的程式。 驗證 (Authentication) 有時會被簡稱為 AuthN。 Microsoft 身分識別平臺會實行[OpenID connect](https://openid.net/connect/)和[SAML 2.0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html)通訊協定來處理驗證。

## <a name="authorization"></a>授權

**授權**是指授與已驗證的合作物件使用權限來執行某個作業的動作。 它會指定您可存取的資料，以及您可以對該資料執行的動作。 授權 (Authorization) 有時會被簡稱為 AuthZ。 Microsoft 身分識別平臺會實行[OAuth 2.0](https://oauth.net/2/)通訊協定來處理授權。

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>使用 Microsoft 身分識別平臺的驗證和授權

應用程式不會建立每個應用程式來維護自己的使用者名稱和密碼資訊，而是在您需要跨多個應用程式新增或移除使用者時，產生高系統管理負擔，而應用程式可以將該責任委派給集中式身分識別提供者。

Azure Active Directory （Azure AD）是雲端中的集中式身分識別提供者。 將驗證和授權委派給它可啟用條件式存取原則，例如要求使用者位於特定位置、使用多重要素驗證，以及讓使用者登入一次，然後自動登入所有共用相同集中式目錄的 web 應用程式。 這項功能稱為「**單一登入」（SSO）**。

Microsoft 身分識別平臺藉由提供身分識別即服務來簡化應用程式開發人員的驗證和授權，並支援業界標準的通訊協定（例如 OAuth 2.0、OpenID Connect 和 SAML 2.0），以及適用于不同平臺的開放原始碼程式庫，協助您快速開始編碼。 它可讓開發人員建立應用程式來登入所有 Microsoft 身分識別、取得權杖以呼叫[Microsoft Graph](https://developer.microsoft.com/graph/)、其他 Microsoft api 或開發人員已建立的 api。 如需詳細資訊，請參閱[Microsoft 身分識別平臺的演進](about-microsoft-identity-platform.md)。

以下是 Microsoft 身分識別平臺所使用的各種通訊協定的簡短比較：

* **Oauth 與 Openid connect**： oauth 是用於授權，而 openid CONNECT （OIDC）用於驗證。 OpenID Connect 建置於 OAuth 2.0 之上，因此這兩者的術語和流程都很類似。 您甚至可以驗證使用者（使用 OpenID Connect），並取得授權以存取使用者在一個要求中所擁有（使用 OAuth 2.0）的受保護資源。 如需詳細資訊，請參閱[OAuth 2.0 和 Openid connect 通訊協定](active-directory-v2-protocols.md)和[openid connect 通訊協定](v2-protocols-oidc.md)。
* **Oauth 與 saml**： oauth 是用於授權，而 saml 用於驗證。 如需如何搭配使用這兩個通訊協定來驗證使用者（使用 SAML）並取得授權以存取受保護資源（使用 OAuth 2.0）的詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0 SAML 持有](v2-saml-bearer-assertion.md)人判斷提示流程。
* **Openid connect 與 saml**： openid CONNECT 和 saml 都是用來驗證使用者，而且可用來啟用單一登入。 SAML 驗證常用於身分識別提供者（例如 Active Directory 同盟服務（ADFS）同盟至 Azure AD，因此經常用於企業應用程式中。

## <a name="next-steps"></a>後續步驟

其他涵蓋驗證和授權基本概念的主題：

* 請參閱[安全性權杖](security-tokens.md)，以瞭解如何在驗證和授權中使用存取權杖、重新整理權杖和識別碼權杖。
* 請參閱[應用程式模型](application-model.md)，以瞭解註冊應用程式以與 Microsoft 身分識別平臺整合的過程。
* 請參閱[應用程式登入流程](app-sign-in-flow.md)，以瞭解 Microsoft 身分識別平臺中 web、桌面和行動應用程式的登入流程。

若要深入瞭解 Microsoft 身分識別平臺所實行的通訊協定：

* 如需 OpenID Connect 和 OAuth 2.0 標準的詳細資訊，請參閱[Microsoft 身分識別平臺上的 OAuth 2.0 和 Openid connect 通訊協定](active-directory-v2-protocols.md)。
* 如需 Microsoft 身分識別平臺如何支援單一登入的詳細資訊，請參閱[單一登入 SAML 通訊協定](single-sign-on-saml-protocol.md)。
