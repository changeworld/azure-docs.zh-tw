---
title: 快速入門：從 Python 精靈呼叫 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會了解 Python 程序如何使用應用程式本身的身分識別來取得存取權杖，並呼叫受 Microsoft 身分識別平台端點保護的 API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: fb26acedc4a043ab84d02c142ecaa1dcfa94fd07
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011742"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>快速入門：使用應用程式的身分識別取得權杖，並從 Python 主控台應用程式呼叫 Microsoft Graph API

在本快速入門中，您會下載並執行程式碼範例，該範例會示範 Python 應用程式如何使用應用程式的身分識別來取得存取權杖，以呼叫 Microsoft Graph API 及顯示目錄中的[使用者清單](/graph/api/user-list)。 此程式碼範例會示範自動作業或 Windows 服務如何使用應用程式識別來執行，而不是以使用者的身分識別執行。 

> [!div renderon="docs"]
> ![說明本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>必要條件

若要執行此範例，您需要：

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) 或 [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式

> [!div renderon="docs" class="sxs-lookup"]
>
> 有兩個選項可用來啟動快速入門應用程式：快速 (下面的選項 1) 和手動 (選項 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至新的 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs)窗格。
> 1. 輸入應用程式的名稱，並選取 [註冊]。
> 1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 搜尋並選取 [Azure Active Directory]  。
> 1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
> 1. 輸入應用程式的 [名稱]，例如 `Daemon-console`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
> 1. 選取 [註冊]。
> 1. 在 [管理]  下，選取 [憑證和密碼]  。
> 1. 在 [用戶端密碼] 底下，選取 [新增用戶端密碼] 並輸入名稱，然後選取 [新增]。 將祕密值記錄在安全的位置，以便在稍後的步驟中使用。
> 1. 在 [管理] 底下，選取 [API 權限] > [新增權限]。 選取 [Microsoft Graph]。
> 1. 選取 [應用程式權限]。
> 1. 在 [使用者] 節點底下，選取 [User.Read.All]，然後選取 [新增權限]。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>下載並設定您的快速入門應用程式
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
> 若要讓本快速入門中的程式碼範例能夠運作，您需要建立用戶端密碼，並新增 Graph API 的 **User.Read.All** 應用程式權限。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-netcore-daemon/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-your-python-project"></a>步驟 2:下載您的 Python 專案

> [!div renderon="docs"]
> [下載 Python 精靈專案](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>步驟 3：設定您的 Python 專案
>
> 1. 將 Zip 檔案解壓縮至磁碟根目錄附近的本機資料夾，例如 **C:\Azure-Samples**。
> 1. 瀏覽至子資料夾 **1-Call-MsGraph-WithSecret"** 。
> 1. 編輯 **parameters.json**，並將欄位 `authority`、`client_id` 和 `secret` 的值取代為下列程式碼片段：
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    其中：
>    - `Enter_the_Application_Id_Here` - 是您註冊的應用程式所具備的 **應用程式 (用戶端) 識別碼**。
>    - `Enter_the_Tenant_Id_Here` - 請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] (例如 contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here` - 請將此值取代為步驟 1 所建立的用戶端密碼。
>
> > [!TIP]
> > 若要尋找 [應用程式 (用戶端) 識別碼]、[目錄 (租用戶) 識別碼] 的值，請在 Azure 入口網站中移至應用程式的 [概觀] 頁面。 若要產生新的金鑰，請移至 [憑證和祕密] 頁面。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>步驟 3：系統管理員同意

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>步驟 4：系統管理員同意

如果您嘗試在此時執行應用程式，您將會收到「HTTP 403 - 禁止」錯誤：`Insufficient privileges to complete the operation`。 之所以發生此錯誤，是因為任何「僅限應用程式權限」都需要管理員同意：目錄的全域管理員必須對應用程式表示同意。 請根據您的角色選取下列其中一個選項：

##### <a name="global-tenant-administrator"></a>全域租用戶管理員

> [!div renderon="docs"]
> 如果您是全域租用戶管理員，請移至 Azure 入口網站「應用程式註冊 (預覽)」中的 [API 權限] 頁面，然後選取 [代表 {租用戶名稱} 授與管理員同意] (其中，{租用戶名稱} 是您的目錄名稱)。

> [!div renderon="portal" class="sxs-lookup"]
> 如果您是全域管理員，請移至 [API 權限] 頁面，選取 [代表 Enter_the_Tenant_Name_Here 授與管理員同意]
> > [!div id="apipermissionspage"]
> > [移至 [API 權限] 頁面]()

##### <a name="standard-user"></a>標準使用者

如果您是租用戶的標準使用者，則需要請全域管理員針對應用程式授與管理員同意。 若要這樣做，請提供下列 URL 給管理員：

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> 其中：
>> * `Enter_the_Tenant_Id_Here` - 請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] (例如 contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - 是您註冊的應用程式所具備的 **應用程式 (用戶端) 識別碼**。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>步驟 4：執行應用程式

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>步驟 5：執行應用程式

您必須安裝此範例的相依性一次

```console
pip install -r requirements.txt
```

然後，透過命令提示字元或主控台執行應用程式：

```console
python confidential_client_secret_sample.py parameters.json
```

您應該會在主控台輸出中看到一些 JSON 片段，代表您 Azure AD 目錄中的使用者清單。

> [!IMPORTANT]
> 此快速入門應用程式會使用用戶端密碼，將自己識別為機密用戶端。 由於用戶端密碼會以純文字形式新增至您的專案檔，因此，基於安全考量，在考慮將應用程式當作生產應用程式之前，建議您使用憑證，而非用戶端密碼。 如需關於如何使用憑證的詳細資訊，請在相同的 GitHub 存放庫 (但位於第二個資料夾 **2-Call-MsGraph-WithCertificate**) 中參閱此範例的 [這些指示](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md)

## <a name="more-information"></a>詳細資訊

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 是一個程式庫，用來登入使用者，以及要求所需權杖用以存取受 Microsoft 身分識別平台保護的 API。 如前所述，本快速入門會使用應用程式本身的身分識別 (而非委派的權限) 來要求權杖。 此案例所使用的驗證流程稱為[用戶端認證 OAuth 流程](v2-oauth2-client-creds-grant-flow.md)。 如需如何搭配使用 MSAL Python 與精靈應用程式的詳細資訊，請參閱[這篇文章](scenario-daemon-overview.md)。

 您可以藉由執行下列 pip 命令來安裝 MSAL Python。

```powershell
pip install msal
```

### <a name="msal-initialization"></a>MSAL 初始化

您可以透過加入下列程式碼來新增 MSAL 的參考：

```Python
import msal
```

接著，使用下列程式碼將 MSAL 初始化：

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | 其中： |描述 |
> |---------|---------|
> | `config["secret"]` | 在 Azure 入口網站中為應用程式建立的用戶端密碼。 |
> | `config["client_id"]` | 是註冊於 Azure 入口網站中的應用程式所具備的 **應用程式 (用戶端) 識別碼**。 您可以在 Azure 入口網站的應用程式 [概觀] 頁面中找到此值。 |
> | `config["authority"]`    | 供使用者用於驗證的 STS 端點。 若為公用雲端，通常是 `https://login.microsoftonline.com/{tenant}`，其中 {tenant} 是租用戶的名稱或租用戶識別碼。|

如需詳細資訊，請參閱[下列項目的參考文件：`ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>要求權杖

若要使用應用程式的身分識別來要求權杖，請使用 `AcquireTokenForClient` 方法：

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |其中：| 描述 |
> |---------|---------|
> | `config["scope"]` | 包含所要求的範圍。 針對機密用戶端，這應該使用類似 `{Application ID URI}/.default` 的格式，以指出所要求的範圍是 Azure 入口網站中所設定應用程式物件中以靜態方式定義的範圍 (若為 Microsoft Graph，`{Application ID URI}` 會指向 `https://graph.microsoft.com`)。 若為自訂 Web API，在 Azure 入口網站「應用程式註冊 (預覽)」中的 [公開 API] 區段底下會定義 `{Application ID URI}`。 |

如需詳細資訊，請參閱[下列項目的參考文件：`AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解精靈應用程式，請參閱案例登陸頁面

> [!div class="nextstepaction"]
> [呼叫 Web API 的精靈應用程式](scenario-daemon-overview.md)
