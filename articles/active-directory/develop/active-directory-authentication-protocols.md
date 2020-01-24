---
title: Microsoft 身分識別平臺驗證通訊協定 |Microsoft Docs
description: 概述 Microsoft 身分識別平臺所支援的驗證通訊協定
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 43168ec7217d8f016857ba6dc54ca30bce2dd594
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699286"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft 身分識別平臺驗證通訊協定

Microsoft 身分識別平臺支援數個最常用的驗證和授權通訊協定。 本節中的主題描述在 Microsoft 身分識別平臺中支援的通訊協定和其執行方式。 這些主題包括支援的宣告類型回顧、同盟中繼資料的使用簡介、詳細的 OAuth 2.0 和 SAML 2.0 通訊協定參考文件，以及疑難排解小節。

## <a name="authentication-protocols-articles-and-reference"></a>驗證通訊協定文章和參考

* [Microsoft 身分識別平臺中簽署金鑰變換的相關重要資訊](active-directory-signing-key-rollover.md)–瞭解 microsoft 身分識別平臺的簽署金鑰變換頻率、自動更新金鑰的變更，以及如何更新最常見應用程式案例的討論。
* [支援的權杖和宣告類型](id-tokens.md)-瞭解權杖中 Microsoft 身分識別平臺所發出的宣告。
* [Microsoft 身分識別平臺中的 oauth 2.0](v2-oauth2-auth-code-flow.md) -瞭解 microsoft 身分識別平臺中的 oauth 2.0 執行方式。
* [OpenID Connect 1.0](v2-protocols-oidc.md) - 了解如何使用 OAuth 2.0 (授權通訊協定) 進行驗證。
* [使用用戶端認證的服務對服務呼叫](v2-oauth2-client-creds-grant-flow.md) - 了解如何使用 OAuth 2.0 用戶端認證授與流程來進行服務對服務呼叫。
* [使用代理者流程的服務對服務呼叫](v2-oauth2-on-behalf-of-flow.md) - 了解如何使用 OAuth 2.0 代理者流程來進行服務對服務呼叫。
* [SAML 通訊協定參考](active-directory-saml-protocol-reference.md)-瞭解 Microsoft 身分識別平臺的單一登入和單一登出 SAML 設定檔。

## <a name="see-also"></a>請參閱

* [Microsoft 身分識別平臺總覽](v2-overview.md)
* [Active Directory 程式碼範例](sample-v2-code.md)
