---
title: 沈浸式閱讀程式 C# 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您會從頭開始建置 Web 應用程式，並且新增沈浸式閱讀程式 API 功能。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/20/2020
ms.author: nitinme
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: f3d694a1e1eb368a97d994ebe9885c279ff44463
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505371"
---
[沈浸式閱讀程式](https://www.onenote.com/learningtools)是經過全面設計的工具，可實作經實證的技術以改善閱讀理解程度。

在本快速入門中，您會從頭開始建置 Web 應用程式，並使用沈浸式閱讀程式用戶端程式庫來整合沈浸式閱讀程式。 本快速入門的完整工作範例可以在[這裡](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)取得。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* 為 Azure Active Directory 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](../../how-to-create-immersive-reader.md)來設定。 設定範例專案屬性時，您需要這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。

## <a name="create-a-web-app-project"></a>建立 Web 應用程式專案

在 Visual Studio 中，使用內建 Model-View-Controller 的 ASP.NET Core Web 應用程式範本和 ASP.NET Core 2.1 來建立新的專案。 將專案命名為 "QuickstartSampleWebApp"。

![新增專案](../../media/quickstart-csharp/1-createproject.png)

![設定新的專案](../../media/quickstart-csharp/2-configureproject.png)

![新的 ASP.NET Core Web 應用程式](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>設定驗證

### <a name="configure-authentication-values"></a>設定驗證值

以滑鼠右鍵按一下_方案總管_中的專案，然後選擇 [管理使用者密碼]。 這樣會開啟名稱為 _secrets.json_ 的檔案。 此檔案不會簽入原始檔控制。 [在此](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows)深入了解。 以下列內容取代 secrets.json 的內容，並使用您建立沈浸式閱讀程式資源時提供的值。

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Add the Microsoft.IdentityModel.Clients.ActiveDirectory NuGet 套件

下列程式碼使用 **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet 套件中的物件，所以您必須在專案中新增該套件的參考。

從 [工具] -> [NuGet 套件管理員] -> [套件管理員主控台] 開啟 NuGet 套件管理員主控台，然後執行下列命令：

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>更新控制器以取得權杖 

開啟 Controllers\HomeController.cs，然後在檔案頂端的 using 陳述式後面加入下列程式碼。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

現在，我們會將控制器設定為從 secrets.json 取得 Azure AD 值。 在 HomeController 類別頂端的 ```public class HomeController : Controller {``` 後面加入下列程式碼。

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>新增範例內容
首先，開啟 Views\Shared\Layout.cshtml。 在 ```</head>``` 行之前加入下列程式碼：

```html
@RenderSection("Styles", required: false)
```

現在，我們會將範例內容新增至此 Web 應用程式。 開啟 Views\Home\Index.cshtml，並且以下列範例取代所有自動產生的程式碼：

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

請注意，所有文字都會有一個 **lang** 屬性，用於描述文字的語言。 此屬性可協助沈浸式閱讀程式提供相關的語言和文法功能。

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>新增 JavaScript 以處理沈浸式閱讀程式的啟動

「沈浸式閱讀程式」程式庫提供的功能包括啟動沈浸式閱讀程式，以及呈現沈浸式閱讀程式按鈕。 [在此](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference)深入了解。

在 Views\Home\Index.cshtml 的底部新增下列程式碼：

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>建置並執行應用程式

從功能表列中選取 [偵錯] > [開始偵錯]，或按 **F5** 以啟動應用程式。

在瀏覽器中，您應該會看到：

![範例應用程式](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>啟動沈浸式閱讀程式

當您按一下 [沈浸式閱讀程式] 按鈕時，您會看到沈浸式閱讀程式啟動且其內容顯示在頁面上。

![沈浸式閱讀程式](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>後續步驟

* 檢視 [Node.js 快速入門](../../tutorial-nodejs.md)，以查看您還可以使用 Node.js 透過沈浸式閱讀程式執行哪些作業
* 檢視 [Android 教學課程](../../tutorial-android.md)，以查看您還可以使用適用於 Android 的 Java 或 Kotlin 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [iOS 教學課程](../../tutorial-ios.md)，以查看您還可以使用適用於 iOS 的 Swift 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [Python 教學課程](../../tutorial-python.md)，以查看您還可以使用 Python 透過沈浸式閱讀程式用戶端程式庫執行哪些作業
* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](../../reference.md)