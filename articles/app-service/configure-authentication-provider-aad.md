---
title: 設定 Azure AD 驗證
description: 瞭解如何將 Azure 活動目錄身份驗證配置為應用服務或 Azure 功能應用的標識提供者。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: f625f5df4f33c6516bd5c50f97c52404d76757a0
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024450"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>套用服務或 Azure 功能應用設定為使用 Azure AD 登入名稱

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介紹如何配置 Azure 應用服務或 Azure 函數,以便使用 Azure 活動目錄 (Azure AD) 作為身份驗證提供者。

> [!NOTE]
> 快速設置流設置 AAD V1 應用程式註冊。 如果要使用 Azure[的目錄 v2.0(](../active-directory/develop/v2-overview.md)包括[MSAL),](../active-directory/develop/msal-overview.md)請依照[進階設定說明操作](#advanced)。

設定應用和身份驗證時遵循以下最佳實務:

- 為每個應用服務應用授予其自己的許可權和同意。
- 配置每個應用服務應用,並進行自己的註冊。
- 通過使用單獨的應用註冊來單獨部署槽,避免環境之間的許可權共用。 測試新代碼時,這種做法有助於防止問題影響生產應用。

## <a name="configure-with-express-settings"></a><a name="express"> </a>使用快速設定來設定

> [!NOTE]
> **"快速'** 選項不適用於政府雲。 

1. 在[Azure 門戶]中,搜索並選擇**應用服務**,然後選擇應用。
2. 從左側導航中,選擇 **「身份驗證/授權** > **」。。**
3. 選擇**Azure 的動作目錄** > **快速 。**

   如果要選擇現有應用註冊::

   1. 選擇 **「選擇現有廣告應用程式**」,然後按**下 Azure AD 應用**。
   2. 選擇現有應用程式註冊,然後單擊 **"確定**"。

3. 選取 [確定]**** 以在 Azure Active Directory 中註冊 App Service 應用程式。 將創建新的應用註冊。
   
    ![Azure 動作目錄中快速設定](./media/configure-authentication-provider-aad/express-settings.png)
   
4. ( 選擇性的 )默認情況下,應用服務提供身份驗證,但不限制對網站內容和 API 的授權訪問。 您必須在應用程式程式碼中授權使用者。 要僅將應用存取許可權限制為 Azure 活動目錄進行身份驗證的使用者,請設置**操作,在未通過 Azure** **活動目錄**進行登錄時執行操作。 設置此功能時,應用需要對所有請求進行身份驗證。 它還將所有未經身份驗證的目錄重定向到 Azure 活動目錄進行身份驗證。

    > [!CAUTION]
    > 以這種方式限制訪問適用於對應用的所有調用,對於具有公開可用主頁的應用(如許多單頁應用程式中)來說,這可能不可取。 對於此類應用程式,可能首選**允許匿名請求(不執行任何操作),** 應用可手動啟動登錄本身。 有關詳細資訊,請參閱[身份驗證流](overview-authentication-authorization.md#authentication-flow)。
5. 選取 [儲存]  。

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>使用進階設定來設定

如果要使用來自其他 Azure AD 租戶的應用註冊,可以手動配置應用設置。 要完成此自定義設定,本文:

1. 在 Azure AD 中建立註冊。
2. 向應用服務提供一些註冊詳細資訊。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>在 Azure AD 中為應用服務應用建立應用註冊

設定應用服務應用時,您需要以下資訊:

- 用戶端識別碼
- 租用戶識別碼
- 用戶端金鑰(選擇性的)
- 應用程式識別碼

執行下列步驟：

1. 登錄到 Azure[門戶],搜索並選擇**應用服務**,然後選擇應用。 請注意應用的**URL。** 您將使用它來配置 Azure 活動目錄應用註冊。
1. 選擇**Azure 的動作目錄** > **套用** > **註冊新註冊**。
1. 在 **「註冊應用程式**」 頁中,輸入應用程式**的名稱**。
1. 在**重定向 URI**`<app-url>/.auth/login/aad/callback`中,選擇**Web**鍵入 。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 
1. 選取 [建立]  。
1. 創建應用註冊後,請複製**應用程式(用戶端)ID**和**目錄(租戶)ID,** 以便以後使用。
1. 選取 [驗證]****。 在**隱式授予**下,啟用**ID 權杖**以允許 App 服務中的 OpenID 連接用戶登入。
1. ( 選擇性的 )選擇**品牌**。 在**主頁網址**中,輸入應用服務應用的網址 並選擇「**儲存**」 。
1. 選擇 **「公開 API** > **集**」。 對於單租戶應用,請粘貼到應用服務應用的 URL 中,然後選擇 **"保存**"和"多租戶應用",將貼上到基於租戶驗證域的 URL 中,然後選擇"**保存**"。

   > [!NOTE]
   > 此值是應用註冊**的應用程式 ID URI。** 如果 Web 應用需要存取雲中的 API,則在配置雲端應用服務資源時需要 Web 應用程式**的應用程式 ID URI。** 例如,如果您希望雲服務顯式授予對 Web 應用的訪問許可權,則可以使用此方法。

1. 選擇 **「添加範圍**」。
   1. 在 **「範圍」 名稱**中,輸入*user_impersonation*。
   1. 在文字框中,輸入您希望使用者在同意頁上查看的同意範圍名稱和說明。 例如,輸入 *「訪問我的應用*」。 
   1. 選擇 **「添加範圍**」。
1. ( 選擇性的 )要建立客戶端機密,請選擇**憑證&機密** > **新用戶端機密** > **添加**。 複製頁面中顯示的用戶端機密值。 它將不會再次顯示。
1. ( 選擇性的 )要新增多個**回覆 URL,** 請選擇 **「身份驗證**」。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>在應用程式服務應用中開啟 Azure 的活動目錄

1. 在[Azure 門戶]中,搜索並選擇**應用服務**,然後選擇應用。 
1. 在左邊窗格中,在 **「設定」** 下,選擇 **「身份驗證/授權** > **」 。**
1. ( 選擇性的 )默認情況下,應用服務身份驗證允許對應用進行未經身份驗證的訪問。 要強制實施使用者身份驗證,請設定在未透過 Azure**的目錄****登入的要求時執行的作業**。
1. 在 [驗證提供者]**** 底下，選取 [Azure Active Directory]****。
1. 在**管理模式中**,根據下表選擇 **「進階**」和「應用服務身份驗證」

    |欄位|描述|
    |-|-|
    |用戶端識別碼| 使用應用註冊**的應用程式(用戶端)ID。** |
    |發行者網址| 使用`<authentication-endpoint>/<tenant-id>/v2.0`並將*\<身份驗證終結點>* 替換為[雲環境的身份驗證終結點](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)(例如,"全域 Azure"),https://login.microsoft.com還將*\<租戶 id>* 替換為創建應用註冊的**目錄(租戶)ID。** 此值用於將使用者重定向到正確的 Azure AD 租戶,以及下載相應的元數據以確定適當的權杖簽名金鑰和權杖頒發者聲明值。 對於`/v2.0`使用 AAD v1 的應用程式,可以省略該部分。 |
    |用戶端金鑰(選擇性的)| 使用在應用註冊中生成的用戶端密鑰。|
    |允許的權杖存取群體| 如果這是雲端或伺服器應用,並且希望允許來自 Web 應用程式驗證權杖,請在此處添加 Web 應用程式**ID URI。** 配置的**用戶端ID***始終*隱式被視為允許的訪問群體。 |

2. 選取 [確定]****，然後選取 [儲存]****。

現在,您已準備好在應用服務應用中使用 Azure 活動目錄進行身份驗證。

## <a name="configure-a-native-client-application"></a>設定原生用戶端應用程式

您可以註冊本機用戶端,以允許使用用戶端庫(如**活動目錄身份驗證庫**)對應用中託管的 Web API 進行身份驗證。

1. 在[Azure 門戶]中,選擇**活動目錄** > **應用註冊** > **「新註冊**」。。
1. 在 **「註冊應用程式**」 頁中,輸入應用程式**的名稱**。
1. 在**重定向 URI**中,選擇**公共用戶端(移動&桌面)** 並鍵入`<app-url>/.auth/login/aad/callback`URL 。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。

    > [!NOTE]
    > 對於 Microsoft 應用商店應用程式,請使用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作為 URI。
1. 選取 [建立]  。
1. 創建應用註冊後,複製**應用程式(用戶端)ID**的值。
1. 選擇**API 權限** > **加入** > **權限的 API**。
1. 選擇之前為應用服務應用創建的應用註冊。 如果看不到應用註冊,請確保在[Azure AD 中為應用服務應用](#register)添加了**user_impersonation**範圍。
1. 選擇**user_impersonation,** 然後選擇 **「添加權限**」 。

您現在配置了一個本機用戶端應用程式,該應用程式可以代表使用者訪問應用服務應用。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
