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
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584294"
---
# <a name="authentication-vs-authorization"></a>驗證與授權

本文會定義驗證和授權，並簡要說明如何使用 Microsoft 身分識別平臺來驗證和授權 web 應用程式、web Api 或呼叫受保護 web Api 的應用程式中的使用者。 如果您看到不熟悉的詞彙，請嘗試我們的[詞彙](developer-glossary.md)或我們的[Microsoft 身分識別平臺](identity-videos.md)影片，其中涵蓋了基本概念。

## <a name="authentication"></a>驗證

**驗證**是證明您是誰所說的程式。 驗證 (Authentication) 有時會被簡稱為 AuthN。 Microsoft 身分識別平臺會實行[OpenID connect](https://openid.net/connect/)通訊協定來處理驗證。

## <a name="authorization"></a>授權

**授權**是指授與已驗證的合作物件使用權限來執行某個作業的動作。 它會指定您可存取的資料，以及您可以對該資料執行的動作。 授權 (Authorization) 有時會被簡稱為 AuthZ。 Microsoft 身分識別平臺會實行[OAuth 2.0](https://oauth.net/2/)通訊協定來處理授權。

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>使用 Microsoft 身分識別平臺的驗證和授權

應用程式不會建立每個應用程式來維護自己的使用者名稱和密碼資訊，而是在您需要跨多個應用程式新增或移除使用者時，產生高系統管理負擔，而應用程式可以將該責任委派給集中式身分識別提供者。

Azure Active Directory （Azure AD）是雲端中的集中式身分識別提供者。 將驗證和授權委派給它可啟用條件式存取原則，例如要求使用者位於特定位置、使用多重要素驗證，以及讓使用者登入一次，然後自動登入所有共用相同集中式目錄的 web 應用程式。 這項功能稱為「**單一登入」（SSO）**。

Microsoft 身分識別平臺藉由提供身分識別即服務來簡化應用程式開發人員的驗證和授權，並支援業界標準的通訊協定（例如 OAuth 2.0 和 OpenID Connect），以及適用于不同平臺的開放原始碼程式庫，協助您快速開始編碼。 它可讓開發人員建立應用程式來登入所有 Microsoft 身分識別、取得權杖以呼叫[Microsoft Graph](https://developer.microsoft.com/graph/)、其他 Microsoft api 或開發人員已建立的 api。 如需詳細資訊，請參閱[Microsoft 身分識別平臺的演進](about-microsoft-identity-platform.md)。

## <a name="next-steps"></a>後續步驟

其他涵蓋驗證和授權基本概念的主題：

* 請參閱[安全性權杖](security-tokens.md)，以瞭解如何在驗證和授權中使用存取權杖、重新整理權杖和識別碼權杖。
* 請參閱[應用程式模型](application-model.md)，以瞭解註冊應用程式以與 Microsoft 身分識別平臺整合的過程。
* 請參閱[應用程式登入流程](app-sign-in-flow.md)，以瞭解 Microsoft 身分識別平臺中 web、桌面和行動應用程式的登入流程。

若要深入瞭解 Microsoft 身分識別平臺所實行的通訊協定：

* 如需 OpenID Connect 和 OAuth 2.0 標準的詳細資訊，請參閱[Microsoft 身分識別平臺上的 OAuth 2.0 和 Openid connect 通訊協定](active-directory-v2-protocols.md)。
* 如需 Microsoft 身分識別平臺如何支援單一登入的詳細資訊，請參閱[單一登入 SAML 通訊協定](single-sign-on-saml-protocol.md)。
