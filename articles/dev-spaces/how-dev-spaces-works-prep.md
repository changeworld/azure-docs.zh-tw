---
title: 準備專案以 Azure Dev Spaces 運作的方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述如何使用 Azure Dev Spaces 來準備您的專案
keywords: azds. yaml，Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241630"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>準備專案以 Azure Dev Spaces 運作的方式

Azure Dev Spaces 提供多種方式來快速反復查看和 Kubernetes 應用程式，並在 Azure Kubernetes Service （AKS）叢集上與您的小組共同作業。 開發人員空間可以為您的專案產生 Dockerfile 和 Helm 圖表。 開發人員空間也會建立並使用設定檔，在 AKS 中部署、執行和 Kubernetes 應用程式。 這些檔案都是與您應用程式的程式碼有關，而且可以加入至您的版本控制系統。

本文說明當您準備好要在 AKS 中使用 Dev Spaces 執行專案時，會發生什麼事。

## <a name="prepare-your-code"></a>準備您的程式碼

若要在開發人員空間中執行您的應用程式，它必須是容器化的，而且您必須定義應該如何將它部署至 Kubernetes。 若要容器化您的應用程式，您需要 Dockerfile。 若要定義如何將應用程式部署至 Kubernetes，您需要[Helm 圖表](https://docs.helm.sh/)。 為了協助同時為您的應用程式建立 Dockerfile 和 Helm 圖表，用戶端工具提供下列`prep`命令：

```cmd
azds prep --enable-ingress
```

此`prep`命令會查看專案中的檔案，並嘗試建立 Dockerfile 和 Helm 圖表，以在 Kubernetes 中執行您的應用程式。 目前，此`prep`命令會產生具有下列語言的 Dockerfile 和 Helm 圖表：

* Java
* Node.js
* .NET Core

您*必須*從包含`prep`原始程式碼的目錄執行命令。 從正確`prep`的目錄執行命令，可讓用戶端工具識別語言，並建立適當的 Dockerfile 來容器化您的應用程式。 您也可以從包含`prep` JAVA 專案的*pom*檔案的目錄執行命令。

如果您從不`prep`包含原始程式碼的目錄執行命令，用戶端工具將不會產生 Dockerfile。 它也會顯示錯誤，指出：*由於不支援的語言而無法產生 Dockerfile*。 如果用戶端工具無法辨識專案類型，也會發生這個錯誤。

當您執行`prep`命令時，可以選擇指定`--enable-ingress`旗標。 此旗標會告知控制器建立此服務的網際網路可存取端點。 如果您未指定此旗標，則只能從叢集內或使用用戶端工具所建立的 localhost 通道來存取服務。 您可以藉由更新所產生的 Helm 圖表`prep` ，在執行命令之後啟用或停用此行為。

此`prep`命令將不會取代您在專案中擁有的任何現有 Dockerfile 或 Helm 圖表。 如果現有的 Dockerfile 或 Helm 圖表使用與`prep`命令所產生之檔案相同的命名慣例，此`prep`命令將會略過產生這些檔案。 否則，此`prep`命令會產生自己的 Dockerfile 或 Helm 圖表，以及現有檔案的側邊。

> [!IMPORTANT]
> Azure Dev Spaces 會使用專案的 Dockerfile 和 Helm 圖表來建立和執行您的程式碼，但如果您想要變更專案的建立和執行方式，可以修改這些檔案。

此`prep`命令也會在專案`azds.yaml`的根目錄產生檔案。 Azure Dev Spaces 使用此檔案來建立、安裝、設定及執行您的應用程式。 此設定檔會列出 Dockerfile 和 Helm 圖表的位置，也會在這些成品上提供額外的設定。

以下是使用[.Net Core 範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)建立的 azds yaml 檔案範例：

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

此`azds.yaml` `prep`命令所產生的檔案適用于簡單的單一專案開發案例。 如果您的特定專案已增加複雜度，您可能需要在執行`prep`命令之後更新此檔案。 例如，您的專案可能需要根據您的開發或偵錯工具需求，對組建或啟動程式進行一些變更。 您的專案中可能也會有多個應用程式，這需要多個組建進程或不同的組建內容。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何在開發人員空間中執行您的程式碼，請參閱[使用 Azure Dev Spaces 執行您的程式碼如何運作][how-it-works-up]。

若要開始使用 Azure Dev Spaces 來準備您的 Azure 開發人員空間的專案，請參閱下列快速入門：

* [使用 Visual Studio Code 和 JAVA 快速反復查看和調試][quickstart-java]
* [使用 Visual Studio Code 和 .NET 快速反復查看和調試][quickstart-netcore]
* [使用 Visual Studio Code 和 node.js 快速反復查看和調試][quickstart-node]
* [使用 Visual Studio 和 .NET Core 快速反復查看和調試][quickstart-vs]
* [使用 CLI 在 Kubernetes 上開發應用程式][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md