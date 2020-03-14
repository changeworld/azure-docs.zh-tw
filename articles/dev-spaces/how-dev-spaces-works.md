---
title: Azure Dev Spaces 的運作方式和設定
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: 描述 power Azure Dev Spaces 的處理常式，以及如何在 azds yaml 設定檔中設定它們。
keywords: azds. yaml，Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259912"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Azure Dev Spaces 的運作方式和設定

開發 Kubernetes 應用程式可能是一項挑戰。 您需要 Docker 和 Kubernetes 設定檔。 您必須瞭解如何在本機測試您的應用程式，並與其他相依服務互動。 您可能需要同時處理多項服務的開發和測試，以及一小組開發人員。

Azure Dev Spaces 可協助您直接在 Azure Kubernetes Service （AKS）中開發、部署及調試 Kubernetes 應用程式。 Azure Dev Spaces 也可讓小組共用開發人員空間。 跨小組共用開發人員空間可讓個別小組成員以隔離方式進行開發，而不需要複寫或模擬叢集中的相依性或其他應用程式。

Azure Dev Spaces 會建立和使用設定檔，以在 AKS 中部署、執行和 Kubernetes 應用程式。 此設定檔會與您的應用程式程式碼一起存在，並可新增至您的版本控制系統。

本文說明 power Azure Dev Spaces 的程式，以及這些進程在 Azure Dev Spaces 設定檔中的設定方式。 若要快速執行 Azure Dev Spaces 並在實務上查看，請完成其中一個快速入門：

* [使用 CLI 和 Visual Studio Code 的 JAVA](quickstart-java.md)
* [使用 CLI 和 Visual Studio Code 的 .NET Core](quickstart-netcore.md)
* [具有 Visual Studio 的 .NET Core](quickstart-netcore-visualstudio.md)
* [使用 CLI 和 Visual Studio Code 的 node.js](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces 如何運作

Azure Dev Spaces 有兩個與您互動的不同元件：控制器和用戶端工具。

![Azure Dev Spaces 元件](media/how-dev-spaces-works/components.svg)

控制器會執行下列動作：

* 管理開發人員空間的建立和選取。
* 安裝您應用程式的 Helm 圖，並建立 Kubernetes 物件。
* 建立應用程式的容器映射。
* 將您的應用程式部署至 AKS。
* 當您的原始程式碼變更時，會執行累加式組建並重新啟動。
* 記錄管理檔和 HTTP 追蹤。
* 將 stdout 和 stderr 轉送至用戶端工具。
* 可讓小組成員建立衍生自父開發人員空間的子 dev spaces。
* 設定應用程式在空間內的路由，以及跨父系和子空間。

控制器位於 AKS 外部。 它會驅動用戶端工具與 AKS 叢集之間的行為和通訊。 當您準備讓叢集使用 Azure Dev Spaces 時，會使用 Azure CLI 來啟用此控制器。 啟用之後，您就可以使用用戶端工具與它互動。

用戶端工具可讓使用者：
* 產生應用程式的 Dockerfile、Helm 圖表和 Azure Dev Spaces 設定檔。
* 建立父系和子開發人員空間。
* 告知控制器建立並啟動您的應用程式。

當您的應用程式正在執行時，用戶端工具也會：
* 從您在 AKS 中執行的應用程式接收並顯示 stdout 和 stderr。
* 使用[埠向前轉送](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)，以允許使用 HTTP：\//localhost. 對應用程式進行 web 存取
* 在 AKS 中，將偵錯工具附加至您正在執行的應用程式。
* 針對累加組建偵測到變更時，將原始程式碼同步處理至您的開發人員空間，以加速反復專案。

您可以使用命令列中的用戶端工具做為 `azds` 命令的一部分。 您也可以搭配使用用戶端工具與：

* 使用[Azure Dev Spaces 延伸](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)模組 Visual Studio Code。
* [適用於 Kubernetes 的 Visual Studio Tools](https://aka.ms/get-vsk8stools)的 Visual Studio。

以下是設定和使用 Azure Dev Spaces 的基本流程：
1. 準備 Azure Dev Spaces 的 AKS 叢集
1. 準備要在 Azure Dev Spaces 上執行的程式碼
1. 在開發人員空間上執行程式碼
1. 在開發人員空間上偵錯工具代碼
1. 共用開發人員空間

我們將在下列各節中詳細說明 Azure Dev Spaces 的運作方式。

## <a name="prepare-your-aks-cluster"></a>準備您的 AKS 叢集

準備您的 AKS 叢集包括：
* 確認您的 AKS 叢集位於[Azure Dev Spaces 支援][supported-regions]的區域中。
* 正在驗證您是否正在執行 Kubernetes 1.10.3 或更新版本。
* 使用 `az aks use-dev-spaces` 在叢集上啟用 Azure Dev Spaces

如需有關如何建立和設定 AKS 叢集以進行 Azure Dev Spaces 的詳細資訊，請參閱其中一個快速入門手冊：
* [開始使用 JAVA Azure Dev Spaces](get-started-java.md)
* [開始使用 .NET Core 和 Visual Studio 的 Azure Dev Spaces](get-started-netcore-visualstudio.md)
* [開始使用 .NET Core Azure Dev Spaces](get-started-netcore.md)
* [開始使用 node.js Azure Dev Spaces](get-started-nodejs.md)

當您的 AKS 叢集上啟用了 Azure Dev Spaces 時，它會為您的叢集安裝控制器。 控制器是叢集外的個別 Azure 資源，並會對叢集中的資源執行下列動作：

* 建立或指定要做為開發人員空間使用的 Kubernetes 命名空間。
* 移除名為*azds*的任何 Kubernetes 命名空間（如果有的話），並建立一個新的。
* 部署 Kubernetes webhook 設定。
* 部署 webhook 許可伺服器。
    

它也會使用您的 AKS 叢集用來對其他 Azure Dev Spaces 元件進行服務呼叫的相同服務主體。

![Azure Dev Spaces 準備叢集](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure Dev Spaces，必須至少有一個開發人員空間。 Azure Dev Spaces 在您的 AKS 叢集中使用 Dev Spaces 的 Kubernetes 命名空間。 安裝控制器時，它會提示您建立新的 Kubernetes 命名空間，或選擇現有的命名空間做為您的第一個開發人員空間。 當命名空間指定為開發人員空間時，控制器會將*azds.io/space=true*標籤新增至該命名空間，以將其識別為開發人員空間。 在準備好叢集之後，預設會選取您建立或指定的初始開發人員空間。 選取空間時，Azure Dev Spaces 會使用它來建立新的工作負載。

根據預設，控制器會藉由升級現有的*預設*Kubernetes 命名空間，建立名為*default*的開發人員空間。 您可以使用用戶端工具來建立新的開發人員空間，並移除現有的開發人員空間。 由於 Kubernetes 的限制，無法移除*預設*的開發人員空間。 控制器也會移除名為*azds*的任何現有 Kubernetes 命名空間，以避免與用戶端工具所使用的 `azds` 命令發生衝突。

Kubernetes webhook 許可伺服器是用來在部署檢測時，將具有三個容器的 pod 插入： devspaces proxy 容器、devspaces proxy-init 容器和 devspaces 組建容器。 **這三個容器都是在 AKS 叢集上以根存取的方式執行。** 它們也會使用您的 AKS 叢集用來對其他 Azure Dev Spaces 元件進行服務呼叫的相同服務主體。

![Azure Dev Spaces Kubernetes webhook 許可伺服器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces proxy 容器是一個側車容器，可處理進出應用程式容器的所有 TCP 流量，並協助執行路由。 如果使用了某些空格，devspaces proxy 容器會將 HTTP 訊息重排。 例如，它可以協助在父和子空間中的應用程式之間路由傳送 HTTP 訊息。 所有非 HTTP 流量都會透過未修改的 devspaces-proxy 傳遞。 Devspaces proxy 容器也會記錄所有輸入和輸出 HTTP 訊息，並將它們以追蹤的形式傳送給用戶端工具。 然後，開發人員可以看到這些追蹤來檢查應用程式的行為。

Devspaces-proxy-init 容器是一個[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它會根據空間階層，將額外的路由規則新增至應用程式的容器。 它會在應用程式容器的 */etc/resolv.conf*檔啟動之前，先更新並進行 iptables 設定，藉以新增路由規則。 */Etc/resolv.conf*的更新可讓您在父空間中解析服務的 DNS。 Iptables 設定更新可確保進出應用程式容器的所有 TCP 流量都會透過 devspaces-proxy 來路由傳送。 除了 Kubernetes 新增的規則之外，devspaces 中的所有更新-proxy-init 也會發生。

Devspaces 組建容器是 init 容器，並已裝載專案原始程式碼和 Docker 通訊端。 專案原始碼和 Docker 的存取權可讓 pod 直接建立應用程式容器。

> [!NOTE]
> Azure Dev Spaces 使用相同的節點來建立應用程式的容器，並加以執行。 因此，Azure Dev Spaces 不需要外部容器登錄來建立和執行您的應用程式。

Kubernetes webhook 許可伺服器會接聽在 AKS 叢集中建立的任何新 pod。 如果該 pod 部署到具有*azds.io/space=true*標籤的任何命名空間，它會插入該 pod 與其他容器。 只有在使用用戶端工具執行應用程式的容器時，才會插入 devspaces-組建容器。

準備好 AKS 叢集之後，您可以使用用戶端工具來準備和執行您在開發人員空間中的程式碼。

## <a name="prepare-your-code"></a>準備您的程式碼

若要在開發人員空間中執行您的應用程式，它必須是容器化的，而且您必須定義應該如何將它部署至 Kubernetes。 若要容器化您的應用程式，您需要 Dockerfile。 若要定義如何將應用程式部署至 Kubernetes，您需要[Helm 圖表](https://docs.helm.sh/)。 為了協助同時為您的應用程式建立 Dockerfile 和 Helm 圖表，用戶端工具提供 `prep` 命令：

```cmd
azds prep --public
```

`prep` 命令會查看專案中的檔案，並嘗試建立 Dockerfile 和 Helm 圖表，以在 Kubernetes 中執行您的應用程式。 目前，`prep` 命令會產生具有下列語言的 Dockerfile 和 Helm 圖表：

* Java
* Node.js
* .NET Core

您*必須*從包含原始程式碼的目錄執行 `prep` 命令。 從正確的目錄執行 `prep` 命令，可讓用戶端工具識別語言，並建立適當的 Dockerfile 來容器化您的應用程式。 您也可以從包含 JAVA 專案的*pom*檔案的目錄執行 `prep` 命令。

如果您從不包含原始程式碼的目錄執行 `prep` 命令，用戶端工具將不會產生 Dockerfile。 它也會顯示錯誤，指出：*由於不支援的語言而無法產生 Dockerfile*。 如果用戶端工具無法辨識專案類型，也會發生這個錯誤。

當您執行 `prep` 命令時，可以選擇指定 `--public` 旗標。 此旗標會告知控制器建立此服務的網際網路可存取端點。 如果您未指定此旗標，則只能從叢集內或使用用戶端工具所建立的 localhost 通道來存取服務。 您可以藉由更新所產生的 Helm 圖表，在執行 `prep` 命令之後，啟用或停用此行為。

`prep` 命令不會取代您在專案中擁有的任何現有 Dockerfile 或 Helm 圖表。 如果現有的 Dockerfile 或 Helm 圖表使用的命名慣例與 `prep` 命令所產生的檔案相同，則 `prep` 命令會略過產生這些檔案。 否則，`prep` 命令會產生自己的 Dockerfile 或 Helm 圖表，以及現有檔案的側邊。

`prep` 命令也會在專案的根目錄產生 `azds.yaml` 檔案。 Azure Dev Spaces 使用此檔案來建立、安裝、設定及執行您的應用程式。 此設定檔會列出 Dockerfile 和 Helm 圖表的位置，也會在這些成品上提供額外的設定。

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

`prep` 命令所產生的 `azds.yaml` 檔案，應該適用于簡單的單一專案開發案例。 如果您的特定專案已增加複雜度，您可能需要在執行 `prep` 命令之後更新此檔案。 例如，您的專案可能需要對組建或啟動程式進行一些調整，視您的開發或偵錯工具需求而定。 您的專案中可能也會有多個應用程式，這需要多個組建進程或不同的組建內容。

## <a name="run-your-code"></a>執行您的程式碼

若要在開發人員空間中執行您的程式碼，請在與 `azds.yaml` 檔案相同的目錄中發出 `up` 命令：

```cmd
azds up
```

`up` 命令會上傳您的應用程式來源檔案和其他必要成品，以建立並執行您的專案到開發人員空間。 在此，您的開發人員空間中的控制器：

1. 建立 Kubernetes 物件以部署您的應用程式。
1. 建立應用程式的容器。
1. 將您的應用程式部署至開發人員空間。
1. 如果已設定，則為您的應用程式端點建立可公開存取的 DNS 名稱。
1. 使用*埠向前轉送*，以使用 http://localhost來提供應用程式端點的存取權。
1. 將 stdout 和 stderr 轉送至用戶端工具。


### <a name="starting-a-service"></a>啟動服務

當您在開發人員空間中啟動服務時，用戶端工具和控制器會協調以同步處理您的來源檔案、建立容器和 Kubernetes 物件，以及執行您的應用程式。

在更細微的層級中，當您執行 `azds up`時，會發生什麼情況：

1. 檔案會從使用者的電腦同步處理到使用者的 AKS 叢集獨有的 Azure 檔案儲存體。 已上傳原始程式碼、Helm 圖和設定檔案。 下一節提供同步處理常式的更多詳細資料。
1. 控制器會建立一個要求來啟動新的會話。 此要求包含數個屬性，包括唯一的識別碼、空間名稱、原始程式碼的路徑，以及偵錯工具旗標。
1. 控制器會以唯一的會話識別碼取代 Helm 圖中的 *$ （tag）* 預留位置，並為您的服務安裝 Helm 圖表。 將唯一會話識別碼的參考新增至 Helm 圖表，可讓針對此特定會話部署至 AKS 叢集的容器，系結回到會話要求和相關聯的資訊。
1. 在安裝 Helm 圖期間，Kubernetes webhook 許可伺服器會將額外的容器新增至您應用程式的 pod，以進行檢測並存取專案的原始程式碼。 新增 devspaces-proxy 和 devspaces proxy-init 容器，以提供 HTTP 追蹤和空間路由。 已新增 devspaces-build 容器，以提供可存取 Docker 實例和專案原始程式碼的 pod，以建立應用程式的容器。
1. 啟動應用程式的 pod 時，會使用 devspaces-build 容器和 devspaces proxy-init 容器來建立應用程式容器。 接著會啟動應用程式容器和 devspaces proxy 容器。
1. 應用程式容器啟動之後，用戶端功能會使用 Kubernetes 的*埠轉送*功能，透過 http://localhost提供應用程式的 HTTP 存取。 此埠轉送會將您的開發電腦連接到您的開發人員空間中的服務。
1. 當 pod 中的所有容器都已啟動時，服務就會在執行中。 此時，用戶端功能會開始串流 HTTP 追蹤、stdout 和 stderr。 這項資訊是由開發人員的用戶端功能所顯示。

### <a name="updating-a-running-service"></a>正在更新執行中的服務

當服務正在執行時，如果有任何專案來源檔案變更，Azure Dev Spaces 就能夠更新該服務。 根據變更的檔案類型，Dev Spaces 也會以不同方式處理服務的更新。 開發人員空間有三種方式可以更新執行中的服務：

* 直接更新檔案
* 在執行中應用程式的容器內重建並重新啟動應用程式的進程
* 重建和重新部署應用程式的容器

![Azure Dev Spaces 檔案同步](media/how-dev-spaces-works/file-sync.svg)

某些屬於靜態資產的專案檔，例如 html、css 和 cshtml 檔案，可以直接在應用程式的容器中更新，而不需要重新開機任何專案。 如果靜態資產變更，新的檔案就會同步處理到開發人員空間，然後由執行中的容器使用。

您可以在執行中的容器內重新開機應用程式的進程，以套用原始程式碼或應用程式佈建檔等檔案的變更。 這些檔案一旦同步處理之後，應用程式就會在執行中的容器內使用*devhostagent*進程重新開機。 一開始建立應用程式的容器時，控制器會將應用程式的啟動命令取代為不同的進程，稱為*devhostagent*。 然後，應用程式的實際進程會在*devhostagent*下以子進程的形式執行，並使用*devhostagent*的輸出輸送輸出。 *Devhostagent*程式也是 dev spaces 的一部分，而且可以代表 dev spaces 在執行中的容器中執行命令。 執行重新開機時，請*devhostagent*：

* 停止目前的進程或與應用程式相關聯的進程
* 重建應用程式
* 重新開機與應用程式相關聯的進程

*Devhostagent*執行上述步驟的方式是在 `azds.yaml` 設定檔中設定。 這項設定會在稍後的章節中詳述。

專案檔（例如 Dockerfile、.csproj 檔案或 Helm 圖表的任何部分）的更新需要重建和重新部署應用程式的容器。 當其中一個檔案同步處理到開發人員空間時，控制器會執行[helm upgrade](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure)命令，並重建並重新部署應用程式的容器。

### <a name="file-synchronization"></a>檔案同步處理

第一次在開發人員空間中啟動應用程式時，會上傳所有應用程式的來源檔案。 當應用程式正在執行，且稍後重新開機時，只會上傳變更的檔案。 有兩個檔案用來協調這個進程：用戶端檔案和控制器端檔案。

用戶端檔案會儲存在臨時目錄中，並根據您在 Dev Spaces 中執行之專案目錄的雜湊來命名。 例如，在 Windows 上，您的專案會有類似*Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog*的檔案。 在 Linux 上，用戶端檔案會儲存在 */tmp*目錄中。 您可以藉由執行 `echo $TMPDIR` 命令，在 macOS 上尋找目錄。

這個檔案是 JSON 格式，而且包含：

* 與開發人員空間同步處理之每個專案檔的專案
* 同步處理識別碼
* 上次同步作業的時間戳記

每個專案檔案專案都包含檔案的路徑和其時間戳記。

控制器端檔案會儲存在 AKS 叢集上。 其中包含上一次同步處理的同步處理識別碼和時間戳記。

同步處理時間戳記在用戶端與控制器端檔案之間不相符時，就會進行同步處理。 在同步處理期間，用戶端工具會逐一查看用戶端檔案中的檔案專案。 如果檔案的時間戳記在同步時間戳記之後，該檔案就會同步處理到開發人員空間。 同步處理完成後，就會在用戶端和控制器端檔案上更新同步時間戳記。

如果用戶端檔案不存在，則會同步處理所有專案檔案。 此行為可讓您藉由刪除用戶端檔案來強制執行完整同步處理。

### <a name="how-routing-works"></a>路由的運作方式

開發人員空間是以 AKS 為基礎，並使用相同的[網路概念](../aks/concepts-network.md)。 Azure Dev Spaces 也有一個集中式*ingressmanager*服務，並將它自己的輸入控制器部署到 AKS 叢集。 *Ingressmanager*服務會監視具有 dev SPACES 的 AKS 叢集，並使用用於路由傳送至應用程式 pod 的輸入物件，來擴大叢集中的 Azure Dev Spaces 輸入控制器。 每個 pod 中的 devspaces proxy 容器都會根據 URL，將 HTTP 流量的 `azds-route-as` HTTP 標頭新增至開發人員空間。 例如， *http://azureuser.s.default.serviceA.fedcba09...azds.io* URL 的要求會取得具有 `azds-route-as: azureuser`的 HTTP 標頭。 Devspaces proxy 容器若已存在，則不會新增 `azds-route-as` 標頭。

從叢集外部對服務發出 HTTP 要求時，要求會移至輸入控制器。 輸入控制器會根據輸入物件和規則，將要求直接路由傳送至適當的 pod。 Pod 中的 devspaces proxy 容器會接收要求、根據 URL 新增 `azds-route-as` 標頭，然後將要求路由傳送至應用程式容器。

當從叢集內的另一個服務對服務發出 HTTP 要求時，要求會先經過呼叫服務的 devspaces proxy 容器。 Devspaces proxy 容器會查看 HTTP 要求，並檢查 `azds-route-as` 標頭。 根據標頭，devspaces proxy 容器會查閱與標頭值相關聯之服務的 IP 位址。 如果找到 IP 位址，devspaces proxy 容器會將要求重設為該 IP 位址。 如果找不到 IP 位址，devspaces proxy 容器會將要求路由傳送至父應用程式容器。

例如，應用程式*serviceA*和*serviceB*會部署到名為*default*的父開發人員空間。 *serviceA*依賴*serviceB*並對其進行 HTTP 呼叫。 Azure 使用者會根據名為*azureuser*的*預設*空間來建立子開發人員空間。 Azure 使用者也會將自己的*serviceA*版本部署到其子空間。 提出要求以 *http://azureuser.s.default.serviceA.fedcba09...azds.io* ：

![Azure Dev Spaces 路由](media/how-dev-spaces-works/routing.svg)

1. 輸入控制器會查閱與 URL 相關聯之 pod 的 IP，也就是*serviceA. azureuser*。
1. 輸入控制器會在 Azure 使用者的開發人員空間中尋找 pod 的 IP，並將要求路由傳送至*serviceA. azureuser* pod。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收要求，並將 `azds-route-as: azureuser` 新增為 HTTP 標頭。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會將要求路由至*serviceA* pod 中的*serviceA*應用程式容器。
1. *ServiceA. azureuser* pod 中的*serviceA*應用程式會呼叫*serviceB*。 *ServiceA*應用程式也會包含程式碼，以保留現有的 `azds-route-as` 標頭，在此案例中為 `azds-route-as: azureuser`。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收要求，並根據 `azds-route-as` 標頭的值查閱*serviceB*的 IP。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器找不到*serviceB. azureuser*的 IP。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會在父空間中查詢*serviceB*的 IP，這是*serviceB。預設值*。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會尋找*serviceB*的 IP，並將要求路由傳送至*serviceB. default* pod。
1. *ServiceB*中的 devspaces proxy 容器會接收要求，並將要求路由傳送至*ServiceB*中的*serviceB*應用程式容器。
1. *ServiceB*中的*serviceB*應用程式會傳回*serviceA azureuser* pod 的回應。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收回應，並將回應路由至*serviceA* pod 中的*serviceA*應用程式容器。
1. *ServiceA*應用程式會接收回應，然後傳回自己的回應。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收來自*serviceA*應用程式容器的回應，並將回應路由至叢集外的原始呼叫端。

所有其他非 HTTP 的 TCP 流量都會通過未修改的輸入控制器和 devspaces proxy 容器。

### <a name="how-running-your-code-is-configured"></a>如何設定執行您的程式碼

Azure Dev Spaces 會使用 `azds.yaml` 檔案來安裝和設定您的服務。 控制器會使用 `azds.yaml` 檔案中的 `install` 屬性來安裝 Helm 圖表，並建立 Kubernetes 物件：

```yaml
...
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
...
```

根據預設，`prep` 命令會產生 Helm 圖表。 它也會將 [*安裝] 圖表*屬性設定為 Helm 圖表的目錄。 如果您想要在不同位置使用 Helm 圖，可以更新此屬性，以使用該位置。

安裝 Helm 圖表時，Azure Dev Spaces 會提供覆寫 Helm 圖表中之值的方法。 Helm 圖表的預設值為 `charts/APP_NAME/values.yaml`。

您可以使用*install. values*屬性來列出一個或多個檔案，這些檔案會定義您想要在 Helm 圖中取代的值。 例如，如果您想要在開發人員空間中執行應用程式時，特別要有主機名稱或資料庫設定，您可以使用此覆寫功能。 您也可以加入 *？* 在任何檔案名的結尾，將它設定為選擇性。

[ *Install* ] （設定）屬性可讓您設定要在 Helm 圖中取代的一個或多個值。 在*install. set 中設定*的任何值都會覆寫在 [*安裝*] 中所列之檔案中所設定的值。 [ *Install* ] 底下的屬性取決於 Helm 圖中的值，而且可能會根據產生的 Helm 圖表而有所不同。

在上述範例中， *replicaCount*屬性會告訴控制器您的應用程式有多少實例要在您的開發人員空間中執行。 根據您的案例而定，您可以增加這個值，但它會影響將偵錯工具附加至應用程式的 pod。 如需詳細資訊，請參閱[疑難排解文章](troubleshooting.md)。

在產生的 Helm 圖表中，容器映射會設定為 *{{。值。圖像儲存機制}}： {{。值。圖像標記}}* 。 `azds.yaml` 檔案預設會*將 install. image. tag*屬性定義為 *$ （tag）* ，這會當做 {{的值使用 *。值。圖像標記}}* 。 藉由以這種方式設定*install. tag*屬性，可讓您應用程式的容器映射在執行 Azure Dev Spaces 時以不同的方式標記。 在此特定案例中，影像會標記為*影像中的\<值。存放庫 >： $ （標記）* 。 您必須使用 *$ （標記）* 變數作為 [ *install* ] 的值，才能辨識 DEV Spaces 並在 AKS 叢集中尋找容器。

在上述範例中，`azds.yaml` 定義了*install. set. hosts*。 [ *Install* ] 屬性會定義公用端點的主機名稱格式。 這個屬性也會使用 *$ （spacePrefix）* 、 *$ （rootSpacePrefix）* 和 *$ （hostSuffix）* ，這是控制器所提供的值。 

*$ （SpacePrefix）* 是子開發人員空間的名稱，其採用的格式為*SPACENAME*。 *$ （RootSpacePrefix）* 是父空間的名稱。 例如，如果*azureuser*是*預設*的子空間， *$ （rootSpacePrefix）* 的值就是*default* ， *$ （spacePrefix）* 的值則是*azureuser. s*。 如果空間不是子空間， *$ （spacePrefix）* 是空白。 例如，如果*預設*空間沒有父空間， *$ （rootSpacePrefix）* 的值為*default* ， *$ （spacePrefix）* 的值則為空白。 *$ （HostSuffix）* 是 DNS 尾碼，指向在您的 AKS 叢集中執行的 Azure Dev Spaces 輸入控制器。 此 DNS 尾碼對應至萬用字元 DNS 專案，例如 *\*。* Azure Dev Spaces 控制器新增至 AKS 叢集時所建立的 RANDOM_VALUE eus. azds。

在上述 `azds.yaml` 檔案中，您也可以更新*install. set. hosts*來變更應用程式的主機名稱。 例如，如果您想要將應用程式的主機名稱從 *$ （spacePrefix） $ （rootSpacePrefix） webfrontend $ （hostSuffix）* 簡化為 $ （spacePrefix） $ （rootSpacePrefix） *Web $ （hostSuffix）* 。

若要為您的應用程式建立容器，控制器會使用 `azds.yaml` 設定檔的下列區段：

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

控制器會使用 Dockerfile 來建立及執行您的應用程式。

[ *Build. coNtext* ] 屬性會列出 dockerfile 所在的目錄。 *Dockerfile*屬性會定義 dockerfile 的名稱，以建立應用程式的實際執行版本。 *Dockerfile*屬性會針對應用程式的開發版本設定 dockerfile 的名稱。

針對開發和生產環境使用不同的 Dockerfile，可讓您在開發期間啟用某些專案，並針對生產環境部署停用這些專案。 例如，您可以在開發期間啟用偵錯工具或更詳細的記錄，並在生產環境中停用。 如果您的 Dockerfile 名稱不同或位於不同的位置，您也可以更新這些屬性。

為了協助您在開發期間快速反復查看，Azure Dev Spaces 會同步處理本機專案的變更，並以累加方式更新您的應用程式。 `azds.yaml` 設定檔中的下一節是用來設定同步處理和更新：

```yaml
...
configurations:
  develop:
    ...
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
...
```

將同步變更的檔案和目錄會列在 [設定]. [*開發] 容器*中。 這些目錄一開始會在您執行 `up` 命令以及偵測到變更時進行同步處理。 如果您想要將其他或不同的目錄同步到您的開發人員空間，您可以變更此屬性。

*BuildCommands*屬性會指定如何在開發案例中建立應用程式。 [設定] 會*提供命令，* 讓您在開發案例中執行應用程式。 如果您想要在開發期間使用其他組建或執行時間旗標或參數，您可能會想要更新其中一個屬性。

*ProcessesToKill*會列出要終止的進程，以停止應用程式。 如果您想要在開發期間變更應用程式的重新開機行為，您可能會想要更新此屬性。 例如，如果您更新了*buildCommands*或設定，請使用 [*開發*] 來變更應用程式的建立或啟動方式，您可能需要變更停止的進程。

使用 `azds prep` 命令來準備程式碼時，您可以加入宣告 `--public` 旗標。 新增 `--public` 旗標會為您的應用程式建立可公開存取的 URL。 如果您省略此旗標，則只能在叢集內或使用 localhost 通道來存取應用程式。 執行 `azds prep` 命令之後，您可以變更此設定，請修改 `charts/APPNAME/values.yaml`中的 [*已啟用*] 屬性：

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>偵錯工具代碼

針對 JAVA、.NET 和 node.js 應用程式，您可以使用 Visual Studio Code 或 Visual Studio，在開發人員空間中直接對執行的應用程式進行 debug。 Visual Studio Code 和 Visual Studio 提供工具來連接到您的開發人員空間、啟動應用程式，以及附加偵錯工具。 執行 `azds prep`之後，您可以在 Visual Studio Code 或 Visual Studio 中開啟專案。 Visual Studio Code 或 Visual Studio 會產生自己的設定檔來進行連線，而這與執行 `azds prep`不同。 從 Visual Studio Code 或 Visual Studio 中，您可以設定中斷點，並啟動應用程式到您的開發人員空間。

![偵錯工具代碼](media/get-started-node/debug-configuration-nodejs2.png)

當您使用 Visual Studio Code 或 Visual Studio 進行偵錯工具啟動應用程式時，它們會以執行 `azds up`的相同方式，來處理啟動和連接到您的開發人員空間。 Visual Studio Code 和 Visual Studio 中的用戶端工具也會提供額外的參數，其中包含用於進行偵錯工具的特定資訊。 參數包含偵錯工具映射的名稱、偵錯工具映射中的偵錯工具位置，以及應用程式容器內用來掛接偵錯工具資料夾的目的地位置。

偵錯工具映射是由用戶端工具自動決定。 它會使用類似于 Dockerfile 和 Helm 圖表在執行 `azds prep`時所產生的方法。 偵錯工具裝載于應用程式的映射之後，就會使用 `azds exec`來執行。

## <a name="sharing-a-dev-space"></a>共用開發人員空間

與小組合作時，您可以在[整個小組之間共用開發人員空間](how-to/share-dev-spaces.md)，並建立衍生的 dev spaces。 具有 dev 空間之資源群組之參與者存取權的任何人都可以使用開發人員空間。

您也可以建立一個衍生自另一個開發人員空間的新開發人員空間。 當您建立衍生的開發人員空間時， *azds.io/parent-space=PARENT-SPACE-NAME*標籤會加入至衍生的開發人員空間的命名空間。 此外，父開發人員空間中的所有應用程式都會與衍生的開發人員空間共用。 如果您將應用程式的更新版本部署至衍生的開發人員空間，它只會存在於衍生的開發人員空間中，而父開發人員空間則不會受到影響。 您最多可以有三個層級的衍生 dev spaces 或*祖*空白字元。

衍生的開發人員空間也會在其本身的應用程式與從其父系共用的應用程式之間，以智慧方式路由傳送要求。 路由的運作方式是嘗試將要求路由至衍生的開發人員空間中的應用程式，並從父開發人員空間回到共用應用程式。 如果應用程式不在父空間中，路由將會切換回祖系空間中的共用應用程式。

例如：
* 開發人員空間的*預設值*有應用程式*serviceA*和*serviceB* 。
* [開發人員空間] *azureuser*衍生自*預設值*。
* *ServiceA*的更新版本會部署到*azureuser*。

使用*azureuser*時，對*serviceA*的所有要求都會路由至*azureuser*中的更新版本。 *ServiceB*的要求會先嘗試路由至*serviceB*的*azureuser*版本。 因為它不存在，所以會路由傳送至*預設*版本的*serviceB*。 如果移除*serviceA*的*azureuser*版本，所有對*serviceA*的要求都會切換回使用*預設*的*serviceA*版本。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure Dev Spaces，請參閱下列快速入門：

* [使用 CLI 和 Visual Studio Code 的 JAVA](quickstart-java.md)
* [使用 CLI 和 Visual Studio Code 的 .NET Core](quickstart-netcore.md)
* [具有 Visual Studio 的 .NET Core](quickstart-netcore-visualstudio.md)
* [使用 CLI 和 Visual Studio Code 的 node.js](quickstart-nodejs.md)

若要開始進行小組開發，請參閱下列操作說明文章：

* [小組開發-使用 CLI 和 Visual Studio Code 的 JAVA](team-development-java.md)
* [小組開發-使用 CLI 和 Visual Studio Code 的 .NET Core](team-development-netcore.md)
* [小組開發-具有 Visual Studio 的 .NET Core](team-development-netcore-visualstudio.md)
* [小組開發-使用 CLI 和 Visual Studio Code 的 node.js](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
