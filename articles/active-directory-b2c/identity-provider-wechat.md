---
title: 使用微信帳戶設置註冊和登錄
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 WeChat 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184429"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 WeChat 帳戶登入

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>建立 WeChat 應用程式

要在 Azure 活動目錄 B2C （Azure AD B2C） 中使用微信帳戶作為標識提供程式，需要在租戶中創建表示它的應用程式。 如果您還沒有微信帳戶，您可以在 上[https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)獲取資訊。

### <a name="register-a-wechat-application"></a>註冊 WeChat 應用程式

1. [https://open.weixin.qq.com/](https://open.weixin.qq.com/)使用微信憑據登錄。
1. 選取 [管理中心]**** \(管理中心\)。
1. 遵循註冊新應用程式的步驟。
1. 在 [授权回调域]**** \(回呼 URL\) 中，輸入 `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 例如，如果您的租用戶名稱是 contoso，請將 URL 設定為 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 複製**應用程式識別碼**和**應用程式金鑰**。 您需要這些值，才能將識別提供者新增至您的租用戶。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 WeChat 設為識別提供者

1. 以 Azure AD B2C 租戶的全域管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]**** 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選擇**身份供應商**，然後選擇**微信（預覽）。**
1. 輸入**名稱**。 例如，*微信*。
1. 對於**用戶端 ID，** 輸入您之前創建的微信應用程式的 APP ID。
1. 對於**用戶端金鑰**，輸入您錄製的 APP 金鑰。
1. 選取 [儲存]****。
