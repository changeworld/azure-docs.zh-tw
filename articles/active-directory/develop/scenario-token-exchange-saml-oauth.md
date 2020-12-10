---
title: 在 Azure Active Directory 中使用 SAML 和 OIDC/OAuth 的 Microsoft 身分識別平臺權杖交換案例
description: 瞭解在 Azure Active Directory 中使用 SAML 和 OIDC/OAuth 時常見的權杖交換案例。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 16de95e177f6be1b5bb2517b5ae3ca595cfb5766
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939112"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>使用 SAML 和 OIDC/OAuth 的 Microsoft 身分識別平臺權杖交換案例

SAML 和 OpenID Connect (OIDC) /OAuth 是一種常用的通訊協定，可用來執行單一 Sign-On (SSO) 。 某些應用程式可能只會執行 SAML，而其他應用程式可能只會執行 OIDC/OAuth。 這兩種通訊協定都使用權杖來傳達秘密。 若要深入瞭解 SAML，請參閱 [單一 Sign-On saml 通訊協定](single-sign-on-saml-protocol.md)。 若要深入瞭解 OIDC/OAuth，請參閱 [Microsoft 身分識別平臺上的 OAuth 2.0 和 OpenID Connect 通訊協定](active-directory-v2-protocols.md)。

本文概述應用程式執行 SAML 但您需要呼叫圖形 API （使用 OIDC/OAuth）的常見案例。 針對使用此案例的人員提供基本指導方針。

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>案例：您有 SAML 權杖，而且想要呼叫圖形 API
許多應用程式都是使用 SAML 來執行。 不過，圖形 API 會使用 OIDC/OAuth 通訊協定。 將 OIDC/OAuth 功能新增至 SAML 應用程式是可行的，但不是很簡單。 當應用程式中有 OAuth 功能可供使用之後，就可以使用圖形 API。

一般策略是將 OIDC/OAuth 堆疊新增至您的應用程式。 您可以使用您的應用程式來執行這兩個標準，您可以使用會話 cookie。 您不會明確地交換權杖。 您要使用 SAML 登入使用者，以產生會話 cookie。 當圖形 API 叫用 OAuth 流程時，您會使用會話 cookie 進行驗證。 此策略假設條件式存取檢查通過，而且使用者已獲授權。

> [!NOTE]
> 新增 OIDC/OAuth 行為的建議程式庫是 Microsoft Authentication Library (MSAL) 。 若要深入瞭解 MSAL，請參閱 [Microsoft 驗證程式庫的總覽 (MSAL) ](msal-overview.md)。 先前的程式庫 Active Directory 驗證程式庫 (ADAL) 中呼叫，不過不建議 MSAL 取代它。

## <a name="next-steps"></a>後續步驟
- [驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
