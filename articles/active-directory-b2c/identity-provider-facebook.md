---
title: 設定註冊，並以 Facebook 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Facebook 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 02c3890a9aca03d9d79b55098297174401cab37d
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98537952"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Facebook 帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>建立 Facebook 應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中讓具有 Facebook 帳戶的使用者登入，您需要在 [Facebook 應用程式儀表板](https://developers.facebook.com/)中建立應用程式。 如需詳細資訊，請參閱 [應用程式開發](https://developers.facebook.com/docs/development)。 如果您還沒有 Facebook 帳戶，可以在 [https://www.facebook.com/](https://www.facebook.com/) 註冊。

1. 請以您的 Facebook 帳戶認證登入 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/)。
1. 如果您尚未這麼做，您需要註冊為 Facebook 開發人員。 若要這樣做，請選取頁面右上角的 [開始]，接受 Facebook 的原則，然後完成註冊步驟。
1. 選取 [我的應用程式]，然後選取 [建立應用程式]。
1. 選取 **組建連線體驗**。
1. 輸入 **顯示名稱** 和有效的 **連絡人電子郵件**。
1. 選取 [建立應用程式識別碼]。 這可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
1. 選取 [設定] > [基本]。
    1. 選擇 **分類**，例如 `Business and Pages`。 此為 Facebook 所需的值，但不會用於 Azure AD B2C。
    1. 輸入 **服務條款 url** 的 url，例如 `http://www.contoso.com/tos` 。 原則 URL 是您維護的頁面，用來提供應用程式的條款及條件。
    1. 輸入 **隱私權原則 URL** 的 URL，如 `http://www.contoso.com/privacy`。 原則 URL 是您需維護以提供應用程式隱私權資訊的網頁。
1. 在頁面底部選取 [新增平台]，然後選取 [網站]。
1. 在 [ **網站 URL**] 中，輸入您網站的位址（例如） `https://contoso.com` 。 
1. 選取 [Save Changes] \(儲存變更\)。
1. 在頁面頂端複製 [應用程式識別碼] 的值。
1. 選取 [顯示]，並複製 [應用程式密碼] 的值。 您必須同時使用這兩個值，將 Facebook 設定為租用戶中的身分識別提供者。 **應用程式密碼** 是重要的安全性認證。
1. 從功能表選取 [**產品**] 旁的 **加號**。 在 [**將產品新增至您的應用程式**] 底下，選取 [在 **Facebook 登** 入下 **設定**]。
1. 從功能表選取 [ **Facebook 登** 入]，然後選取 [ **設定**]。
1. 在 [Valid OAuth redirect URIs] \(有效的 OAuth 重新導向 URI\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 選取頁面底部的 [儲存變更]。
1. 若要將您的 Facebook 應用程式提供給 Azure AD B2C，請選取頁面右上方的狀態選取器，並 **將它開啟** 以使應用程式成為公用，然後選取 [ **切換模式]**。  此時，狀態應該會從 [開發] 變更為 [作用中]。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>將 Facebook 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [識別提供者]，然後選取 [Facebook]。
1. 輸入 [名稱]。 例如，Facebook。
1. 在 [用戶端識別碼] 中，輸入稍早所建立 Facebook 應用程式的應用程式識別碼。
1. 在 [用戶端密碼] 中，輸入您記下的應用程式祕密。
1. 選取 [儲存]。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-facebook-as-an-identity-provider"></a>將 Facebook 新增為識別提供者

1. 在 `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 檔案中，將 `client_id` 的值取代為 Facebook 應用程式識別碼：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>將 Facebook 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要新增 Facebook 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 **Facebook**。
1. 選取 [儲存]。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="upload-and-test-the-policy"></a>上傳並測試原則

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 將 *TrustFrameworkExtensions.xml* 檔案上傳至您的租用戶。
1. 在 [自訂原則] 下，選取 [B2C_1A_signup_signin]。
1. 針對 [ **選取應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 依序選取 [立即執行]、[Facebook]，以使用 Facebook 登入並測試自訂原則。

::: zone-end

## <a name="next-steps"></a>後續步驟

瞭解如何將 [Facebook 權杖傳遞至您的應用程式](idp-pass-through-user-flow.md)。
