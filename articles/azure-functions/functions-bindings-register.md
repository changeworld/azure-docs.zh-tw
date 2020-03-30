---
title: 註冊 Azure 函數綁定擴展
description: 瞭解如何基於環境註冊 Azure 函數綁定擴展。
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277514"
---
# <a name="register-azure-functions-binding-extensions"></a>註冊 Azure 函數綁定擴展

在 Azure 函數版本 2.x 中，[綁定](./functions-triggers-bindings.md)作為函數運行時的單獨包提供。 雖然 .NET 函數通過 NuGet 包訪問綁定，但擴展包允許其他函數通過配置設置訪問所有綁定。

請考慮與綁定擴展相關的以下項：

- 綁定擴展未在函數 1.x 中顯式註冊，除非[使用 Visual Studio 創建 C# 類庫](#local-csharp)。

- 預設情況下支援 HTTP 和計時器觸發器，不需要擴展。

下表指示註冊綁定的時以及如何註冊綁定。

| 開發環境 |註冊<br/> 在 Functions 1.x 中  |註冊<br/> 在 Functions 2.x 中  |
|-------------------------|------------------------------------|------------------------------------|
|Azure 入口網站|自動|自動|
|Non-.NET語言或本地 Azure 核心工具開發|自動|[使用 Azure 函數核心工具和擴展包](#extension-bundles)|
|使用視覺化工作室的 C# 類庫|[使用 NuGet 工具](#vs)|[使用 NuGet 工具](#vs)|
|使用 Visual Studio Code 的 C# 類別庫|N/A|[使用 .NET Core CLI](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>用於本地發展的擴展捆綁包

擴展包是一種部署技術，允許您向函數應用添加一組相容的函數綁定擴展。 生成應用時，將添加一組預定義的擴展。 捆綁包中定義的擴展包彼此相容，這有助於避免包之間的衝突。 您可以在應用的主機.json 檔中啟用擴展包。  

您可以將擴展包與版本 2.x 和函數運行時的更高版本一起使用。 在本地開發時，請確保使用的是最新版本的[Azure 函數核心工具](functions-run-local.md#v2)。

使用 Azure 函數核心工具、視覺化工作室代碼以及遠端生成時，將擴展包用於本地開發。

如果不使用擴展包，則必須在本地電腦上安裝 .NET Core 2.x SDK，然後才能安裝任何綁定擴展。 擴展捆綁包消除了本地開發的此要求。 

要使用擴展包，請更新*host.json*檔以包括 以下條目`extensionBundle`：
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>帶\#視覺工作室的 C 類庫

在**Visual Studio 中**，可以使用[安裝包](https://docs.microsoft.com/nuget/tools/ps-ref-install-package)命令從包管理器主控台安裝包，如以下示例所示：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

用於給定綁定的包的名稱在該綁定的參考文章中提供。 如需範例，請參閱[服務匯流排繫結參考文章的套件一節](functions-bindings-service-bus.md#functions-1x)。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效版本以[NuGet.org](https://nuget.org)在各個包頁上列出。對應于函數運行時 1.x 或 2.x 的主要版本在綁定的參考文章中指定。

如果使用`Install-Package`引用綁定，則不需要使用[擴展包](#extension-bundles)。 此方法特定于在 Visual Studio 中構建的類庫。

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>C# 類庫，帶視覺工作室代碼

在**Visual Studio 代碼**中，使用 .NET Core CLI 中的[dotnet 添加包](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package)命令從命令提示符安裝 C# 類庫專案的包。 以下示例演示如何添加綁定：

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI 只能用於 Azure Functions 2.x 開發。

替換為`<BINDING_TYPE_NAME>`包含所需綁定的包的名稱。 您可以在[受支援的繫結欄位表中](./functions-triggers-bindings.md#supported-bindings)找到所需的綁定引用文章。

請以特定版本的套件 (例如 `3.0.0-beta5`) 取代範例中的 `<TARGET_VERSION>`。 有效版本以[NuGet.org](https://nuget.org)在各個包頁上列出。對應于函數運行時 1.x 或 2.x 的主要版本在綁定的參考文章中指定。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [Azure 函數觸發器和綁定示例](./functions-bindings-example.md)
