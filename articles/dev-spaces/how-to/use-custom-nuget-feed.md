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
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960213"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>搭配 Azure Dev Spaces 使用自訂 NuGet 摘要

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

NuGet 摘要會提供在專案中包含套件來源的便利方式。 Azure Dev Spaces 需要存取此摘要，才能將相依性正確地安裝在 Docker 容器中。

## <a name="set-up-a-nuget-feed"></a>設定 NuGet 摘要

在節點下的檔案中，為您的相依性新增 [套件參考](/nuget/consume-packages/package-references-in-project-files) `*.csproj` `PackageReference` 。 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在專案資料夾中建立 [NuGet.Config](/nuget/reference/nuget-config-file) 檔案，並 `packageSources` 為您的 NuGet 摘要設定和 `packageSourceCredentials` 區段。 `packageSources`區段包含您的摘要 url，必須可從 AKS 叢集存取。 `packageSourceCredentials`是用來存取摘要的認證。 例如：

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

更新您的 Dockerfile，以將檔案複製 `NuGet.Config` 到映射。 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows、 `NuGet.Config` 、 `Nuget.Config` 和上，全部都 `nuget.config` 是有效的檔案名。 在 Linux 上，只有這個檔案有 `NuGet.Config` 有效的檔案名。 由於 Azure Dev Spaces 使用 Docker 和 Linux，因此必須將此檔案命名為 `NuGet.Config` 。 您可以手動或藉由執行來修正命名 `dotnet restore --configfile nuget.config` 。


如果您使用 Git，則您不應該在版本控制中擁有 NuGet 摘要的認證。 將加入 `NuGet.Config` 至 `.gitignore` 專案的，使檔案 `NuGet.Config` 不會加入至版本控制。 Azure Dev Spaces 在容器映射建立程式期間需要此檔案，但根據預設，它會遵循在同步處理期間和中定義的規則 `.gitignore` `.dockerignore` 。 若要變更預設值，並允許 Azure Dev Spaces 同步處理檔案 `NuGet.Config` ，請更新檔案 `azds.yaml` ：

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

如果您不是使用 Git，則可以略過此步驟。

下次您在 `azds up` `F5` Visual Studio Code 或 Visual Studio 中執行或時，Azure Dev Spaces 將會同步處理檔案， `NuGet.Config` 以用來安裝套件相依性。

## <a name="next-steps"></a>後續步驟

深入瞭解 [NuGet 及其運作方式](/nuget/what-is-nuget)。
