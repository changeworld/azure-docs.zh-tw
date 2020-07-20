---
title: 使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入
description: 使用 Azure Active Directory B2C，讓具有 QQ 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d230bc8a1e9bf388e1cca4e3a3a691223146d734
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387978"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>建立 QQ 應用程式

若要在 Azure Active Directory B2C （Azure AD B2C）中使用 QQ 帳戶做為識別提供者，您需要在代表它的租使用者中建立應用程式。 如果您還沒有 QQ 帳戶，可以在註冊 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) 。

### <a name="register-for-the-qq-developer-program"></a>註冊 QQ 開發人員計劃

1. 使用您的 QQ 帳戶認證登入 [QQ 開發人員入口網站](http://open.qq.com)。
1. 登入之後，請移至， [https://open.qq.com/reg](https://open.qq.com/reg) 將您自己註冊為開發人員。
1. 選取 [个人]**** \(個人開發人員\)。
1. 輸入必要資訊，並選取 [下一步]**** \(下一步\)。
1. 完成電子郵件驗證程序。 在註冊為開發人員之後，您必須等待數天以進行核准。

### <a name="register-a-qq-application"></a>註冊 QQ 應用程式

1. 移至 [https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
1. 選取 [应用管理]**** \(應用程式管理\)。
1. 選取 [创建应用]**** \(建立應用程式\)，然後輸入所需的資訊。
1. 在 [授权回调域]**** \(回呼 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso，請將 URL 設為 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 選取 [创建应用]**** \(建立應用程式\)。
1. 在 [確認] 頁面上，選取 [应用管理]**** \(應用程式管理\) 以返回應用程式管理頁面。
1. 在您建立的應用程式旁邊，選取 [查看]**** \(檢視\)。
1. 選取 [修改]**** \(編輯\)。
1. 複製**應用程式識別碼**和**應用程式金鑰**。 您需要這兩個值，才能將識別提供者新增至您的租用戶。

## <a name="configure-qq-as-an-identity-provider"></a>將 QQ 設為識別提供者

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**]，然後選取 **[QQ （預覽）**]。
1. 輸入 [名稱]。 例如， *QQ*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立之 QQ 應用程式的 [應用程式識別碼]。
1. 針對 [**用戶端密碼**]，輸入您所記錄的應用程式金鑰。
1. 選取 [儲存]。
