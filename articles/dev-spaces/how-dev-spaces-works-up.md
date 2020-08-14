---
title: 如何搭配 Azure Dev Spaces 執行您的程式碼
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes Service 上使用 Azure Dev Spaces 執行程式碼的流程
keywords: azds. yaml，Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器
ms.openlocfilehash: 9dbc1f0f21c2883e5caadbdae268a515eb94d145
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208688"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>如何搭配 Azure Dev Spaces 執行您的程式碼

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 當您的 [專案準備好要在開發人員空間中執行][how-it-works-prep]之後，您就可以使用 dev Spaces，在 AKS 叢集中建立並執行您的專案。

本文說明在 AKS 中使用 Dev Spaces 執行程式碼的情況。

## <a name="run-your-code"></a>執行您的程式碼

若要在開發人員空間中執行您的程式碼，請 `up` 在與檔案相同的目錄中發出命令 `azds.yaml` ：

```cmd
azds up
```

命令會將 `up` 您的應用程式來源檔案和其他成品上傳到開發人員空間，以建立並執行您的專案。 在此，您的開發人員空間中的控制器：

1. 建立 Kubernetes 物件以部署您的應用程式。
1. 建立應用程式的容器。
1. 將您的應用程式部署至開發人員空間。
1. 如果已設定，則為您的應用程式端點建立可公開存取的 DNS 名稱。
1. 使用 *埠向前轉送* ，以使用來提供應用程式端點的存取權 http://localhost 。
1. 將 stdout 和 stderr 轉送至用戶端工具。


## <a name="starting-a-service"></a>啟動服務

當您在開發人員空間中啟動服務時，用戶端工具和控制器會協調以同步處理您的來源檔案、建立容器和 Kubernetes 物件，以及執行您的應用程式。

在更細微的層級中，當您執行時，會發生 `azds up` 下列情況：

1. 檔案會從使用者的電腦[同步][sync-section]處理到使用者的 AKS 叢集獨有的 Azure 檔案儲存體。 已上傳原始程式碼、Helm 圖和設定檔案。
1. 控制器會建立一個要求來啟動新的會話。 此要求包含數個屬性，包括唯一的識別碼、空間名稱、原始程式碼的路徑，以及偵錯工具旗標。
1. 控制器會以唯一的會話識別碼取代 Helm 圖中的 *$ (標記) * 預留位置，並為您的服務安裝 Helm 圖表。 將唯一會話識別碼的參考新增至 Helm 圖表，可讓針對此特定會話部署至 AKS 叢集的容器，系結回到會話要求和相關聯的資訊。
1. 在安裝 Helm 圖期間，Kubernetes webhook 許可伺服器會將額外的容器新增至您應用程式的 pod，以進行檢測並存取專案的原始程式碼。 新增 devspaces-proxy 和 devspaces proxy-init 容器，以提供 HTTP 追蹤和空間路由。 已新增 devspaces-build 容器，以提供可存取 Docker 實例和專案原始程式碼的 pod，以建立應用程式的容器。
1. 啟動應用程式的 pod 時，會使用 devspaces-build 容器和 devspaces proxy-init 容器來建立應用程式容器。 接著會啟動應用程式容器和 devspaces proxy 容器。
1. 在應用程式容器啟動之後，用戶端功能會使用 Kubernetes 的 *埠轉送* 功能，透過將 HTTP 存取提供給您的應用程式 http://localhost 。 此埠轉送會將您的開發電腦連接到您的開發人員空間中的服務。
1. 當 pod 中的所有容器都已啟動時，服務就會在執行中。 此時，用戶端功能會開始串流 HTTP 追蹤、stdout 和 stderr。 這項資訊是由開發人員的用戶端功能所顯示。

## <a name="updating-a-running-service"></a>正在更新執行中的服務

當服務正在執行時，如果有任何專案來源檔案變更，Azure Dev Spaces 就能夠更新該服務。 根據變更的檔案類型，Dev Spaces 也會以不同方式處理服務的更新。 開發人員空間有三種方式可以更新執行中的服務：

* 直接更新檔案
* 在執行中應用程式的容器內重建並重新啟動應用程式的進程
* 重建和重新部署應用程式的容器

![Azure Dev Spaces 檔案同步](media/how-dev-spaces-works/file-sync.svg)

某些屬於靜態資產的專案檔，例如 html、css 和 cshtml 檔案，可以直接在應用程式的容器中更新，而不需要重新開機任何專案。 如果靜態資產變更，新的檔案就會同步處理到開發人員空間，然後由執行中的容器使用。

您可以在執行中的容器內重新開機應用程式的進程，以套用原始程式碼或應用程式佈建檔等檔案的變更。 這些檔案一旦同步處理之後，應用程式就會在執行中的容器內使用 *devhostagent* 進程重新開機。 一開始建立應用程式的容器時，控制器會將應用程式的啟動命令取代為不同的進程，稱為 *devhostagent*。 然後，應用程式的實際進程會在 *devhostagent*下以子進程的形式執行，並使用 *devhostagent*的輸出輸送輸出。 *Devhostagent*程式也是 dev spaces 的一部分，而且可以代表 dev spaces 在執行中的容器中執行命令。 執行重新開機時，請 *devhostagent*：

* 停止目前的進程或與應用程式相關聯的進程
* 重建應用程式
* 重新開機與應用程式相關聯的進程

*Devhostagent*執行上述步驟的方式是[在中 `azds.yaml` 設定][azds-yaml-section]。

專案檔（例如 Dockerfile、.csproj 檔案或 Helm 圖表的任何部分）的更新需要重建和重新部署應用程式的容器。 當其中一個檔案同步處理到開發人員空間時，控制器會執行 [helm upgrade][helm-upgrade] 命令，並重建並重新部署應用程式的容器。

## <a name="file-synchronization"></a>檔案同步處理

第一次在開發人員空間中啟動應用程式時，會上傳所有應用程式的來源檔案。 當應用程式正在執行，且稍後重新開機時，只會上傳變更的檔案。 有兩個檔案用來協調這個進程：用戶端檔案和控制器端檔案。

用戶端檔案會儲存在臨時目錄中，並根據您在 Dev Spaces 中執行之專案目錄的雜湊來命名。 例如，在 Windows 上，您的專案會有類似 *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* 的檔案。 在 Linux 上，用戶端檔案會儲存在 */tmp* 目錄中。 您可以執行命令，在 macOS 上尋找目錄 `echo $TMPDIR` 。

這個檔案是 JSON 格式，而且包含：

* 與開發人員空間同步處理之每個專案檔的專案
* 同步處理識別碼
* 上次同步作業的時間戳記

每個專案檔案專案都包含檔案的路徑和其時間戳記。

控制器端檔案會儲存在 AKS 叢集上。 其中包含上一次同步處理的同步處理識別碼和時間戳記。

同步處理時間戳記在用戶端與控制器端檔案之間不相符時，就會進行同步處理。 在同步處理期間，用戶端工具會逐一查看用戶端檔案中的檔案專案。 如果檔案的時間戳記在同步時間戳記之後，該檔案就會同步處理到開發人員空間。 同步處理完成後，就會在用戶端和控制器端檔案上更新同步時間戳記。

如果用戶端檔案不存在，則會同步處理所有專案檔案。 此行為可讓您藉由刪除用戶端檔案來強制執行完整同步處理。

## <a name="how-running-your-code-is-configured"></a>如何設定執行您的程式碼

Azure Dev Spaces 使用檔案 `azds.yaml` 來安裝和設定您的服務。 控制器會使用檔案 `install` 中的屬性 `azds.yaml` 來安裝 Helm 圖表，並建立 Kubernetes 物件：

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

根據預設，此 `prep` 命令會產生 Helm 圖表。 它也會將 [ *安裝] 圖表* 屬性設定為 Helm 圖表的目錄。 如果您想要在不同位置使用 Helm 圖，可以更新此屬性，以使用該位置。

安裝 Helm 圖表時，Azure Dev Spaces 會提供覆寫 Helm 圖表中之值的方法。 Helm 圖表的預設值為 `charts/APP_NAME/values.yaml` 。

您可以使用 *install. values* 屬性來列出一個或多個檔案，這些檔案會定義您想要在 Helm 圖中取代的值。 例如，如果您想要在開發人員空間中執行應用程式時，特別要有主機名稱或資料庫設定，您可以使用此覆寫功能。 您也可以加入 *？* 在任何檔案名的結尾，將它設定為選擇性。

[ *Install* ] （設定）屬性可讓您設定要在 Helm 圖中取代的一個或多個值。 在 *install. set 中設定* 的任何值都會覆寫在 [ *安裝*] 中所列之檔案中所設定的值。 [ *Install* ] 底下的屬性取決於 Helm 圖中的值，而且可能會根據產生的 Helm 圖表而有所不同。

在上述範例中， *replicaCount* 屬性會告訴控制器您的應用程式有多少實例要在您的開發人員空間中執行。 根據您的案例而定，您可以增加這個值，但它會影響將偵錯工具附加至應用程式的 pod。 如需詳細資訊，請參閱 [疑難排解文章][troubleshooting]。

在產生的 Helm 圖表中，容器映射會設定為 *{{。值。圖像儲存機制}}： {{。值。圖像標記}}*。 檔案 `azds.yaml` 預設會將 *install. image. tag* 屬性定義為 *$ (* 標籤) ，這會當做 {{的值使用 *。值。圖像標記}}*。 藉由以這種方式設定 *install. tag* 屬性，可讓您應用程式的容器映射在執行 Azure Dev Spaces 時以不同的方式標記。 在此特定案例中，影像會標記為* \<value from image.repository> ： $ (* 標籤) 。 您必須使用 *$ (* 標籤) 變數做為 install.   *tag* 的值，才能辨識 DEV Spaces 並在 AKS 叢集中尋找容器。

在上述範例中，會 `azds.yaml` 定義 *install. set. hosts*。 [ *Install* ] 屬性會定義公用端點的主機名稱格式。 這個屬性也會使用 *$ (spacePrefix) *、 *$ (rootSpacePrefix) *和 *$ (hostSuffix) *，這是控制器所提供的值。

*$ (spacePrefix) *是子開發人員空間的名稱，其採用的格式為*SPACENAME*。 *$ (rootSpacePrefix) *是父空間的名稱。 例如，如果*azureuser*是*預設*的子空間， *$ (rootSpacePrefix) *的值為*default* ，而 *$ (spacePrefix) *的值為*azureuser。* 如果空間不是子空間， *$ (spacePrefix) * 就會是空白的。 例如，如果 *預設* 空間沒有父空間， *$ (rootSpacePrefix) * 的值為 *Default* ，而 *$ (spacePrefix) * 的值為空白。 *$ (hostSuffix) *是一個 DNS 尾碼，指向在您的 AKS 叢集中執行的 Azure Dev Spaces 輸入控制器。 此 DNS 尾碼會對應至萬用字元 DNS 專案，例如* \* 。* Azure Dev Spaces 控制器新增至 AKS 叢集時所建立的 RANDOM_VALUE eus. azds。

在上述檔案中 `azds.yaml` ，您也可以更新 *install. set. hosts* 來變更應用程式的主機名稱。 例如，如果您想要簡化應用程式的主機名稱，請從 *$ (spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix) * 到 *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix) *。

若要為您的應用程式建立容器，控制器會使用設定檔的下列區段 `azds.yaml` ：

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

[ *Build. coNtext* ] 屬性會列出 dockerfile 所在的目錄。 *build.dockerfile*屬性會定義 Dockerfile 的名稱，以建立應用程式的實際執行版本。 *configurations.develop.build.dockerfile*屬性會針對應用程式的開發版本設定 Dockerfile 的名稱。

針對開發和生產環境使用不同的 Dockerfile，可讓您在開發期間啟用某些專案，並針對生產環境部署停用這些專案。 例如，您可以在開發期間啟用偵錯工具或更詳細的記錄，並在生產環境中停用。 如果您的 Dockerfile 名稱不同或位於不同的位置，您也可以更新這些屬性。

為了協助您在開發期間快速反復查看，Azure Dev Spaces 會同步處理本機專案的變更，並以累加方式更新您的應用程式。 設定檔中的下一節 `azds.yaml` 是用來設定同步處理和更新：

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

將同步變更的檔案和目錄會列在 [設定]. [ *開發] 容器* 中。 這些目錄一開始會在您執行命令以及偵測 `up` 到變更時進行同步處理。 如果您想要將其他或不同的目錄同步到您的開發人員空間，您可以變更此屬性。

*BuildCommands*屬性會指定如何在開發案例中建立應用程式。 [設定] 會 *提供命令，* 讓您在開發案例中執行應用程式。 如果您想要在開發期間使用其他組建或執行時間旗標或參數，您可能會想要更新其中一個屬性。

*ProcessesToKill*會列出要終止的進程，以停止應用程式。 如果您想要在開發期間變更應用程式的重新開機行為，您可能會想要更新此屬性。 例如，如果您更新了 *buildCommands* 或設定，請使用 [ *開發* ] 來變更應用程式的建立或啟動方式，您可能需要變更停止的進程。

使用命令來準備程式碼時 `azds prep` ，您可以選擇新增 `--enable-ingress` 旗標。 新增旗標會 `--enable-ingress` 為您的應用程式建立可公開存取的 URL。 如果您省略此旗標，則只能在叢集內或使用 localhost 通道來存取應用程式。 執行 `azds prep` 命令之後，您可以變更此設定，修改中的 [ *啟用* ] 屬性 `charts/APPNAME/values.yaml` ：

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解網路功能以及如何在中路由傳送要求 Azure Dev Spaces 查看 [路由如何與 Azure Dev Spaces 搭配運作][how-it-works-routing]。

若要深入瞭解如何使用 Azure Dev Spaces 快速反復查看和開發，請參閱 [使用 Kubernetes 的本機程式如何運作][how-it-works-local-process-kubernetes] ，以及 [如何使用 Azure Dev Spaces 進行遠端偵錯程式碼][how-it-works-remote-debugging]。


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-local-process-kubernetes]: /visualstudio/containers/overview-local-process-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md