---
title: 新增身分識別提供者-Azure Active Directory B2C |Microsoft Docs
description: 瞭解如何將身分識別提供者新增至您的 Active Directory B2C 租使用者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 01/04/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e336fe8d8042657329936ed59058ed91109692fc
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861841"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>將身分識別提供者新增至您的 Azure Active Directory B2C 租使用者

您可以設定 Azure AD B2C，允許使用者使用來自外部社交或企業識別提供者 (IdP) 的認證來登入您的應用程式。 Azure AD B2C 支援外部識別提供者，例如 Facebook、Microsoft 帳戶、Google、Twitter，以及任何支援 OAuth 1.0、OAuth 2.0、OpenID Connect 和 SAML 通訊協定的識別提供者。

透過外部識別提供者同盟，您可讓取用者能夠使用其現有的社交或企業帳戶登入，而不需為您的應用程式建立新的帳戶。

在註冊或登入頁面上，Azure AD B2C 會顯示使用者可選擇用於登入的外部識別提供者清單。 一旦選取其中一個外部識別提供者，系統就會將他們重新導向到所選提供者的網站，以完成登入程序。 使用者成功登入之後，便會回到 Azure AD B2C，以在您的應用程式中驗證帳戶。

![使用社交帳戶 (Facebook) 的行動裝置登入範例](media/add-identity-provider/external-idp.png)

您可以使用 Azure 入口網站，將 Azure Active Directory B2C (Azure AD B2C) 支援的識別提供者新增至您的[使用者流程](user-flow-overview.md)。 您也可以將身分識別提供者新增至您的 [自訂原則](custom-policy-get-started.md)。

## <a name="select-an-identity-provider"></a>選取身分識別提供者

您在應用程式中通常只會使用一個識別提供者，但您可以選擇新增更多個。 下列操作說明文章會示範如何建立身分識別提供者應用程式、將身分識別提供者新增至您的租使用者，以及將身分識別提供者新增至您的使用者流程或自訂原則。

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Azure AD (單一租用戶)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (多租用戶)](identity-provider-azure-ad-multi-tenant.md)
* [Facebook](identity-provider-facebook.md)
* [一般識別提供者](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft 帳戶](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [微信](identity-provider-wechat.md)
* [微博](identity-provider-weibo.md)
