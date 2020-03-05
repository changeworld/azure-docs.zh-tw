---
title: 教學課程：在原生用戶端應用程式中驗證使用者
titleSuffix: Azure AD B2C
description: 關於如何使用 Azure Active Directory B2C 為 .NET 傳統型應用程式提供使用者登入的教學課程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186194"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 在原生桌面用戶端驗證使用者

本教學課程將說明如何使用 Azure Active Directory B2C (Azure AD B2C) 在 Windows Presentation Foundation (WPF) 傳統型應用程式中登入和註冊使用者。 Azure AD B2C 可讓您的應用程式使用開放式標準通訊協定向社交帳戶、企業帳戶和 Azure Active Directory 帳戶進行驗證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增原生用戶端應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

- [建立使用者流程](tutorial-create-user-flows.md)以在您的應用程式中達成使用者體驗。
- 安裝 [Visual Studio 2019](https://www.visualstudio.com/downloads/)，其中包含 **.NET 傳統型開發**以及 **ASP.NET 和 Web 開發**工作負載。

## <a name="add-the-native-client-application"></a>新增原生用戶端應用程式

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

記錄 [應用程式 (用戶端) 識別碼]  ，以便在稍後的步驟中使用。

## <a name="configure-the-sample"></a>設定範例

在本教學課程中，您會設定可從 GitHub 下載的範例。 範例 WPF 桌面應用程式會示範如何在 Azure AD B2C 中註冊、登入，並且可呼叫受保護的 Web API。 [下載 Zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip)、[瀏覽存放庫](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)，或從 GitHub 複製範例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

若要更新應用程式以與 Azure AD B2C 租用戶搭配使用，並叫用其使用者流程，而不是預設示範租用戶中的流程：

1. 在 Visual Studio 中開啟 **active-directory-b2c-wpf** 方案 (`active-directory-b2c-wpf.sln`)。
2. 在 **active-directory-b2c-wpf** 專案中，開啟 *App.xaml.cs* 檔案，並尋找以下變數定義。 將 `{your-tenant-name}` 取代為您的 Azure AD B2C 租用戶名稱，並將 `{application-ID}` 取代為您先前記錄的應用程式識別碼。

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. 使用建立作為必要條件的使用者流程名稱來更新原則名稱變數。 例如：

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>執行範例

按 **F5** 以建置並執行範例。

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

1. 選取 [登入]  ，以使用者身分註冊。 這會使用 **B2C_1_signupsignin1** 使用者流程。
2. Azure AD B2C 會顯示含有 [立即註冊]  連結的登入頁面。 由於您還沒有帳戶，因此請選取 [立即註冊]  連結。
3. 註冊工作流程會顯示一個使用電子郵件地址來收集並驗證使用者身分識別的頁面。 註冊工作流程也會收集使用者的密碼，以及在使用者流程中定義的要求屬性。

    請使用有效的電子郵件地址，並使用驗證碼進行驗證。 設定密碼。 輸入要求的屬性值。

    ![註冊頁面顯示為登入/註冊工作流程的一部分](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. 選取 [建立]  ，在 Azure AD B2C 租用戶中建立本機帳戶。

現在，使用者可以使用電子郵件地址登入並使用桌面應用程式。 成功註冊或登入後，權杖詳細資料顯示在 WPF 應用程式的下方窗格中。

![WPF 桌面應用程式底部窗格中顯示的權杖詳細資料](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

如果您選取 [呼叫 API]  按鈕，則會顯示**錯誤訊息**。 發生錯誤是因為在其目前狀態下，應用程式正嘗試存取示範租用戶 `fabrikamb2c.onmicrosoft.com` 保護的 API。 由於您的存取權杖只對您的 Azure AD B2C 租用戶有效，所以此 API 呼叫未經授權。

繼續進行下一個教學課程，以在您自己的租用戶中註冊受保護的 Web API，並啟用 [呼叫 API]  功能。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增原生用戶端應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

接著，若要啟用 [呼叫 API]  按鈕功能，請授與 WPF 桌面應用程式存取您專屬 Azure AD B2C 租用戶中註冊的 Web API 權限：

> [!div class="nextstepaction"]
> [教學課程：授與從桌面應用程式存取 Node.js Web API 的權限 >](tutorial-desktop-app-webapi.md)
