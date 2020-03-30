---
title: 設定 Azure AD 驗證
description: 瞭解如何將 Azure 活動目錄身份驗證配置為應用服務應用的標識提供程式。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: fdad1f820d006c39fa135a29a5ec7377c47591f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80046452"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>將應用服務應用配置為使用 Azure AD 登錄名

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介紹如何將 Azure 應用服務配置為使用 Azure 活動目錄 （Azure AD） 作為身份檢查器提供者。

設置應用和身份驗證時遵循以下最佳實踐：

- 為每個應用服務應用授予其自己的許可權和同意。
- 配置每個應用服務應用，並進行自己的註冊。
- 通過使用單獨的應用註冊來單獨部署槽，避免環境之間的許可權共用。 測試新代碼時，這種做法有助於防止問題影響生產應用。

## <a name="configure-with-express-settings"></a><a name="express"> </a>使用快速設定來設定

> [!NOTE]
> **"快速"** 選項不適用於政府雲。 

1. 在[Azure 門戶]中，搜索並選擇**應用服務**，然後選擇應用。
2. 從左側導航中，選擇 **"身份驗證/授權** > **"。**
3. 選擇**Azure 活動目錄** > **快速 。**

   如果要選擇現有應用註冊：：

   1. 選擇 **"選擇現有廣告應用**"，然後按一下**Azure AD 應用**。
   2. 選擇現有應用程式註冊，然後按一下 **"確定**"。

3. 選取 [確定]**** 以在 Azure Active Directory 中註冊 App Service 應用程式。 將創建新的應用註冊。
   
    ![Azure 活動目錄中的快速設置](./media/configure-authentication-provider-aad/express-settings.png)
   
4. （可選）預設情況下，應用服務提供身份驗證，但不限制對網站內容和 API 的授權訪問。 您必須在應用程式程式碼中授權使用者。 要僅將應用存取權限限制為 Azure 活動目錄進行身份驗證的使用者，請設置**操作，在未通過 Azure** **活動目錄**進行登錄時執行操作。 設置此功能時，應用需要對所有請求進行身份驗證。 它還將所有未經身份驗證的目錄重定向到 Azure 活動目錄進行身份驗證。

    > [!CAUTION]
    > 以這種方式限制訪問適用于對應用的所有調用，對於具有公開可用主頁的應用（如許多單頁應用程式中）來說，這可能不可取。 對於此類應用程式，可能首選**允許匿名請求（不執行任何操作），** 應用可手動啟動登錄本身。 有關詳細資訊，請參閱[身份驗證流](overview-authentication-authorization.md#authentication-flow)。
5. 選取 [儲存]****。

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>使用進階設定來設定

如果要使用來自其他 Azure AD 租戶的應用註冊，可以手動設定應用設置。 要完成此自訂配置，本文：

1. 在 Azure AD 中創建註冊。
2. 向應用服務提供一些註冊詳細資訊。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>在 Azure AD 中為應用服務應用創建應用註冊

配置應用服務應用時，您需要以下資訊：

- 用戶端識別碼
- 租用戶識別碼
- 用戶端金鑰（可選）
- 應用程式 ID URI

執行下列步驟：

1. 登錄到 Azure[門戶]，搜索並選擇**應用服務**，然後選擇應用。 請注意應用的**URL。** 您將使用它來配置 Azure 活動目錄應用註冊。
1. 選擇**Azure 活動目錄** > **應用註冊** > **新註冊**。
1. 在 **"註冊應用程式**"頁中，輸入應用註冊**的名稱**。
1. 在**重定向 URI**中，選擇`<app-url>/.auth/login/aad/callback`**Web**並鍵入 。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 
1. 選取 [建立]****。
1. 創建應用註冊後，請複製**應用程式（用戶端）ID**和**目錄（租戶）ID，** 以便以後使用。
1. 選取 [建立品牌]****。 在**主頁 URL**中，輸入應用服務應用的 URL 並選擇"**保存**"。
1. 選擇 **"公開 API** > **集**"。 粘貼應用服務應用的 URL 並選擇 **"保存**"。

   > [!NOTE]
   > 此值是應用註冊**的應用程式 ID URI。** 如果 Web 應用需要訪問雲中的 API，則在配置雲應用服務資源時需要 Web 應用**的應用程式 ID URI。** 例如，如果您希望雲服務顯式授予對 Web 應用的存取權限，則可以使用此方法。

1. 選擇 **"添加範圍**"。
   1. 在 **"範圍"名稱**中，輸入*user_impersonation*。
   1. 在文字方塊中，輸入您希望使用者在同意頁上查看的同意範圍名稱和說明。 例如，輸入 *"訪問我的應用*"。 
   1. 選擇 **"添加範圍**"。
1. （可選）要創建用戶端機密，請選擇**證書&機密** > **新用戶端機密** > **添加**。 複製頁面中顯示的用戶端機密值。 它將不會再次顯示。
1. （可選）要添加多個**回復 URL，** 請選擇 **"身份驗證**"。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>在應用服務應用中啟用 Azure 活動目錄

1. 在[Azure 門戶]中，搜索並選擇**應用服務**，然後選擇應用。 
1. 在左側窗格中，在 **"設置"** 下，選擇 **"身份驗證/授權** > **"。**
1. （可選）預設情況下，應用服務身份驗證允許對應用進行未經身份驗證的訪問。 要強制實施使用者身份驗證，請設置在未通過 Azure**活動目錄****登錄的請求時執行的操作**。
1. 在 [驗證提供者]**** 底下，選取 [Azure Active Directory]****。
1. 在**管理模式中**，根據下表選擇 **"高級**"和"應用服務身份驗證"：

    |欄位|描述|
    |-|-|
    |用戶端識別碼| 使用應用註冊**的應用程式（用戶端）ID。** |
    |頒發者 ID| 使用`https://login.microsoftonline.com/<tenant-id>`，並將*\<租戶 id>* 替換為應用註冊的**目錄（租戶）ID。** |
    |用戶端金鑰（可選）| 使用在應用註冊中生成的用戶端金鑰。|
    |允許的權杖訪問群體| 如果這是雲或伺服器應用，並且希望允許來自 Web 應用的身份驗證權杖，請在此處添加 Web 應用**的應用程式 ID URI。** 配置的**用戶端 ID** *始終*隱式被視為允許的訪問群體。 |

2. 選取 [確定]****，然後選取 [儲存]****。

現在，您已準備好在應用服務應用中使用 Azure 活動目錄進行身份驗證。

## <a name="configure-a-native-client-application"></a>設定原生用戶端應用程式

您可以註冊本機用戶端以允許使用用戶端庫（如**活動目錄身份驗證庫**）進行身份驗證。

1. 在[Azure 門戶]中，選擇**活動目錄** > **應用註冊** > **"新註冊**"。
1. 在 **"註冊應用程式**"頁中，輸入應用註冊**的名稱**。
1. 在**重定向 URI**中，選擇**公共用戶端（移動&桌面）** 並鍵入`<app-url>/.auth/login/aad/callback`URL 。 例如： `https://contoso.azurewebsites.net/.auth/login/aad/callback` 。

    > [!NOTE]
    > 對於 Windows 應用程式，請使用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作為 URI。
1. 選取 [建立]****。
1. 創建應用註冊後，複製**應用程式（用戶端）ID**的值。
1. 選擇**API 許可權** > **添加** > **許可權我的 API**。
1. 選擇之前為應用服務應用創建的應用註冊。 如果看不到應用註冊，請確保在[Azure AD 中為應用服務應用](#register)添加了**user_impersonation**範圍。
1. 選擇**user_impersonation，** 然後選擇 **"添加許可權**"。

您現在已設定了可以存取您 App Service 應用程式的原生用戶端應用程式。

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure 門戶]: https://portal.azure.com/
