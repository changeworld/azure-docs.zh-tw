---
title: 設定註冊，並以 WeChat 帳戶登入
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184429"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 WeChat 帳戶登入

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>建立 WeChat 應用程式

若要在 Azure Active Directory B2C （Azure AD B2C）中使用 WeChat 帳戶做為識別提供者，您需要在代表它的租使用者中建立應用程式。 如果您還沒有 WeChat 帳戶，可以在[https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)取得相關資訊。

### <a name="register-a-wechat-application"></a>註冊 WeChat 應用程式

1. 使用您的 WeChat 認證登入 [https://open.weixin.qq.com/](https://open.weixin.qq.com/)。
1. 選取 [管理中心]\(管理中心\)。
1. 遵循註冊新應用程式的步驟。
1. 在 [授权回调域]`https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **\(回呼 URL\) 中，輸入** 。 例如，如果您的租用戶名稱是 contoso，請將 URL 設定為 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 複製**應用程式識別碼**和**應用程式金鑰**。 您需要這些值，才能將識別提供者新增至您的租用戶。

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>在租用戶中將 WeChat 設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**]，然後選取 **[WeChat （預覽）** ]。
1. 輸入 [名稱]。 例如， *WeChat*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立之 WeChat 應用程式的 [應用程式識別碼]。
1. 針對 [**用戶端密碼**]，輸入您所記錄的應用程式金鑰。
1. 選取 [儲存]。
