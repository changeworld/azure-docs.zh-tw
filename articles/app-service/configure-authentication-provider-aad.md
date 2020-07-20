---
title: 設定 Azure AD 驗證
description: 了解如何將 Azure Active Directory 驗證設定為 App Service 或 Azure Functions 應用程式的識別提供者。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: c3892cfe3f8bd6966f5bd00c0747590eef3bc50d
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860509"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>將 App Service 或 Azure Functions 應用程式設定為使用 Azure AD 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文說明如何將 App Service 或 Azure Functions 應用程式設定為使用 Azure Active Directory (Azure AD) 作為驗證提供者。

> [!NOTE]
> 快速設定流程會設定 AAD V1 應用程式註冊。 如果您想要使用 [Azure Active Directory v2.0](../active-directory/develop/v2-overview.md) (包括 [MSAL](../active-directory/develop/msal-overview.md))，請遵循[進階設定指示](#advanced)。

在設定您的應用程式和驗證時，請遵循下列最佳做法：

- 為每個 App Service 應用程式提供其自己的權限並予以同意。
- 使用自己的註冊來設定每個 App Service 應用程式。
- 對不同的部署位置使用不同的應用程式註冊，以避免在環境之間共用權限。 測試新的程式碼時，這種做法有助於避免問題影到響生產應用程式。

> [!NOTE]
> 這項功能目前不適用於 Azure Functions 的 Linux 使用量方案

## <a name="configure-with-express-settings"></a><a name="express"></a>透過快速設定進行設定

> [!NOTE]
> 政府雲端無法使用 [快速] 選項。

1. 在 [Azure 入口網站]中，搜尋並選取 [應用程式服務]，然後選取您的應用程式。
2. 在左側瀏覽區中，選取 [驗證 / 授權] > [開啟]。
3. 選取 [Azure Active Directory] > [快速]。

   如果您想要改為選擇現有的應用程式註冊：

   1. 選擇 [選取現有的 AD 應用程式]，然後按一下 [Azure AD 應用程式]。
   2. 選擇現有的應用程式註冊，然後按一下 [確定]。

3. 選取 [確定] 以在 Azure Active Directory 中註冊 App Service 應用程式。 已建立新的應用程式。

    ![Azure Active Directory 中的快速設定](./media/configure-authentication-provider-aad/express-settings.png)

4. (選擇性) App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。 若要限制只有經由 Azure Active Directory 授權的使用者可以存取應用程式，請將 [要求未經驗證時所採取的動作] 設為 [使用 Azure Active Directory 登入]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 其也會將所有未驗證的要求重新導向至 Azure Active Directory 進行驗證。

    > [!CAUTION]
    > 以這種方式限制存取，適用於對您應用程式的所有呼叫，不建議具有公開可用首頁的應用程式 (如許多單頁應用程式) 這麼做。 對於這類應用程式，您可能偏好 [允許匿名要求 (不執行動作)]，應用程式會手動開始登入本身。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。
5. 選取 [儲存]。

## <a name="configure-with-advanced-settings"></a><a name="advanced"></a> 使用進階設定進行設定

如果您想使用來自不同 Azure AD 租用戶的應用程式註冊，可以手動設定應用程式設定。 若要完成此自訂設定：

1. 在 Azure AD 中建立註冊。
2. 將一些註冊詳細資料提供給 App Service。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>在 Azure AD 中為 App Service 應用程式建立應用程式註冊

當您設定 App Service 應用程式時，您需要下列資訊：

- 用戶端識別碼
- 租用戶識別碼
- 用戶端秘密 (選用)
- 應用程式識別碼 URI

執行下列步驟：

1. 登入 [Azure 入口網站]，搜尋並選取 [應用程式服務]，然後選取您的應用程式。 請記下您應用程式的 **URL**。 您將使用該資訊來設定 Azure Active Directory 應用程式註冊。
1. 選取 [Azure Active Directory] > [應用程式註冊] > [新註冊]。
1. 在 [註冊應用程式] 頁面上，輸入您應用程式註冊的 [名稱]。
1. 在 [重新導向 URI] 中，選取 [Web]，然後輸入 `<app-url>/.auth/login/aad/callback`。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。
1. 選取 [建立]。
1. 建立應用程式註冊之後，請複製 [應用程式 (用戶端) 識別碼] 和 [目錄 (租用戶) 識別碼] 以供稍後使用。
1. 選取 [驗證]。 在 [隱含授與] 底下，啟用 [識別碼權杖] 以允許 OpenID Connect 使用者從 App Service 登入。
1. (選用) 選取 [品牌]。 在 [首頁 URL] 中，輸入您的 App Service 應用程式 URL，然後選取 [儲存]。
1. 選取 [公開 API] > [設定]。 對於單一租用戶應用程式，請貼入您的 App Service 應用程式 URL 並選取 [儲存]，對於多租用戶應用程式，請貼入以其中一個租用戶驗證的網域為基礎的 URL，然後選取 [儲存]。

   > [!NOTE]
   > 此值是應用程式註冊的 [應用程式識別碼 URI]。 如果 Web 應用程式需要存取雲端的 API，則在設定雲端 App Service 資源時，需要 Web 應用程式的 [應用程式識別碼 URI]。 例如，如果想要讓雲端服務明確授與 Web 應用程式的存取權，您可以使用此 URI。

1. 選取 [新增範圍]。
   1. 在 [範圍名稱] 中，輸入 *user_impersonation*。
   1. 在文字方塊中，輸入您希望使用者在同意頁面上看到的同意範圍名稱和描述。 例如，輸入「存取我的應用程式」。
   1. 選取 [新增範圍]。
1. (選擇性) 若要建立用戶端秘密，請選取 [憑證和秘密] > [新增用戶端秘密] > [新增]。 複製頁面中顯示的用戶端秘密值。 它將不會再次顯示。
1. (選擇性) 若要新增多個 [回覆 URL]，請選取 [驗證]。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>在您的 App Service 應用程式中啟用 Azure Active Directory

1. 在 [Azure 入口網站]中，搜尋並選取 [應用程式服務]，然後選取您的應用程式。
1. 在左窗格的 [設定] 之下，選取 [驗證 / 授權] > [開啟]。
1. (選擇性) 根據預設，App Service 驗證允許未經驗證存取您的應用程式。 若要強制進行使用者驗證，請將 [當要求未經驗證時所要採取的動作] 設定為 [使用 Azure Active Directory 登入]。
1. 在 [驗證提供者] 底下，選取 [Azure Active Directory]。
1. 在 [管理模式] 中，選取 [進階]並根據下表設定 App Service 驗證：

    |欄位|描述|
    |-|-|
    |用戶端識別碼| 使用應用程式註冊的 [應用程式 (用戶端) 識別碼]。 |
    |簽發者 URL| 使用 `<authentication-endpoint>/<tenant-id>/v2.0`，以[您的雲端環境驗證端點](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)(例如，"https://login.microsoft.com " 適用於全域 Azure) 取代 *\< authentication-endpoint>* ，並以應用程式註冊建立所在的 **[目錄 (租用戶) 識別碼]** 取代 *\< tenant-id>* 。 此值用來將使用者重新導向至正確的 Azure AD 租用戶，以及下載適當的中繼資料，以判斷適當的權杖簽署金鑰和權杖簽發者宣告值 (舉例而言)。 使用 AAD v1 的應用程式可能會省略 `/v2.0` 區段。 |
    |用戶端秘密 (選用)| 使用您在應用程式註冊中產生的用戶端秘密。|
    |允許的權杖對象| 如果這是雲端或伺服器應用程式，而且您想允許來自 Web 應用程式的驗證權杖，請在這裡新增 Web 應用程式的 [應用程式識別碼 URI]。 所設定的 [用戶端識別碼]一律會被隱含地視為允許的對象。 |

2. 選取 [確定]，然後選取 [儲存]。

現在，您已可在 App Service 應用程式中使用 Azure Active Directory 進行驗證。

## <a name="configure-a-native-client-application"></a>設定原生用戶端應用程式

您可以註冊原生用戶端，以允許使用用戶端程式庫 (例如 **Active Directory 驗證程式庫**) 向您應用程式中裝載的 Web API 進行驗證。

1. 在 [Azure 入口網站]中，選取 [Active Directory] > [應用程式註冊] > [新增註冊]。
1. 在 [註冊應用程式] 頁面上，輸入您應用程式註冊的 [名稱]。
1. 在 [重新導向 URI] 中，選取 [公用用戶端 (行動和傳統型)] 並輸入 URL `<app-url>/.auth/login/aad/callback`。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。

    > [!NOTE]
    > 若是 Microsoft Store 應用程式，請改為使用[套件 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作為 URI。
1. 選取 [建立]。
1. 建立應用程式註冊之後，複製 [應用程式 (用戶端) 識別碼] 的值。
1. 選取 [API 權限] > [新增權限] > [我的 API]。
1. 選取您稍早為 App Service 應用程式建立的應用程式註冊。 如果沒看到應用程式註冊，請確定您已在[在 Azure AD 中為 App Service 應用程式建立應用程式註冊](#register)中新增了 **user_impersonation** 範圍。
1. 在 [委派權限] 底下，選取 [user_impersonation]，然後選取 [新增權限]。

您現在已設定了可代表使用者存取您 App Service 應用程式的原生用戶端應用程式。

## <a name="configure-a-daemon-client-application-for-service-to-service-calls"></a>設定服務對服務呼叫的精靈用戶端應用程式

您的應用程式可以取得權杖以代表其本身 (而非代表使用者) 呼叫裝載於 App Service 或函式應用程式中的 Web API。 此案例適用於未使用已登入使用者來執行工作的非互動式精靈應用程式。 其會使用標準 OAuth 2.0 [用戶端認證](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)授與。

1. 在 [Azure 入口網站]中，選取 [Active Directory] > [應用程式註冊] > [新增註冊]。
1. 在 [註冊應用程式] 頁面上，輸入精靈應用程式註冊的 [名稱]。
1. 若為精靈應用程式，您不需要重新導向 URI，因此可以保留空白。
1. 選取 [建立]。
1. 建立應用程式註冊之後，複製 [應用程式 (用戶端) 識別碼] 的值。
1. 選取 [憑證及祕密] > [新增用戶端密碼] > [新增]。 複製頁面中顯示的用戶端秘密值。 它將不會再次顯示。

您現在可以[使用用戶端識別碼和用戶端密碼來要求存取權杖](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)，方法是將 `resource` 參數設定為目標應用程式的**應用程式識別碼 URI**。 接著，您可以使用標準 [OAuth 2.0 授權標頭](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)，將產生的存取權杖呈現給目標應用程式，然後 App Service 驗證/授權就會像往常一樣驗證並使用權杖，但現在會指出呼叫端 (在此案例中為應用程式，而非使用者) 已通過驗證。

目前，這可讓您在 Azure AD 租用戶中的_任何_用戶端應用程式要求存取權杖，並向目標應用程式進行驗證。 如果您也想要強制讓_授權_僅允許特定的用戶端應用程式，就必須執行一些額外的設定。

1. 在應用程式註冊 (代表您要保護的 App Service 或函式應用程式) 的資訊清單中[定義應用程式角色](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)。
1. 在應用程式註冊 (代表需要授權的用戶端) 上，選取 [API 權限] > [新增權限] > [我的 API]。
1. 選取您稍早建立的應用程式註冊。 如果您沒有看到應用程式註冊，請確定您已[新增應用程式角色](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)。
1. 在 [應用程式權限] 底下，選取您稍早建立的應用程式角色，然後選取 [新增權限]。
1. 確實按一下 [授與管理員同意]，以授權用戶端應用程式要求權限。
1. 類似於上一個案例 (在新增任何角色之前)，您現在可以針對相同的目標 `resource` [要求存取權杖](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)，而且存取權杖將會包含 `roles` 宣告，內含已授權給用戶端應用程式的應用程式角色。
1. 在目標 App Service 或函式應用程式的程式碼內，您現在可以驗證預期的角色是否存在於權杖中 (這不會由 App Service 驗證/授權來執行)。 如需詳細資訊，請參閱[存取使用者宣告](app-service-authentication-how-to.md#access-user-claims)。

您現在已設定可使用自己的身分識別來存取 App Service 應用程式的精靈用戶端應用程式。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [教學課程：在 Azure App Service 中對使用者進行端對端驗證和授權](app-service-web-tutorial-auth-aad.md)
<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
