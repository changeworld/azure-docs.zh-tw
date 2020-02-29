---
title: 設定註冊，並以 Weibo 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有微博帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e99b5717cdcc32d8fd138d1edf5fe1bd3283c70e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187894"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以微博帳戶登入

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>建立 Weibo 應用程式

若要在 Azure Active Directory B2C （Azure AD B2C）中使用 Weibo 帳戶做為識別提供者，您需要在代表它的租使用者中建立應用程式。 如果您還沒有 Weibo 帳戶，可以在[https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)註冊。

1. 使用您的微博帳戶認證登入[微博開發人員入口網站](https://open.weibo.com/)。
1. 登入之後，在右上角選取您的顯示名稱。
1. 在下拉式清單中，選取 [编辑开发者信息] \(編輯開發人員資訊)。
1. 輸入必要資訊，並選取 [提交]。
1. 完成電子郵件驗證程序。
1. 移至[識別驗證頁面](https://open.weibo.com/developers/identity/edit)。
1. 輸入必要資訊，並選取 [提交]。

### <a name="register-a-weibo-application"></a>註冊 Weibo 應用程式

1. 移至[新的 Weibo 應用程式註冊頁面](https://open.weibo.com/apps/new)。
1. 輸入必要的應用程式資訊。
1. 選取 [创建]\(建立\)。
1. 複製**應用程式金鑰**和**應用程式密碼**的值。 您需要這兩個值，才能將識別提供者新增至您的租用戶。
1. 上傳所需的相片，並輸入所需的資訊。
1. 選取 [保存以上信息]\(儲存\)。
1. 選取 [高级信息]\(進階資訊\)。
1. 選取 OAuth2.0 [授权设置] (\重新導向 URL\) 旁的 [编辑] \(編輯\)。
1. 針對 OAuth2.0 [授权设置]`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **\(重新導向 URL) 輸入**。 例如，如果您的租用戶名稱是 contoso，請將 URL 設定為 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 選取 [提交]。

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>將微博帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**]，然後選取 **[Weibo （預覽）** ]。
1. 輸入 [名稱]。 例如， *Weibo*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立之 Weibo 應用程式的應用程式金鑰。
1. 針對 [**用戶端密碼**]，輸入您所記錄的應用程式密碼。
1. 選取 [儲存]。
