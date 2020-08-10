---
title: 註冊 Azure Functions 系結延伸模組
description: 瞭解如何根據您的環境註冊 Azure Functions 系結延伸模組。
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: a045ef0fea70347f168e8ae0cc93e0c359f31dfa
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031112"
---
# <a name="register-azure-functions-binding-extensions"></a>註冊 Azure Functions 系結延伸模組

從 Azure Functions 2.x 版開始，系結[會當做](./functions-triggers-bindings.md)函式執行時間中的個別套件來提供。 雖然 .NET 函式會透過 NuGet 封裝來存取系結，但擴充功能組合可讓其他函式透過設定設定來存取所有系結。

請考慮下列與系結延伸模組相關的專案：

- 除了[使用 Visual Studio 建立 c # 類別庫](#local-csharp)以外，系結延伸模組不會在函數1.x 中明確註冊。

- 預設支援 HTTP 和計時器觸發程式，而且不需要擴充功能。

下表指出註冊系結的時機和方式。

| 開發環境 |註冊<br/> 在 Functions 1.x 中  |註冊<br/> 在函數 3.x/2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|自動<sup>*</sup>|
|Non-.NET 語言或本機 Azure Core 工具開發|自動|[使用 Azure Functions Core Tools 和延伸模組配套](#extension-bundles)|
|使用 Visual Studio 的 c # 類別庫|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#vs-code)|

<sup>*</sup>入口網站使用延伸模組配套。

## <a name="extension-bundles"></a><a name="extension-bundles"></a>延伸模組配套

延伸模組組合是將一組相容的函式系結延伸模組新增至函式應用程式的方式。 使用配套時，會在您建立應用程式時新增一組預先定義的延伸模組。 配套中定義的延伸模組套件會經過驗證，以協助您避免封裝之間的衝突。 延伸模組配套可讓您避免必須以 non-.NET 函式專案發行 .NET 專案程式碼。 您會在應用程式的 host.json file 中啟用擴充功能配套。  

您可以使用版本2.x 和更新版本的函式執行時間的延伸模組組合。 

在遠端建立時，使用 Azure Functions Core Tools、Visual Studio Code 和的本機開發延伸模組組合。 在本機開發時，請確定您使用的是最新版本的[Azure Functions Core Tools](functions-run-local.md#v2)。 在 Azure 入口網站中開發函式時，也會使用延伸模組配套。 

如果您未使用延伸模組配套，則必須先在本機電腦上安裝 .NET Core 2.x SDK，才能[明確安裝任何](#explicitly-install-extensions)系結延伸模組。 擴充 .csproj 檔案（明確定義必要的延伸模組）會新增至您的專案。 延伸模組配套會移除這些需求以進行本機開發。 

若要使用延伸模組配套，請更新檔案*上的host.js* ，以包含下列專案 `extensionBundle` ：
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>明確安裝延伸模組

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>NuGet 套件

針對 c # 類別庫型函式專案，您應該安裝延伸模組組合是特別針對非類別的專案所設計。 

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#具有 Visual Studio 的 C 類別庫

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
