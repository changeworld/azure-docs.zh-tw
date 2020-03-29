---
title: 使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入
description: 使用 Azure Active Directory B2C，讓具有 QQ 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187982"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>建立 QQ 應用程式

要在 Azure 活動目錄 B2C（Azure AD B2C）中使用 QQ 帳戶作為標識提供程式，需要在租戶中創建表示它的應用程式。 如果您還沒有 QQ 帳戶，則可以在 註冊[https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)。

### <a name="register-for-the-qq-developer-program"></a>註冊 QQ 開發人員計劃

1. 使用您的 QQ 帳戶認證登入 [QQ 開發人員入口網站](http://open.qq.com)。
1. 登錄後，轉到[https://open.qq.com/reg](https://open.qq.com/reg)註冊自己為開發人員。
1. 選取 [个人]**** \(個人開發人員\)。
1. 輸入必要資訊，並選取 [下一步]**** \(下一步\)。
1. 完成電子郵件驗證程序。 在註冊為開發人員之後，您必須等待數天以進行核准。

### <a name="register-a-qq-application"></a>註冊 QQ 應用程式

1. 轉到[https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
1. 選取 [应用管理]**** \(應用程式管理\)。
1. 選取 [创建应用]**** \(建立應用程式\)，然後輸入所需的資訊。
1. 在 [授权回调域]**** \(回呼 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso，請將 URL 設為 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 選取 [创建应用]**** \(建立應用程式\)。
1. 在 [確認] 頁面上，選取 [应用管理]**** \(應用程式管理\) 以返回應用程式管理頁面。
1. 在您建立的應用程式旁邊，選取 [查看]**** \(檢視\)。
1. 選取 [修改]**** \(編輯\)。
1. 複製**應用程式識別碼**和**應用程式金鑰**。 您需要這兩個值，才能將識別提供者新增至您的租用戶。

## <a name="configure-qq-as-an-identity-provider"></a>將 QQ 設為識別提供者

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 在 Azure 門戶中，搜索並選擇**Azure AD B2C**。
1. 選擇**身份提供程式**，然後選擇**QQ（預覽版）。**
1. 輸入**名稱**。 例如 *，QQ*。
1. 對於**用戶端 ID，** 輸入您之前創建的 QQ 應用程式的 APP ID。
1. 對於**用戶端金鑰**，輸入您錄製的 APP 金鑰。
1. 選取 [儲存]****。
