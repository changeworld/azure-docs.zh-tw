---
title: 教學課程：在 Python Web 應用程式中啟用驗證
titleSuffix: Azure AD B2C
description: 在本教學課程中，了解如何使用 Azure Active Directory B2C 為 Python Flask Web 應用程式提供使用者登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 6a113b3a3df475853f3690a0e932378bc0e2ab02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844621"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>教學課程：在 Python Web 應用程式中透過 Azure AD B2C 啟用驗證

本教學課程將說明如何使用 AAzure Active Directory B2C (Azure AD B2C) 在 Python Flask Web 應用程式中註冊和登入使用者。

本教學課程內容：

> [!div class="checklist"]
> * 將回覆 URL 新增至在您 Azure AD B2C 租用戶中註冊的應用程式
> * 從 GitHub 下載範例程式碼
> * 修改範例應用程式的程式碼以使用您的租用戶
> * 使用您的註冊/登入使用者流程註冊

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

您需要先備妥下列 Azure AD B2C 資源，才能繼續本教學課程中的步驟：

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 已在您租用戶中[註冊的應用程式](tutorial-register-applications.md)，以及其*應用程式 (用戶端) 識別碼*和*用戶端密碼*
* 您租用戶中[建立的使用者流程](tutorial-create-user-flows.md)

此外，您的開發環境中需要下列項目：

* [Visual Studio Code](https://code.visualstudio.com/) 或其他程式碼編輯器
* [Python](https://nodejs.org/en/download/) 2.7+ 或 3+

## <a name="add-a-redirect-uri"></a>新增重新導向 URI

在已完成的第二個必要教學課程中，您在 Azure AD B2C 中註冊了 Web 應用程式。 若要啟用本教學課程中範例程式碼的通訊，請在應用程式註冊中新增回覆 URL (也稱為重新導向 URI)。

若要更新 Azure AD B2C 租用戶中的應用程式，您可以使用我們全新整合的**應用程式註冊**體驗，或使用舊版**應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]、選取 [擁有的應用程式] 索引標籤，然後選取 [webapp1] 應用程式。
1. 在 [管理] 底下，選取 [驗證]。
1. 在 [Web] 底下，選取 [新增 URI] 連結，然後在文字方塊中輸入 `http://localhost:5000/getAToken`。
1. 選取 [儲存]。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選取 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式 (舊版)]，然後選取 [webapp1] 應用程式。
1. 在 [回覆 URL] 下方，新增 `http://localhost:5000/getAToken`。
1. 選取 [儲存]。
* * *

## <a name="get-the-sample-code"></a>取得範例程式碼

在本教學課程中，您會設定從 GitHub 下載以與 B2C 租用戶搭配使用的程式碼範例。 此範例會示範 Python Flask Web 應用程式如何使用 Azure AD B2C 進行使用者註冊和登入。

[下載 .ZIP 封存檔](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)，或從 GitHub 複製[程式碼範例存放庫](https://github.com/Azure-Samples/ms-identity-python-webapp)。

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>更新範例

一旦取得了範例，就請將應用程式設定為使用您的 Azure AD B2C 租用戶、應用程式註冊和使用者流程。

在專案的根目錄中：

1. 將 *app_config.py* 檔案重新命名為 *app_config.py.OLD*
1. 將 *app_config_b2c.py* 檔案重新命名為 *app_config.py*

將剛重新命名的 *app_config.py* 更新為 Azure AD B2C 租用戶和應用程式註冊 (這兩者已被您建立作為必要條件) 的值。

1. 在編輯器中開啟 *app_config.py* 檔案。
1. 將 `b2c_tenant` 值更新為 Azure AD B2C 租用戶的名稱，例如 *contosob2c*。
1. 更新每個 `*_user_flow` 值，以符合您建立作為必要條件的使用者流程名稱。
1. 將 `CLIENT_ID` 值更新為 Web 應用程式的**應用程式 (用戶端) 識別碼**，而此 Web 應用程式已被您註冊為必要條件。
1. 將 `CLIENT_SECRET` 值更新為您已在必要條件中建立的**用戶端密碼**值。 為了提高安全性，考慮將其改存在**環境變數**中，依註解中的建議進行。

*app_config.py* 的頂端區段現在看起來應該類似下列程式碼片段：

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> 如程式碼片段註解中所述，我們建議您**不要以純文字將秘密儲存**在應用程式碼中。 程式碼範例中使用硬式編碼變數，*只是為了方便*而已。 請考慮使用環境變數或秘密存放區，例如 Azure Key Vault。

## <a name="run-the-sample"></a>執行範例

1. 在您的主控台或終端機中，切換至包含範例的目錄。 例如：

    ```console
    cd ms-identity-python-webapp
    ```
1. 執行下列命令，從 PyPi 安裝所需的套件，並在您的本機電腦上執行 Web 應用程式：

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    主控台視窗會顯示本機執行應用程式的連接埠號碼：

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. 瀏覽至 `http://localhost:5000`，以檢視在本機電腦上執行的 Web 應用程式。

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="網頁瀏覽器顯示在本機執行的 Python Flask Web 應用程式":::

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

這個範例應用程式支援註冊、登入和密碼重設。 在本教學課程中，您會使用電子郵件地址來註冊。

1. 選取 [登入]，以起始您在先前的步驟中指定的 *B2C_1_signupsignin1* 使用者流程。
1. Azure AD B2C 會顯示包含註冊連結的登入頁面。 由於您還沒有帳戶，因此請選取 [立即註冊] 連結。
1. 註冊工作流程會顯示一個使用電子郵件地址來收集並驗證使用者身分識別的頁面。 註冊工作流程也會收集使用者的密碼，以及在使用者流程中定義的要求屬性。

    請使用有效的電子郵件地址，並使用驗證碼進行驗證。 設定密碼。 輸入要求的屬性值。

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="網頁瀏覽器顯示在本機執行的 Python Flask Web 應用程式":::

1. 選取 [建立]，在 Azure AD B2C 目錄中建立本機帳戶。

當您選取 [建立] 時，應用程式會顯示已登入之使用者的名稱。

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="網頁瀏覽器顯示在本機執行的 Python Flask Web 應用程式":::

如果想要測試登入，請選取 [登出] 連結，然後選取 [登入]，以您註冊時所輸入的電子郵件地址和密碼登入。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定 Python Flask Web 應用程式，以在 Azure AD B2C 租用戶中使用使用者流程，以提供註冊和登入功能。 您已完成下列步驟：

> [!div class="checklist"]
> * 已將回覆 URL 新增到在您 Azure AD B2C 租用戶中註冊的應用程式
> * 已從 GitHub 下載範例程式碼
> * 已修改範例應用程式的程式碼以使用您的租用戶
> * 已使用您的註冊/登入使用者流程註冊

接下來，了解如何自訂使用者流程頁面的 UI，這些頁面是由 Azure AD B2C 呈現給您的使用者：

> [!div class="nextstepaction"]
> [教學課程：在 Azure AD B2C 中自訂使用者介面體驗 >](tutorial-customize-ui.md)
