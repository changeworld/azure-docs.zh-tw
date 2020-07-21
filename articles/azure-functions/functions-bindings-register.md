---
title: 註冊 Azure Functions 系結延伸模組
description: 瞭解如何根據您的環境註冊 Azure Functions 系結延伸模組。
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 2dde784e2f67266b2f6c6ccd7da20f01546bbda7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506480"
---
# <a name="register-azure-functions-binding-extensions"></a>註冊 Azure Functions 系結延伸模組

在 Azure Functions 2.x 版中，系結會當做函式運行[時間中的](./functions-triggers-bindings.md)個別套件來提供。 雖然 .NET 函式會透過 NuGet 封裝來存取系結，但擴充功能組合可讓其他函式透過設定設定來存取所有系結。

請考慮下列與系結延伸模組相關的專案：

- 除了[使用 Visual Studio 建立 c # 類別庫](#local-csharp)以外，系結延伸模組不會在函數1.x 中明確註冊。

- 預設支援 HTTP 和計時器觸發程式，而且不需要擴充功能。

下表指出註冊系結的時機和方式。

| 開發環境 |註冊<br/> 在 Functions 1.x 中  |註冊<br/> 在 Functions 2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|自動|
|Non-.NET 語言或本機 Azure Core 工具開發|自動|[使用 Azure Functions Core Tools 和延伸模組配套](#extension-bundles)|
|使用 Visual Studio 的 c # 類別庫|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>本機開發的延伸模組配套

延伸模組配套是一種部署技術，可讓您將一組相容的函式系結擴充功能新增至函式應用程式。 當您建立應用程式時，會新增一組預先定義的延伸模組。 組合中定義的延伸模組套件彼此相容，這可協助您避免封裝之間的衝突。 您會在應用程式的 host.json file 中啟用擴充功能配套。  

您可以使用版本2.x 和更新版本的函式執行時間的延伸模組組合。 在本機開發時，請確定您使用的是最新版本的[Azure Functions Core Tools](functions-run-local.md#v2)。

在遠端建立時，使用 Azure Functions Core Tools、Visual Studio Code 和的本機開發延伸模組組合。

如果您未使用延伸模組配套，則必須先在本機電腦上安裝 .NET Core 2.x SDK，再安裝任何系結延伸模組。 延伸模組配套會移除本機開發的這項需求。 

若要使用延伸模組配套，請更新檔案*上的host.js* ，以包含下列專案 `extensionBundle` ：
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#具有 Visual Studio 的 C 類別庫

在**Visual Studio**中，您可以使用 [[安裝套件](/nuget/tools/ps-ref-install-package)] 命令從套件管理員主控台安裝套件，如下列範例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用於指定系結的封裝名稱會在該系結的參考文章中提供。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效版本會列在[NuGet.org](https://nuget.org)的個別套件頁面上。對應至函式執行時間1.x 或2.x 的主要版本，會在系結的參考文章中指定。

如果您使用 `Install-Package` 來參考系結，則不需要使用[延伸](#extension-bundles)模組配套。 這個方法是 Visual Studio 內建的類別庫所特有。

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>具有 Visual Studio Code 的 c # 類別庫

在**Visual Studio Code**中，使用 .NET Core CLI 中的[dotnet add package](/dotnet/core/tools/dotnet-add-package)命令，從命令提示字元安裝 c # 類別庫專案的套件。 下列範例示範如何新增系結：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用於 Azure Functions 2.x 開發。

將取代 `<BINDING_TYPE_NAME>` 為包含您所需系結的套件名稱。 您可以在支援的系結[清單](./functions-triggers-bindings.md#supported-bindings)中找到所需的系結參考文章。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效版本會列在[NuGet.org](https://nuget.org)的個別套件頁面上。對應至函式執行時間1.x 或2.x 的主要版本，會在系結的參考文章中指定。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure 函數觸發程式和系結範例](./functions-bindings-example.md)
