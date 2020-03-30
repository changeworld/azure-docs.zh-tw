---
title: 為 Azure 開發空間準備專案的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 開發人員空間準備專案的工作原理
keywords: azds.yaml， Azure 開發空間， 開發空間， Docker， 庫伯奈斯， Azure， AKS， Azure 庫伯奈斯服務， 容器
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241630"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>為 Azure 開發空間準備專案的工作原理

Azure 開發人員空間為您提供了多種方法來快速反覆運算和調試 Kubernetes 應用程式，並與您的團隊協作處理 Azure Kubernetes 服務 （AKS） 群集。 開發空間可以為專案生成 Dockerfile 和 Helm 圖表。 開發人員空間還創建和使用設定檔，用於在 AKS 中部署、運行和調試 Kubernetes 應用程式。 所有這些檔都駐留在應用程式的代碼中，可以添加到版本控制系統中。

本文介紹了為在具有開發空間的 AKS 中運行的專案準備發生的情況。

## <a name="prepare-your-code"></a>準備代碼

為了在開發空間中運行應用程式，它需要將其容器化，並且您需要定義應該如何將其部署到 Kubernetes。 要對應用程式進行容器化，您需要 Dockerfile。 要定義應用程式如何部署到庫伯內斯，您需要一個[Helm 圖表](https://docs.helm.sh/)。 為了協助為應用程式創建 Dockerfile 和 Helm 圖表，用戶端工具提供以下`prep`命令：

```cmd
azds prep --enable-ingress
```

該`prep`命令將查看專案中的檔，並嘗試創建 Dockerfile 和 Helm 圖表，用於在 Kubernetes 中運行應用程式。 目前，該`prep`命令將生成具有以下語言的 Dockerfile 和 Helm 圖表：

* Java
* Node.js
* .NET Core

*您必須*從包含原始程式碼`prep`的目錄中運行該命令。 從正確的`prep`目錄運行命令允許用戶端工具標識語言並創建適當的 Dockerfile 來容器化應用程式。 還可以從包含 JAVA`prep`專案的*pom.xml*檔的目錄運行該命令。

如果從不包含原始程式碼`prep`的目錄運行命令，則用戶端工具將不會生成 Dockerfile。 它還將顯示一個錯誤，指出：*由於不支援的語言，無法生成 Dockerfile。* 如果用戶端工具無法識別專案類型，也會發生此錯誤。

運行該`prep`命令時，可以選擇指定標誌`--enable-ingress`。 此標誌告訴控制器為此服務創建可 Internet 訪問的終結點。 如果不指定此標誌，則服務只能從群集內或使用用戶端工具創建的本地主機隧道進行訪問。 您可以通過更新生成的 Helm 圖表來`prep`啟用或禁用此行為。

該`prep`命令不會替換專案中現有的 Dockerfile 或 Helm 圖表。 如果現有的 Dockerfile 或 Helm 圖表使用與`prep`命令生成的檔相同的命名約定，則`prep`該命令將跳過生成這些檔。 否則，該`prep`命令將生成其自己的 Dockerfile 或 Helm 圖表，沿現有檔的一側。

> [!IMPORTANT]
> Azure 開發人員空間使用 Dockerfile 和 Helm 圖表來生成和運行代碼，但如果您想要更改專案的生成和運行方式，則可以修改這些檔。

該`prep`命令還將在專案的根`azds.yaml`目錄生成一個檔。 Azure 開發空間使用此檔來生成、安裝、配置和運行應用程式。 此設定檔列出了 Dockerfile 和 Helm 圖表的位置，並在這些專案的基礎上提供了其他配置。

下面是使用[.NET Core 應用程式範例](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)創建的 azds.yaml 檔示例：

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

該`azds.yaml``prep`命令生成的檔適用于簡單的單個專案開發方案。 如果特定專案的複雜性增加，則可能需要在運行`prep`該命令後更新此檔。 例如，您的專案可能需要根據您的開發或調試需求對生成或啟動過程進行一些更改。 專案中可能還有多個應用程式，這些應用程式需要多個生成過程或不同的生成內容。

## <a name="next-steps"></a>後續步驟

要瞭解有關在開發空間中運行代碼的更多詳細資訊，請參閱[使用 Azure 開發人員空間運行代碼的工作原理][how-it-works-up]。

要開始使用 Azure 開發空間為 Azure 開發空間準備專案，請參閱以下快速入門：

* [使用視覺化工作室代碼和 JAVA 快速反覆運算和調試][quickstart-java]
* [使用視覺化工作室代碼和 .NET 快速反覆運算和調試][quickstart-netcore]
* [使用視覺化工作室代碼和 Node.js 快速反覆運算和調試][quickstart-node]
* [使用 Visual Studio 和 .NET 核心快速反覆運算和調試][quickstart-vs]
* [使用 CLI 在庫伯奈斯開發應用程式][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md