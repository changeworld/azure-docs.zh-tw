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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74325733"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>搭配 Azure Dev Spaces 使用自訂 NuGet 摘要

NuGet 摘要會提供在專案中包含套件來源的便利方式。 Azure Dev Spaces 需要存取此摘要，才能正確地將相依性安裝在 Docker 容器中。

## <a name="set-up-a-nuget-feed"></a>設定 NuGet 摘要

在節點下的檔案中，為您的相依性新增[套件參考](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) `*.csproj` `PackageReference` 。 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在專案資料夾中建立[NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file)檔案，並 `packageSources` 為 NuGet 摘要設定和 `packageSourceCredentials` 區段。 `packageSources`區段包含您的摘要 url，必須可從您的 AKS 叢集存取。 `packageSourceCredentials`是用來存取摘要的認證。 例如：

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

更新您的 Dockerfile，將檔案複製 `NuGet.Config` 到映射。 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows 上， `NuGet.Config` 、 `Nuget.Config` 和 `nuget.config` 都是以有效的檔案名的形式運作。 在 Linux 上，只有 `NuGet.Config` 這個檔案有效的檔案名。 因為 Azure Dev Spaces 使用 Docker 和 Linux，所以這個檔案必須命名為 `NuGet.Config` 。 您可以手動或藉由執行來修正此命名 `dotnet restore --configfile nuget.config` 。


如果您使用 Git，則不應該在版本控制中擁有 NuGet 摘要的認證。 將新增 `NuGet.Config` 至 `.gitignore` 專案的，讓檔案 `NuGet.Config` 不會加入至版本控制。 Azure Dev Spaces 在容器映射建立程式期間需要此檔案，但根據預設，它會遵守在同步處理期間和中定義的規則 `.gitignore` `.dockerignore` 。 若要變更預設值，並允許 Azure Dev Spaces 同步處理檔案 `NuGet.Config` ，請更新檔案 `azds.yaml` ：

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

如果您不是使用 Git，可以略過此步驟。

下次您執行 `azds up` 或點擊 `F5` Visual Studio Code 或 Visual Studio 時，Azure Dev Spaces 會同步處理檔案， `NuGet.Config` 以用來安裝封裝相依性。

## <a name="next-steps"></a>後續步驟

深入瞭解[NuGet 及其運作方式](https://docs.microsoft.com/nuget/what-is-nuget)。