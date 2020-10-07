---
title: Microsoft 身分識別平台概觀 - Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身分識別平台的元件，以及其如何協助您在應用程式中建立身分識別和存取管理 (IAM) 支援。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40
ms.openlocfilehash: 831af87159b909ca744839d2997aa2b70cbc72e3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91627120"
---
# <a name="what-is-the-microsoft-identity-platform"></a>什麼是 Microsoft 身分識別平台？

Microsoft 身分識別平台可協助您建置可供使用者和客戶使用其 Microsoft 身分識別或社交帳戶來登入的應用程式，並授權其存取 API 或 Microsoft API，例如 Microsoft Graph。

Microsoft 身分識別平台由數個元件組成：

- **OAuth 2.0 和 OpenID Connect 符合標準的驗證服務**，可讓開發人員驗證多個身分識別類型，包括：
  - 工作或學校帳戶 (透過 Azure AD 佈建)
  - 個人 Microsoft 帳戶，例如 Skype、Xbox 及 Outlook.com
  - 社交或本機帳戶 (透過使用 Azure AD B2C)
- **開放原始碼程式庫**：Microsoft 驗證程式庫 (MSAL) 和其他符合標準的程式庫支援
- **應用程式管理入口網站**：Azure 入口網站的註冊和設定體驗，以及其他 Azure 管理功能。
- **應用程式設定 API 和 PowerShell**：透過 Microsoft Graph API 和 PowerShell 以程式設計方式設定應用程式，以便您將 DevOps 工作自動化。
- **開發人員內容**：技術文件，包括快速入門、教學課程、操作指南和程式碼範例。

對於開發人員，Microsoft 身分識別平台會提供身分識別和安全性空間的現代化創新整合，例如無密碼驗證、設定驗證和條件式存取。 您不需要自行實作這類功能：與 Microsoft 身分識別平台整合的應用程式可以原生方式利用這類創新。

透過 Microsoft 身分識別平台，您只需撰寫程式碼一次，即可觸及任何使用者。 您可以建置應用程式一次並讓它在許多平台上運作，或建置可當作用戶端以及資源應用程式 (API) 的應用程式。

## <a name="getting-started"></a>開始使用

選擇您想要建立的[應用程式案例](authentication-flows-app-scenarios.md)。 這些案例中的每一個都會從概觀開始，然後提供快速入門的連結來協助您開始並執行：

- [單頁應用程式 (SPA)](scenario-spa-overview.md)
- [登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)
- [呼叫 Web API 的 Web 應用程式](scenario-web-app-call-api-overview.md)
- [受保護的 Web API](scenario-protected-web-api-overview.md)
- [呼叫 Web API 的 Web API](scenario-web-api-call-api-overview.md)
- [傳統型應用程式](scenario-desktop-overview.md)
- [精靈應用程式](scenario-daemon-overview.md)
- [行動應用程式](scenario-mobile-overview.md)

當您使用 Microsoft 身分識別平台來整合應用程式中的驗證和授權時，您可以參考此圖像，其中概述最常見的應用程式案例及其身分識別元件。 選取圖像可透過完整大小檢視。

[![顯示 Microsoft 身分識別平台中數個應用程式案例的 Metro 地圖](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>了解驗證概念

在這組建議的文章中，了解如何將核心驗證和 Azure AD 概念套用至 Microsoft 身分識別平台：

- [驗證基本概念](./authentication-vs-authorization.md)
- [應用程式與服務主體](app-objects-and-service-principals.md)
- [對象](v2-supported-account-types.md)
- [權限和同意](v2-permissions-and-consent.md)
- [識別碼權杖](id-tokens.md)
- [存取權杖](access-tokens.md)
- [驗證流程和應用程式情節](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>更多身分識別與存取管理選項

[Azure AD B2C](../../active-directory-b2c/overview.md) - 建立客戶面向的應用程式，讓使用者可以使用其社交帳戶 (例如 Facebook 或 Google) 或電子郵件地址和密碼來登入。

[Azure AD B2B](../external-identities/what-is-b2b.md) - 邀請外部使用者成為您 Azure AD 租用戶的「來賓」使用者，並對其指派權限以進行授權，同時他們可使用其現有認證來進行驗證。

> [!TIP]
> 正在尋找 Azure Active Directory 開發人員平台 (v1.0) 文件嗎？ 請參閱[開發人員適用的 Azure Active Directory (v1.0) 概觀](../azuread-dev/v1-overview.md)。

## <a name="next-steps"></a>後續步驟

如果您有 Azure 帳戶，表示您已擁有 Azure Active Directory 租用戶的存取權，但大部分的 Microsoft 身分識別平台開發人員都需要自己的 Azure AD 租用戶，以在開發應用程式時使用，也就是「開發人員租用戶 (dev tenant)」。

了解如何建立您自己的租用戶，以在建立應用程式時使用：

[快速入門：設定 Azure AD 租用戶](quickstart-create-new-tenant.md)