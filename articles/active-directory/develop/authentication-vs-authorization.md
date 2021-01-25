---
title: 驗證與授權 |蔚藍
titleSuffix: Microsoft identity platform
description: 深入瞭解 Microsoft 身分識別平臺中的驗證和授權基本概念。
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
ms.openlocfilehash: b81b34010736bce33085cb1ebf0faa3da6a41bd6
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755384"
---
# <a name="authentication-vs-authorization"></a>驗證與授權

本文定義驗證和授權。 它也會簡短介紹如何使用 Microsoft 身分識別平臺來驗證和授權 web 應用程式、web Api 或呼叫受保護 web Api 的應用程式中的使用者。 如果您發現不熟悉的字詞，請試試我們的 [詞彙](developer-glossary.md) 或 [Microsoft 身分識別平臺](identity-videos.md)影片，其中涵蓋基本概念。

## <a name="authentication"></a>驗證

*驗證* 是證明您是指您所說的流程。 有時會縮短為 *驗證*。 Microsoft 身分識別平臺會使用 [OpenID Connect](https://openid.net/connect/) 的通訊協定來處理驗證。

## <a name="authorization"></a>授權

*授權* 是授與已驗證的合作對象權限以執行某些工作的動作。 會指定您可以存取哪些資料，以及可以將該資料用於哪些用途。 授權有時會縮短為 *授權。* Microsoft 身分識別平臺會使用 [OAuth 2.0](https://oauth.net/2/) 通訊協定來處理授權。

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>使用 Microsoft 身分識別平臺進行驗證和授權

當您需要在多個應用程式之間新增或移除使用者時，建立每個應用程式以維護其使用者名稱和密碼資訊，會產生高系統管理負擔。 相反地，您的應用程式可以將該責任委派給集中式身分識別提供者。

Azure Active Directory (Azure AD) 是雲端中的集中式識別提供者。 將驗證和授權委派給它可啟用下列案例：

- 要求使用者必須位於特定位置的條件式存取原則。
- 使用 [多重要素驗證](../authentication/concept-mfa-howitworks.md)，有時也稱為雙因素驗證或2FA。
- 讓使用者登入一次，然後自動登入共用相同集中式目錄的所有 web 應用程式。 這項功能稱為 *單一登入 (SSO)*。

Microsoft 身分識別平臺會提供身分識別即服務，為應用程式開發人員簡化授權和驗證。 它支援業界標準的通訊協定，以及適用于不同平臺的開放原始碼程式庫，協助您快速開始撰寫程式碼。 它可讓開發人員建立應用程式，以登入所有 Microsoft 身分識別、取得權杖以呼叫 [Microsoft Graph](https://developer.microsoft.com/graph/)、存取 Microsoft api，或存取開發人員建立的其他 api。

這段影片說明 Microsoft 身分識別平臺和新式驗證的基本概念： 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

以下是 Microsoft 身分識別平臺所使用的通訊協定比較：

* **Oauth 與 OpenID Connect**：平臺會使用 OAuth 進行授權，並 OPENID CONNECT (OIDC) 進行驗證。 OpenID Connect 建置於 OAuth 2.0 之上，因此兩者的術語和流程都很類似。 您甚至可以透過 OpenID Connect) 來驗證使用者 (，以及取得存取受保護資源的授權，而使用者透過 OAuth 2.0) 在一個要求中擁有 (。 如需詳細資訊，請參閱 [OAuth 2.0 和 OpenID Connect 通訊協定](active-directory-v2-protocols.md)和 [OpenID Connect 通訊協定](v2-protocols-oidc.md)。
* **Oauth 與 saml**：平臺會使用 OAuth 2.0 進行授權和 saml 以進行驗證。 如需有關如何搭配使用這些通訊協定來驗證使用者，以及取得存取受保護資源之授權的詳細資訊，請參閱 [Microsoft 身分識別平臺和 OAuth 2.0 SAML 持有](./scenario-token-exchange-saml-oauth.md)人判斷提示流程。
* **OpenID Connect 與 saml**：平臺會使用 OPENID CONNECT 和 saml 來驗證使用者，並啟用單一登入。 SAML 驗證通常會與身分識別提供者搭配使用，例如 Active Directory 同盟服務 (AD FS) 同盟至 Azure AD，因此通常用於企業應用程式。 OpenID Connect 通常用於純雲端中的應用程式，例如行動應用程式、網站和 web Api。

## <a name="next-steps"></a>後續步驟

適用于其他涵蓋驗證和授權基本概念的主題：

* 若要瞭解如何在授權和驗證中使用存取權杖、重新整理權杖和識別碼權杖，請參閱 [安全性權杖](security-tokens.md)。
* 若要瞭解註冊應用程式以與 Microsoft 身分識別平臺整合的程式，請參閱 [應用程式模型](application-model.md)。