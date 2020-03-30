---
title: 教程：將標識提供程式添加到應用
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183511"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>教程：在 Azure 活動目錄 B2C 中向應用程式添加標識提供程式

在您的應用程式中，您可以讓使用者使用不同的識別提供者登入。 *識別提供者*可建立、維護及管理身分識別資訊，同時對應用程式提供驗證服務。 可以使用 Azure 門戶將 Azure 活動目錄 B2C （Azure AD B2C） 支援的標識提供程式添加到[使用者流](user-flow-overview.md)中。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立識別提供者應用程式
> * 將識別提供者新增至您的租用戶
> * 將識別提供者新增至您的使用者流程

您在應用程式中通常只會使用一個識別提供者，但您可以選擇新增更多個。 本教學課程說明如何將 Azure AD 識別提供者和 Facebook 識別提供者新增至您的應用程式。 將這兩個這些識別提供者新增至應用程式是選用作業。 您還可以添加其他身份供應商，如[亞馬遜](identity-provider-amazon.md)[、GitHub、](identity-provider-github.md)[谷歌](identity-provider-google.md)[、LinkedIn、](identity-provider-linkedin.md)[微軟](identity-provider-microsoft-account.md)或[推特](identity-provider-twitter.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。

## <a name="create-applications"></a>建立應用程式

識別提供者應用程式會提供識別碼和金鑰，以啟用與 Azure AD B2C 租用戶之間的通訊。 在本節的教學課程中，您將建立 Azure AD 應用程式和 Facebook 應用程式，以從中取得識別碼和金鑰，將識別提供者新增至您的租用戶。 如果您只要新增其中一個識別提供者，則只需建立該提供者的應用程式。

### <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

若要讓使用者可從 Azure AD 登入，您必須在 Azure AD 租用戶內註冊應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [新增註冊]****。
1. 輸入應用程式的名稱。 例如： `Azure AD B2C App` 。
1. **僅接受此組織目錄中針對此應用程式的帳戶**選擇。
1. 對於**重定向 URI**，接受**Web**的值，並在所有小寫字母中輸入以下 URL，`your-B2C-tenant-name`替換為 Azure AD B2C 租戶的名稱。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

    所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。

1. 選擇 **"註冊**"，然後記錄在後面的步驟中使用**的應用程式（用戶端）ID。**
1. 在應用程式功能表中的 **"管理"** 下，選擇**證書&機密**，然後選擇 **"新用戶端機密**"。
1. 輸入用戶端機密**的說明**。 例如： `Azure AD B2C App Secret` 。
1. 選擇過期期限。 對於此申請，接受 1**年中的**選擇。
1. 選擇 **"添加**"，然後記錄在後面的步驟中使用的新用戶端機密的值。

### <a name="create-a-facebook-application"></a>建立 Facebook 應用程式

若要在 Azure AD B2C 中使用 Facebook 帳戶作為識別提供者，您必須在 Facebook 建立應用程式。 如果您還沒有 Facebook 帳戶，您可以在 上[https://www.facebook.com/](https://www.facebook.com/)獲取。

1. 請以您的 Facebook 帳戶認證登入 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/)。
1. 如果您尚未這麼做，您必須註冊為 Facebook 開發人員。 為此，請在頁面右上角選擇 **"開始"，** 接受 Facebook 的政策，並完成註冊步驟。
1. 選擇 **"我的應用**"，然後**創建應用**。
1. 輸入**顯示名稱**和有效的**連絡人電子郵件**。
1. 按一下 [建立應用程式識別碼]****。 這可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
1. 選擇 **"設置** > **基本**"。
1. 選擇**分類**，例如 `Business and Pages`。 此值是 Facebook 所必需的，但 Azure AD B2C 不使用。
1. 在頁面底部選取 [新增平台]****，然後選取 [網站]****。
1. 在 [Site URL]**** \(網站 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/`，並將 `your-tenant-name` 取代為您的租用戶名稱。
1. 輸入**隱私權原則 URL** 的 URL，如 `http://www.contoso.com/`。 隱私政策 URL 是您為應用程式提供隱私資訊而維護的頁面。
1. 選取 [Save Changes] \(儲存變更\)****。
1. 在頁面頂部，記錄**應用 ID**的值。
1. 在**應用機密**旁邊，選擇 **"顯示**並記錄其值"。 您可以使用應用 ID 和應用金鑰將 Facebook 配置為租戶中的標識提供程式。 **應用金鑰**是一個重要的安全憑據，您應該安全地存儲。
1. 選擇**產品**旁邊的加號，然後在 Facebook**登錄下**選擇 **"設置**"。
1. 在左側功能表中的**Facebook 登錄**功能表下，選擇 **"設置**"。
1. 在 [Valid OAuth redirect URIs]**** \(有效的 OAuth 重新導向 URI\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 選擇頁面底部的 **"保存更改**"。
1. 要使 Facebook 應用程式可供 Azure AD B2C 使用，請按一下頁面右上角**的狀態**選擇器，然後將其**打開以公開**應用程式，然後按一下"**確認**"。 此時，狀態應該會從 [開發]**** 變更為 [作用中]****。

## <a name="add-the-identity-providers"></a>新增識別提供者

在您為要新增的識別提供者建立應用程式之後，請將識別提供者新增至您的租用戶。

### <a name="add-the-azure-active-directory-identity-provider"></a>新增 Azure Active Directory 識別提供者

1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選擇**標識提供程式**，然後選擇 **"新建 OpenID 連接提供程式**"。
1. 輸入**名稱**。 例如，輸入 *Contoso Azure AD*。
1. 對於**中繼資料 URL，** 輸入以下 URL`your-AD-tenant-domain`替換為 Azure AD 租戶的功能變數名稱：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如： `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 。

1. 對於**用戶端 ID，** 輸入您以前記錄的應用程式 ID。
1. 對於**用戶端機密**，輸入您以前記錄的用戶端金鑰。
1. 保留**範圍**、**回應類型**和**回應模式**的預設值。
1. （可選）輸入**Domain_hint**的值。 例如 *，ContosoAD*。 [域提示](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)是包含在來自應用程式的身份驗證請求中的指令。 它們可用來加快使用者進入其同盟 IdP 登入頁面的速度。 或者，多租用戶應用程式也可使用網域提示來加速使用者直接前往其租用戶的品牌化 Azure AD 登入頁面。
1. 在**標識提供程式宣告對應**下，輸入以下宣告對應值：

    * **使用者 ID**： *oid*
    * **顯示名稱**：*名稱*
    * **給定名稱**： *given_name*
    * **姓氏**： *family_name*
    * **電子郵件**： *unique_name*

1. 選取 [儲存]****。

### <a name="add-the-facebook-identity-provider"></a>新增 Facebook 識別提供者

1. 選擇**身份供應商**，然後選擇**Facebook**。
1. 輸入**名稱**。 例如 *，Facebook*。
1. 對於**用戶端 ID，** 輸入您之前創建的 Facebook 應用程式的應用 ID。
1. 對於**用戶端金鑰**，輸入您錄製的應用金鑰。
1. 選取 [儲存]****。

## <a name="update-the-user-flow"></a>更新使用者流程

在教學課程中，您已完成必要條件的部分，建立了名為 *B2C_1_signupsignin1* 的註冊和登入使用者流程。 在本節中，您會將識別提供者新增至 *B2C_1_signupsignin1* 使用者流程。

1. 選取 [使用者流程 (原則)]****，然後選取 *B2C_1_signupsignin1* 使用者流程。
2. 選取 [識別提供者]****，然後選取您新增的 **Facebook** 和 **Contoso Azure AD** 識別提供者。
3. 選取 [儲存]****。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 在您所建立使用者流程的 [概觀] 頁面上，選取 [執行使用者流程]****。
1. 針對 [應用程式]****，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 選擇 **"運行使用者流**"，然後使用以前添加的標識提供程式登錄。
1. 針對您新增的其他識別提供者重複步驟 1 到 3。

如果登錄操作成功，您將被重定向到`https://jwt.ms`顯示解碼權杖的標記，類似于：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立識別提供者應用程式
> * 將識別提供者新增至您的租用戶
> * 將識別提供者新增至您的使用者流程

接下來，瞭解如何自訂作為使用者在應用程式中身份體驗的一部分顯示給使用者的頁面的 UI：

> [!div class="nextstepaction"]
> [自訂 Azure 活動目錄 B2C 中應用程式的使用者介面](tutorial-customize-ui.md)
