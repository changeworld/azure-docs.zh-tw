---
title: Microsoft 身分識別平臺驗證通訊協定
description: 概述 Microsoft 身分識別平臺所支援的驗證通訊協定
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
ROBOTS: NOINDEX
ms.openlocfilehash: ffd9b415d3b2a8e1aa716caa0e8289c6ae127b71
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065077"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft 身分識別平臺驗證通訊協定

Microsoft 身分識別平臺支援數個最廣泛使用的驗證和授權通訊協定。 本節中的主題說明在 Microsoft 身分識別平臺中支援的通訊協定和其執行方式。 這些主題包括支援的宣告類型回顧、同盟中繼資料的使用簡介、詳細的 OAuth 2.0  和 SAML 2.0 通訊協定參考文件，以及疑難排解小節。

## <a name="authentication-protocols-articles-and-reference"></a>驗證通訊協定文章和參考

* [在 Microsoft 身分識別平臺中簽署金鑰變換的重要資訊](active-directory-signing-key-rollover.md) –瞭解 microsoft 身分識別平臺的簽署金鑰變換頻率、您可以自動更新金鑰的變更，以及如何更新最常見的應用程式案例的討論。
* [支援的權杖和宣告類型](id-tokens.md) -瞭解 Microsoft 身分識別平臺所發出之權杖中的宣告。
* [Microsoft 身分識別平臺中的 oauth 2.0](v2-oauth2-auth-code-flow.md) -瞭解如何在 microsoft 身分識別平臺中執行 oauth 2.0。
* [OpenID Connect 1.0](v2-protocols-oidc.md) - 了解如何使用 OAuth 2.0 (授權通訊協定) 進行驗證。
* [使用用戶端認證的服務對服務呼叫](v2-oauth2-client-creds-grant-flow.md) - 了解如何使用 OAuth 2.0 用戶端認證授與流程來進行服務對服務呼叫。
* [使用代理者流程的服務對服務呼叫](v2-oauth2-on-behalf-of-flow.md) - 了解如何使用 OAuth 2.0 代理者流程來進行服務對服務呼叫。
* [SAML 通訊協定參考](active-directory-saml-protocol-reference.md) -瞭解 Microsoft 身分識別平臺的單一 Sign-On 和單一登出 SAML 設定檔。

## <a name="see-also"></a>請參閱

* [Microsoft 身分識別平台概觀](v2-overview.md)
* [Active Directory 程式碼範例](sample-v2-code.md)
