---
title: 設定 Azure AD 驗證
description: 瞭解如何將 Azure Active Directory authentication 設定為 App Service 或 Azure Functions 應用程式的身分識別提供者。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 913aac7755e6c4f9a4b42d45933728fcc8840bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190005"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>設定 App Service 或 Azure Functions 應用程式以使用 Azure AD 登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文說明如何將 Azure App Service 或 Azure Functions 設定為使用 Azure Active Directory （Azure AD）做為驗證提供者。

> [!NOTE]
> 快速設定流程會設定 AAD V1 應用程式註冊。 如果您想要使用[Azure Active Directory](../active-directory/develop/v2-overview.md) v2.0 （包括[MSAL](../active-directory/develop/msal-overview.md)），請遵循[advanced configuration 指示](#advanced)。

在設定您的應用程式和驗證時，請遵循下列最佳作法：

- 為每個 App Service 應用程式提供自己的許可權和同意。
- 使用自己的註冊來設定每個 App Service 應用程式。
- 在不同的部署位置使用個別的應用程式註冊，以避免在環境之間共用許可權。 測試新的程式碼時，這種作法有助於防止問題影響生產應用程式。

> [!NOTE]
> 這項功能目前無法在 Azure Functions 的 Linux 使用量方案上使用

## <a name="configure-with-express-settings"></a><a name="express"> </a>使用快速設定來設定

> [!NOTE]
> [**快速**] 選項無法供政府雲端使用。 

1. 在 [ [Azure 入口網站]中，搜尋並選取 [**應用程式服務**]，然後選取您的應用程式。
2. 在左側導覽中，選取 [**驗證/授權** > **]。**
3. 選取 [ **Azure Active Directory** > **Express**]。

   如果您想要改為選擇現有的應用程式註冊：

   1. 選擇 [**選取現有的 AD 應用程式**]，然後按一下 [ **Azure AD App**]。
   2. 選擇現有的應用程式註冊，然後按一下 **[確定]**。

3. 選取 [確定]**** 以在 Azure Active Directory 中註冊 App Service 應用程式。 隨即會建立新的應用程式註冊。
   
    ![Azure Active Directory 中的快速設定](./media/configure-authentication-provider-aad/express-settings.png)
   
4. 選擇性根據預設，App Service 會提供驗證，但不會限制對您網站內容和 Api 的已授權存取。 您必須在應用程式程式碼中授權使用者。 若要將應用程式存取限制為僅由 Azure Active Directory 驗證的使用者，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Azure Active Directory 登入**]。 當您設定此功能時，您的應用程式會要求所有要求都必須經過驗證。 它也會將所有未驗證的重新導向至 Azure Active Directory 進行驗證。

    > [!CAUTION]
    > 以這種方式限制存取適用于對您應用程式的所有呼叫，這對於具有公開可用首頁的應用程式（如許多單頁應用程式），可能不是理想的做法。 對於這類應用程式，可能會慣用 [**允許匿名要求（無動作）** ]，而應用程式會以手動方式啟動登入本身。 如需詳細資訊，請參閱[驗證流程](overview-authentication-authorization.md#authentication-flow)。
5. 選取 [儲存]  。

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>使用進階設定來設定

如果您想要使用來自不同 Azure AD 租使用者的應用程式註冊，您可以手動設定應用程式設定。 若要完成此自訂設定：

1. 在 Azure AD 中建立註冊。
2. 提供 App Service 的一些註冊詳細資料。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>在 Azure AD 中，為您的 App Service 應用程式建立應用程式註冊

當您設定 App Service 應用程式時，您將需要下列資訊：

- 用戶端識別碼
- 租用戶識別碼
- 用戶端密碼（選擇性）
- 應用程式識別碼 URI

執行下列步驟：

1. 登入[Azure 入口網站]，搜尋並選取 [**應用程式服務**]，然後選取您的應用程式。 請記下您應用程式的**URL**。 您將使用它來設定您的 Azure Active Directory 應用程式註冊。
1. 選取 [ **Azure Active Directory** > **應用程式註冊** > **新增註冊**]。
1. 在 [**註冊應用程式**] 頁面中，輸入應用程式註冊的**名稱**。
1. 在 [重新**導向 URI**] 中， `<app-url>/.auth/login/aad/callback`選取 [ **Web** ]，然後輸入。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 
1. 選取 [建立]  。
1. 建立應用程式註冊之後，請複製**應用程式（用戶端）識別碼**和**目錄（租使用者）識別碼**，以供稍後查看。
1. 選取 [驗證]****。 在 **[隱含授**與] 底下，啟用 [**識別碼權杖**] 以允許 OpenID connect 使用者從 App Service 登入。
1. 選擇性選取 [**商標**]。 在 [**首頁 url**] 中，輸入 App Service 應用程式的 URL，然後選取 [**儲存**]。
1. 選取 [**公開 API** > **集合**]。 對於單一租使用者應用程式，請貼上 App Service 應用程式的 URL，然後選取 [**儲存**] 和 [針對多租使用者應用程式]，貼上以其中一個租使用者驗證的網域為基礎的 url，然後選取 [**儲存**]。

   > [!NOTE]
   > 此值是應用**程式註冊的應用程式識別碼 URI** 。 如果您的 web 應用程式需要存取雲端中的 API，當您設定雲端 App Service 資源時，您需要 web 應用**程式的應用程式識別碼 URI** 。 例如，如果您想要讓雲端服務明確授與 web 應用程式的存取權，您可以使用此範例。

1. 選取 [**新增範圍**]。
   1. 在 [**範圍名稱**] 中，輸入*user_impersonation*。
   1. 在文字方塊中，輸入您希望使用者在同意頁面上看到的同意範圍名稱和描述。 例如，輸入*存取我的應用程式*。 
   1. 選取 [**新增領域**]。
1. 選擇性若要建立用戶端密碼，請選取 [**憑證] & 秘密** > **新的用戶端密碼** > **新增]**。 複製頁面中顯示的 [用戶端密碼] 值。 它將不會再次顯示。
1. 選擇性若要新增多個**回復 url**，請選取 [**驗證**]。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>在您的 App Service 應用程式中啟用 Azure Active Directory

1. 在 [ [Azure 入口網站]中，搜尋並選取 [**應用程式服務**]，然後選取您的應用程式。 
1. 在左窗格的 [**設定**] 底下，選取 [**驗證/授權** > **開啟**]。
1. 選擇性根據預設，App Service authentication 會允許未經驗證的存取您的應用程式。 若要強制執行使用者驗證，請將 [**要求未經驗證時所採取的動作**] 設定為 [**使用 Azure Active Directory 登入**]。
1. 在 [驗證提供者]**** 底下，選取 [Azure Active Directory]****。
1. 在 [**管理模式]** 中，選取 [ **Advanced** ]，並根據下表設定 App Service 驗證：

    |欄位|描述|
    |-|-|
    |用戶端識別碼| 使用應用**程式註冊的應用程式（用戶端）識別碼**。 |
    |簽發者 Url| 使用`<authentication-endpoint>/<tenant-id>/v2.0`，並將* \<驗證端點>* 取代為[雲端環境的驗證端點](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)（例如，"https://login.microsoft.com" 適用于全域 Azure），同時以建立應用程式註冊所在的**目錄（租使用者）識別碼**取代* \<租使用者識別碼>* 。 這個值是用來將使用者重新導向至正確的 Azure AD 租使用者，以及下載適當的中繼資料來判斷適當的權杖簽署金鑰和權杖簽發者宣告值（例如）。 使用`/v2.0` AAD v1 的應用程式可能會省略區段。 |
    |用戶端密碼（選擇性）| 使用您在應用程式註冊中產生的用戶端密碼。|
    |允許的權杖物件| 如果這是雲端或伺服器應用程式，而且您想要允許來自 web 應用程式的驗證權杖，請在這裡新增 web 應用程式的**應用程式識別碼 URI** 。 設定的**用戶端識別碼***一律*會隱含地視為允許的物件。 |

2. 選取 [確定]****，然後選取 [儲存]****。

您現在已經準備好在 App Service 應用程式中使用 Azure Active Directory 進行驗證。

## <a name="configure-a-native-client-application"></a>設定原生用戶端應用程式

您可以使用**Active Directory 驗證程式庫**之類的用戶端程式庫，註冊原生用戶端，以允許對應用程式中裝載的 Web API 進行驗證。

1. 在 [ [Azure 入口網站]中，選取 [ **Active Directory** > **應用程式註冊** > **新增註冊**]。
1. 在 [**註冊應用程式**] 頁面中，輸入應用程式註冊的**名稱**。
1. 在 [重新**導向 URI**] 中，選取 [**公用用戶端（行動 & 桌面）** ]，然後輸入 URL `<app-url>/.auth/login/aad/callback`。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。

    > [!NOTE]
    > 若為 Microsoft Store 應用程式，請改用[封裝 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作為 URI。
1. 選取 [建立]  。
1. 建立應用程式註冊之後，複製 [**應用程式（用戶端）識別碼**] 的值。
1. 選取 [ **api 許可權** > ] [**新增許可權** > ] [**我的 api**]。
1. 選取您稍早為 App Service 應用程式建立的應用程式註冊。 如果您沒有看到應用程式註冊，請確定您已在 App Service 應用程式的 Azure AD 中，新增 [[建立應用程式註冊](#register)] 中的**user_impersonation**範圍。
1. 選取 [ **user_impersonation**]，然後選取 [**新增許可權**]。

您現在已設定可代表使用者存取 App Service 應用程式的原生用戶端應用程式。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
