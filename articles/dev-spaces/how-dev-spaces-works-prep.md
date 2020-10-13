---
title: 準備 Azure Dev Spaces 專案的方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 說明如何使用 Azure Dev Spaces 工作來準備您的專案
keywords: azds. yaml、Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: 61351072494b51d02a1d6c31399208b9e9b54fce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213424"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>準備 Azure Dev Spaces 專案的方式

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 開發人員空間可以為您的專案產生 Dockerfile 和 Helm 圖表。 開發人員空間也會建立並使用設定檔，以在 AKS 中部署、執行及 Kubernetes 應用程式的偵錯工具。 這些檔案都包含在您的應用程式程式碼中，並且可以新增至您的版本控制系統。

本文說明您在使用 Dev Spaces 準備專案以在 AKS 中執行時，會發生什麼事。

## <a name="prepare-your-code"></a>準備您的程式碼

若要在開發人員空間中執行您的應用程式，您必須將其容器化，而且您必須定義應如何將它部署至 Kubernetes。 若要將您的應用程式，您需要 Dockerfile。 若要定義應用程式部署至 Kubernetes 的方式，您需要 [Helm 圖](https://docs.helm.sh/)。 為了協助您為應用程式建立 Dockerfile 和 Helm 圖表，用戶端工具提供 `prep` 下列命令：

```cmd
azds prep --enable-ingress
```

此 `prep` 命令會查看專案中的檔案，並嘗試建立 Dockerfile 和 Helm 圖表，以便在 Kubernetes 中執行您的應用程式。 目前，此 `prep` 命令會產生具有下列語言的 Dockerfile 和 Helm 圖表：

* Java
* Node.js
* .NET Core

您 *必須* `prep` 從包含原始程式碼的目錄執行命令。 `prep`從正確的目錄執行命令可讓用戶端工具識別語言，並建立適當的 Dockerfile 來將您的應用程式。 您也可以 `prep` 從包含 JAVA 專案 *pom.xml* 檔案的目錄中執行命令。

如果您從不 `prep` 包含原始程式碼的目錄執行命令，用戶端工具將不會產生 Dockerfile。 它也會顯示錯誤，指出： *由於不支援的語言而無法產生 Dockerfile*。 如果用戶端工具無法辨識專案類型，也會發生此錯誤。

當您執行 `prep` 命令時，您可以選擇指定 `--enable-ingress` 旗標。 此旗標會告知控制器建立此服務的網際網路可存取端點。 如果您未指定此旗標，則只能從叢集內或使用用戶端工具所建立的 localhost 通道來存取此服務。 您可以藉 `prep` 由更新產生的 Helm 圖表，在執行命令之後啟用或停用此行為。

此 `prep` 命令不會取代您的專案中任何現有的 dockerfile 或 Helm 圖表。 如果現有的 Dockerfile 或 Helm 圖使用與命令所產生之檔案相同的命名慣例 `prep` ，此 `prep` 命令將會略過產生這些檔案。 否則，此 `prep` 命令將會產生自己的 Dockerfile 或 Helm 圖表，以及現有檔案的側邊。

> [!IMPORTANT]
> Azure Dev Spaces 針對您的專案使用 Dockerfile 和 Helm 圖表來建立和執行您的程式碼，但如果您想要變更專案的建立和執行方式，可以修改這些檔案。

此 `prep` 命令也會 `azds.yaml` 在專案的根目錄中產生檔案。 Azure Dev Spaces 會使用此檔案來建立、安裝、設定及執行您的應用程式。 此設定檔會列出 Dockerfile 和 Helm 圖表的位置，也會在這些構件之上提供其他設定。

以下是使用 [.Net Core 範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)所建立的 azds yaml 檔案範例：

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

`azds.yaml`命令所產生的檔案 `prep` 可用於簡單的單一專案開發案例。 如果您的特定專案有更高的複雜度，您可能需要在執行命令之後更新這個檔案 `prep` 。 例如，您的專案可能需要根據您的開發或偵錯工具需求來變更組建或啟動流程。 您的專案中也可能會有多個應用程式，而這需要多個組建程式或不同的組建內容。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何在開發人員空間中執行您的程式碼，請參閱 [使用 Azure Dev Spaces 執行您的程式碼的運作方式][how-it-works-up]。

[how-it-works-up]: how-dev-spaces-works-up.md