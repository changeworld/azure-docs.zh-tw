---
title: 使用自訂 NuGet 摘要
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: 在 Azure Dev Spaces 中使用自訂 NuGet 摘要以存取及使用 NuGet 套件。
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325733"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>將自訂 NuGet 源與 Azure 開發空間一起使用

NuGet 摘要會提供在專案中包含套件來源的便利方式。 Azure 開發人員空間需要訪問此源，以便在 Docker 容器中正確安裝依賴項。

## <a name="set-up-a-nuget-feed"></a>設定 NuGet 摘要

`*.csproj`在`PackageReference`節點下的檔中為依賴項添加[包引用](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)。 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在專案資料夾中創建[NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file)檔，並為`packageSources`NuGet 源設置 和`packageSourceCredentials`節。 該`packageSources`部分包含源 URL，必須從 AKS 群集訪問該 URL。 是`packageSourceCredentials`訪問源的憑據。 例如：

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

更新 Docker 檔以將`NuGet.Config`檔案複製到映射。 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows `NuGet.Config` `Nuget.Config`上，`nuget.config`和 和所有都用作有效的檔案名。 在 Linux`NuGet.Config`上，僅此檔的有效檔案名。 由於 Azure 開發人員空間使用 Docker 和 Linux，`NuGet.Config`因此必須命名此檔。 您可以手動修復命名，也可以通過運行`dotnet restore --configfile nuget.config`來修復命名。


如果使用 Git，則不應在版本控制項中具有 NuGet 源的憑據。 添加到`NuGet.Config`專案的`.gitignore`， 以便`NuGet.Config`檔不會添加到版本控制項。 Azure 開發人員空間在容器映射生成過程中需要此檔，但預設情況下，它尊重同步期間`.gitignore`和`.dockerignore`同步期間定義的規則。 要更改預設值並允許 Azure 開發空間同步檔，`NuGet.Config`請`azds.yaml`更新該檔：

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

如果您不使用 Git，則可以跳過此步驟。

下次在 Visual `azds up` Studio`F5`代碼或視覺化工作室中運行或命中時，Azure 開發人員空間`NuGet.Config`將同步檔使用它安裝包依賴項。

## <a name="next-steps"></a>後續步驟

瞭解更多關於[NuGet及其工作原理](https://docs.microsoft.com/nuget/what-is-nuget)。