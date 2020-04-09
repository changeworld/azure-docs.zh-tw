---
title: 微軟身份平台認證協定
description: Microsoft 識別平台支援的身份驗證協定的概述
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884676"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>微軟身份平台認證協定

Microsoft 標識平台支援幾種使用最廣泛的身份驗證和授權協定。 本節中的主題介紹 Microsoft 標識平臺中受支援的協定及其實現。 這些主題包括支援的宣告類型回顧、同盟中繼資料的使用簡介、詳細的 OAuth 2.0  和 SAML 2.0 通訊協定參考文件，以及疑難排解小節。

## <a name="authentication-protocols-articles-and-reference"></a>認證協定文章和參考

* [有關在 Microsoft 識別平台中簽名密鑰滾動的重要資訊](active-directory-signing-key-rollover.md)– 瞭解 Microsoft 標識平台的簽名密鑰滾動節奏、可以進行更改以自動更新密鑰以及如何更新最常見的應用程式方案的討論。
* [受支援的權杖和聲明類型](id-tokens.md)- 瞭解 Microsoft 識別平台發出權杖中的聲明。
* [微軟身份平臺中的OAuth 2.0](v2-oauth2-auth-code-flow.md) - 瞭解 Microsoft 身份平臺中 OAuth 2.0 的實現。
* [OpenID Connect 1.0](v2-protocols-oidc.md) - 了解如何使用 OAuth 2.0 (授權通訊協定) 進行驗證。
* [使用用戶端認證的服務對服務呼叫](v2-oauth2-client-creds-grant-flow.md) - 了解如何使用 OAuth 2.0 用戶端認證授與流程來進行服務對服務呼叫。
* [使用代理者流程的服務對服務呼叫](v2-oauth2-on-behalf-of-flow.md) - 了解如何使用 OAuth 2.0 代理者流程來進行服務對服務呼叫。
* [SAML 協定引用](active-directory-saml-protocol-reference.md)- 瞭解 Microsoft 識別平台的單一登入和單簽出 SAML 設定檔。

## <a name="see-also"></a>另請參閱

* [微軟身份平台概述](v2-overview.md)
* [Active Directory 程式碼範例](sample-v2-code.md)
