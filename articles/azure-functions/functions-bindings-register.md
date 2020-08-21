---
title: 註冊 Azure Functions 系結延伸模組
description: 瞭解如何根據您的環境註冊 Azure Functions 系結延伸模組。
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689542"
---
# <a name="register-azure-functions-binding-extensions"></a>註冊 Azure Functions 系結延伸模組

從 Azure Functions 2.x 版開始，函數執行時間預設只會包含 HTTP 和計時器觸發程式。 其他 [觸發程式和](./functions-triggers-bindings.md) 系結可作為個別的套件。

.NET 類別庫函式應用程式會使用在專案中安裝為 NuGet 套件的系結。 擴充功能組合可讓 non-.NET 函式應用程式使用相同的系結，而不需要處理 .NET 基礎結構。

下表指出註冊系結的時機和方式。

| 開發環境 |註冊<br/> 在 Functions 1.x 中  |註冊<br/> 在函數 3.x/2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|自動<sup>*</sup>|
|Non-.NET 語言|自動|使用[延伸](#extension-bundles)模組套件組合 (建議) 或[明確安裝擴充](#explicitly-install-extensions)功能|
|使用 Visual Studio 的 c # 類別庫|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#vs-code)|

<sup>*</sup> 入口網站使用延伸模組套件組合。

## <a name="access-extensions-in-non-net-languages"></a>存取 non-.NET 語言的延伸模組

針對 JAVA、JavaScript、PowerShell、Python 和自訂處理函式應用程式，我們建議使用延伸模組組合來存取系結。 如果無法使用延伸模組組合，您可以明確地安裝系結延伸模組。

### <a name="extension-bundles"></a><a name="extension-bundles"></a>延伸模組套件組合

擴充功能組合是將一組相容的系結延伸模組新增至函式應用程式的方式。 您可以在應用程式的 *host.js* 檔案中啟用延伸模組套件組合。

您可以使用擴充功能組合搭配2.x 版和更新版本的函式執行時間。

擴充功能套組已建立版本。 每個版本都包含一組特定的系結延伸模組，可進行驗證以一起運作。 根據您在應用程式中所需的擴充功能，選取套件組合版本。

若要將擴充功能套件組合新增至函式應用程式，請將 `extensionBundle` 區段新增至 *host.js*。 在許多情況下，Visual Studio Code 和 Azure Functions Core Tools 會自動為您新增。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

下表列出目前可用的 *ExtensionBundle* 組合，以及其所包含之延伸模組的連結。

| 套件組合版本 | host.js的版本 | 包含的延伸模組 |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | 查看用來產生套件組合的[extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |
| 2.x | `[2.*, 3.0.0)` | 查看用來產生套件組合的[extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |

> [!NOTE]
> 雖然您可以在 host.js的上指定自訂版本範圍，但我們建議您使用此資料表中的版本值。

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>明確安裝擴充功能

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>以 .NET 語言安裝來自 NuGet 的延伸模組

針對 c # 類別庫型函式專案，您應該直接安裝擴充功能。 擴充套件組合是特別針對非 c # 類別庫型的專案所設計。

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#具有 Visual Studio 的 C 類別庫

在 **Visual Studio**中，您可以使用 [安裝套件](/nuget/tools/ps-ref-install-package) 命令從封裝管理員主控台安裝套件，如下列範例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

針對指定系結所使用的封裝名稱，會在該系結的參考文章中提供。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org)的個別封裝頁面上。對應至函式執行時間1.x 或2.x 的主要版本是在系結的參考文章中指定的。

如果您使用 `Install-Package` 參考系結，就不需要使用 [延伸](#extension-bundles)模組套件組合。 這種方法是 Visual Studio 內建的類別庫所特有。

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> 具有 Visual Studio Code 的 c # 類別庫

在 **Visual Studio Code**中，使用 .NET Core CLI 中的 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令，從命令提示字元安裝 c # 類別庫專案的封裝。 下列範例示範如何新增系結：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用於 Azure Functions 2.x 開發。

取代 `<BINDING_TYPE_NAME>` 為包含所需系結的封裝名稱。 您可以在支援的系結 [清單](./functions-triggers-bindings.md#supported-bindings)中找到所需的系結參考文章。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效的版本會列在 [NuGet.org](https://nuget.org)的個別封裝頁面上。對應至函式執行時間1.x 或2.x 的主要版本是在系結的參考文章中指定的。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure 函數觸發程式和系結範例](./functions-bindings-example.md)
