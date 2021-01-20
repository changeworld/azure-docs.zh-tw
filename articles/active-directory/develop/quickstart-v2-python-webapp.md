---
title: 快速入門：將「使用 Microsoft 登入」新增至 Python Web 應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，了解 Python 應用程式如何登入使用者、從 Microsoft 身分識別平台取得存取權杖，以及呼叫 Microsoft Graph API。
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 3c3eaddf1767a3fa4a2ba73ae7a27f1f7df13990
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178192"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>快速入門：將「使用 Microsoft 登入」新增至 Python Web 應用程式

在本快速入門中，您會下載並執行程式碼範例，該範例會示範 Python Web 應用程式如何登入使用者，並取得存取權杖來呼叫 Microsoft Graph API。 具有個人 Microsoft 帳戶或任何 Azure Active Directory (Azure AD) 組織中帳戶的使用者都可以登入應用程式。

如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) 或 [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/)、[Flask-Session](https://pypi.org/project/Flask-Session/)、[requests](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
>
> 有兩個選項可用來啟動快速入門應用程式：快速 (選項 1) 和手動 (選項 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs" target="_blank">Azure 入口網站 - 應用程式註冊<span class="docon docon-navigate-external x-hidden-focus"></span></a>快速入門體驗。
> 1. 輸入應用程式的名稱，並選取 [註冊]  。
> 1. 依照指示來下載並自動設定新應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
>
> 若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
> 1. 輸入應用程式的 [名稱]，例如 `python-webapp`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
> 1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
> 1. 選取 [註冊]。
> 1. 在應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值以供稍後使用。
> 1. 在 [管理] 底下，選取 [驗證]。
> 1. 選取 [新增平台] > [Web]。
> 1. 將 `http://localhost:5000/getAToken` 新增為 **重新導向 URI**。
> 1. 選取 [設定] 。
> 1. 在 [管理] 底下選取 [憑證和祕密]，然後從 [用戶端密碼] 區段中，選取 [新增用戶端密碼]。
> 1. 輸入金鑰描述 (用於執行個體應用程式祕密)、保留預設到期日，然後選取 [新增]。
> 1. 請注意 **用戶端密碼** 的 **值**，以供稍後使用。
> 1. 在 [管理] 底下，選取 [API 權限] > [新增權限]。
>1.  確定已選取 [Microsoft API] 索引標籤。
> 1. 在 [常用的 Microsoft API] 區段中，選取 [Microsoft Graph]。
> 1. 在 [委派的權限] 區段中，請確定已選取正確的權限：**User.ReadBasic.All**。 如有需要請使用搜尋方塊。
> 1. 選取 [新增權限] 按鈕。
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
>
> 若要讓本快速入門中的程式碼範例能正常運作，您需要：
>
> 1. 新增回覆 URL 作為 `http://localhost:5000/getAToken`。
> 1. 建立用戶端密碼。
> 1. 新增 Microsoft Graph API 的 User.ReadBasic.All 委派權限。
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-aspnet-webapp/green-check.png) 您的應用程式已設定了這個屬性

#### <a name="step-2-download-your-project"></a>步驟 2:下載您的專案
> [!div renderon="docs"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 下載專案，並將 zip 檔案解壓縮至根資料夾附近的本機資料夾 - 例如 **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>步驟 3：設定應用程式
>
> 1. 將 ZIP 檔案解壓縮至根資料夾附近的本機資料夾 - 例如 **C:\Azure-Samples**
> 1. 如果您使用整合式開發環境，請在您慣用的 IDE 中開啟範例 (選用)。
> 1. 開啟 **app_config.py** 檔案，其位於根資料夾並取代為下列程式碼片段：
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> 其中：
>
> - `Enter_the_Application_Id_here` - 是您註冊的應用程式所具備的應用程式識別碼。
> - `Enter_the_Client_Secret_Here` - 您在 [憑證與祕密]  中為您所註冊的應用程式建立的 [用戶端密碼]  。
> - `Enter_the_Tenant_Name_Here` - 是您所註冊之應用程式的 [目錄 (租用戶) 識別碼]  值。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>步驟 3：執行程式碼範例

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>步驟 4：執行程式碼範例

1. 您需要使用 pip 安裝 MSAL Python 程式庫、Flask 架構、Flask-Session，以進行伺服器端工作階段管理和要求，如下所示：

    ```Shell
    pip install -r requirements.txt
    ```

2. 從殼層或命令列執行 app.py：

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > 此快速入門應用程式會使用用戶端密碼，將自己識別為機密用戶端。 由於用戶端密碼會以純文字形式新增至您的專案檔，因此，基於安全考量，在考慮將應用程式當作生產應用程式之前，建議您使用憑證，而非用戶端密碼。 如需如何使用憑證的詳細資訊，請參閱[這些指示](./active-directory-certificate-credentials.md) \(部分機器翻譯\)。

## <a name="more-information"></a>詳細資訊

### <a name="how-the-sample-works"></a>此範例的運作方式
![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>取得 MSAL
MSAL 是用來登入使用者並要求權杖的程式庫，該權杖可用來存取受 Microsoft 身分識別平台保護的 API。
您可以使用 PIP 來將 MSAL Python 新增至應用程式。

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL 初始化
您可以藉由將下列程式碼新增至您將使用 MSAL 的檔案頂端，以將參考新增至 MSAL Python：

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

深入了解在多部分案例系列中登入使用者的 Web 應用程式。

> [!div class="nextstepaction"]
> [案例：登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)
