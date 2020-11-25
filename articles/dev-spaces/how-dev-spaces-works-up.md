---
title: 使用 Azure Dev Spaces 執行您的程式碼的運作方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes Service 上使用 Azure Dev Spaces 執行程式碼的程式
keywords: azds. yaml、Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014426"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>使用 Azure Dev Spaces 執行您的程式碼的運作方式

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 當您的 [專案準備好在開發人員空間中執行時][how-it-works-prep]，您可以使用 dev Spaces 在您的 AKS 叢集中建立並執行您的專案。

本文說明在 AKS 中使用 Dev Spaces 執行程式碼時，會發生什麼事。

## <a name="run-your-code"></a>執行您的程式碼

若要在開發人員空間中執行您的程式碼，請 `up` 在與您的檔案相同的目錄中發出命令 `azds.yaml` ：

```cmd
azds up
```

此 `up` 命令會將您的應用程式原始程式檔以及建立和執行專案所需的其他成品上傳至開發人員空間。 從該處，您的開發人員空間中的控制器：

1. 建立 Kubernetes 物件，以部署您的應用程式。
1. 為您的應用程式建立容器。
1. 將您的應用程式部署至開發人員空間。
1. 如果已設定，就會為您的應用程式端點建立可公開存取的 DNS 名稱。
1. 使用 *埠轉送* ，以提供應用程式端點的存取權 http://localhost 。
1. 將 stdout 和 stderr 轉送至用戶端工具。


## <a name="starting-a-service"></a>啟動服務

當您在開發人員空間中啟動服務時，用戶端工具和控制器會進行協調以同步處理您的原始程式檔、建立容器和 Kubernetes 物件，以及執行您的應用程式。

在更細微的層級，以下是執行時所發生的情況 `azds up` ：

1. 檔案會從使用者的電腦[同步][sync-section]處理到使用者 AKS 叢集特有的 Azure 檔案儲存體。 原始程式碼、Helm 圖表和設定檔上傳。
1. 控制器會建立新會話的啟動要求。 此要求包含數個屬性，包括唯一識別碼、空間名稱、原始程式碼的路徑，以及偵錯工具旗標。
1. 控制器會以唯一的會話識別碼取代 Helm 圖中的 *$ (標記)* 預留位置，並安裝服務的 Helm 圖表。 將唯一會話識別碼的參考新增至 Helm 圖表，可讓此特定會話部署到 AKS 叢集的容器連結回會話要求和相關聯的資訊。
1. 在安裝 Helm 圖期間，Kubernetes webhook 許可伺服器會將額外的容器新增至您應用程式的 pod，以進行檢測並存取專案的原始程式碼。 系統會新增 microsoft.devspaces proxy 和 microsoft.devspaces proxy-init 容器以提供 HTTP 追蹤和空間路由。 系統會新增 microsoft.devspaces 組建容器，以提供 pod 以存取 Docker 實例和專案原始程式碼，以建立應用程式的容器。
1. 當應用程式的 pod 啟動時，會使用 microsoft.devspaces 組建容器和 microsoft.devspaces proxy-init 容器來建立應用程式容器。 然後會啟動應用程式容器和 microsoft.devspaces proxy 容器。
1. 應用程式容器啟動之後，用戶端功能會使用 Kubernetes 的 *埠轉送* 功能，以提供應用程式的 HTTP 存取 http://localhost 。 此埠轉送會將您的開發電腦連接到開發人員空間中的服務。
1. 當 pod 中的所有容器都已啟動時，服務就會執行。 此時，用戶端功能會開始串流 HTTP 追蹤、stdout 和 stderr。 開發人員的用戶端功能會顯示這項資訊。

## <a name="updating-a-running-service"></a>正在更新執行中的服務

當服務正在執行時，如果有任何專案來源檔案變更，Azure Dev Spaces 可以更新該服務。 開發人員空間也會根據變更的檔案類型，以不同的方式來處理更新服務。 開發人員空間可更新執行中服務的方式有三種：

* 直接更新檔案
* 在正在執行的應用程式容器內重建並重新啟動應用程式的進程
* 重建和重新部署應用程式的容器

![Azure Dev Spaces 檔案同步](media/how-dev-spaces-works/file-sync.svg)

某些靜態資產（例如 html、css 和 cshtml 檔案）的特定專案檔可以直接在應用程式的容器中更新，而不需要重新開機任何專案。 如果靜態資產變更，則新的檔案會同步至開發空間，然後由執行中的容器使用。

您可以在執行中的容器內重新開機應用程式的進程，以套用原始程式碼或應用程式佈建檔等檔案的變更。 這些檔案在同步處理之後，就會使用 *devhostagent* 程式在執行中的容器內重新開機應用程式的進程。 在一開始建立應用程式的容器時，控制器會以稱為 *devhostagent* 的不同進程來取代應用程式的啟動命令。 然後應用程式的實際進程會以 *devhostagent* 下的子進程的形式執行，並使用 *devhostagent* 的輸出將其輸出輸送。 *Devhostagent* 程式也是開發人員空間的一部分，而且可以代表 dev spaces 在執行中的容器中執行命令。 執行重新開機時，請 *devhostagent*：

* 停止目前的進程或與應用程式相關聯的進程
* 重建應用程式
* 重新開機與應用程式相關聯的進程

*Devhostagent* 執行上述步驟的方式是 [在中 `azds.yaml` 設定][azds-yaml-section]。

更新專案檔（例如 Dockerfile、.csproj 檔案或 Helm 圖表的任何部分）需要重建和重新部署應用程式的容器。 當這些檔案的其中一個同步處理至開發人員空間時，控制器會執行 [helm upgrade][helm-upgrade] 命令，並重建並重新部署應用程式的容器。

## <a name="file-synchronization"></a>檔案同步處理

應用程式第一次在開發人員空間中啟動時，所有應用程式的原始程式檔都會上傳。 當應用程式正在執行，且稍後重新開機時，只會上傳已變更的檔案。 有兩個檔案用來協調此進程：用戶端檔案和控制器端檔案。

用戶端檔案會儲存在臨時目錄中，並根據您在 Dev Spaces 中執行之專案目錄的雜湊來命名。 例如，在 Windows 上，您的專案會有類似 *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* 的檔案。 在 Linux 上，用戶端檔案會儲存在 */tmp* 目錄中。 您可以藉由執行命令，在 macOS 上尋找目錄 `echo $TMPDIR` 。

此檔案是 JSON 格式，包含：

* 與開發人員空間同步處理的每個專案檔案的專案
* 同步處理識別碼
* 上次同步處理作業的時間戳記

每個專案檔專案都包含檔案的路徑及其時間戳記。

控制器端檔案儲存在 AKS 叢集上。 它包含上次同步處理的同步處理識別碼和時間戳記。

當用戶端和控制器端檔案之間的同步處理時間戳記不相符時，就會進行同步處理。 在同步處理期間，用戶端工具會逐一查看用戶端檔案中的檔案專案。 如果檔案的時間戳記是在同步時間戳記之後，該檔案就會同步處理至開發空間。 同步處理完成後，用戶端和控制器端檔案上的同步時間戳記都會更新。

如果用戶端檔案不存在，則會同步處理所有的專案檔案。 此行為可讓您藉由刪除用戶端檔案來強制執行完整同步處理。

## <a name="how-running-your-code-is-configured"></a>如何設定執行程式碼

Azure Dev Spaces 使用檔案 `azds.yaml` 來安裝和設定您的服務。 控制器會使用檔案 `install` 中的屬性 `azds.yaml` 來安裝 Helm 圖，並建立 Kubernetes 物件：

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

根據預設，此 `prep` 命令會產生 Helm 圖表。 它也會將 [Helm *] 屬性設定為 [圖表]* 的目錄。 如果您想要在不同的位置使用 Helm 圖表，您可以更新此屬性以使用該位置。

安裝 Helm 圖時，Azure Dev Spaces 提供覆寫 Helm 圖表中值的方法。 Helm 圖的預設值為 `charts/APP_NAME/values.yaml` 。

您可以使用 [ *install* ] 屬性來列出一或多個檔案，這些檔案會定義您想要在 Helm 圖中取代的值。 例如，如果您想要在開發人員空間中執行應用程式時明確地想要主機名稱或資料庫設定，則可以使用此覆寫功能。 您也可以新增 *？* 在任何檔案名的結尾，將它設定為選擇性。

[ *安裝* ] 屬性可讓您設定要在 Helm 圖中取代的一或多個值。 安裝時所 *設定* 的任何值都會覆寫 [ *install. values*] 中所列檔案中所設定的值。 [ *安裝* ] 下的屬性取決於 Helm 圖中的值，而且可能會因產生的 Helm 圖表而有所不同。

在上述範例中， *replicaCount* 屬性會告訴控制器要在您的開發空間中執行的應用程式實例數目。 視您的案例而定，您可以增加此值，但它會影響將偵錯工具附加至應用程式的 pod。 如需詳細資訊，請參閱 [疑難排解文章][troubleshooting]。

在產生的 Helm 圖中，容器映射設為 *{{。值。儲存機制}}： {{。Values. tag}}*。 檔案 `azds.yaml` 預設會將 *install. tag* 屬性定義為 *$ (tag)* ，以作為 {{的值使用 *。Values. tag}}*。 藉由以這種方式設定 *install. tag* 屬性，可讓您的應用程式的容器映射在執行 Azure Dev Spaces 時，以不同的方式標記。 在此特定案例中，會將影像標記為 *\<value from image.repository> ： $ (* 標籤) 。 您必須使用 *$ (* 標籤) 變數作為   *install* .. 標籤的值，以便開發人員空間辨識和尋找 AKS 叢集中的容器。

在上述範例中， `azds.yaml` 定義了 *install. hosts*。 *Install* 屬性會定義公用端點的主機名稱格式。 此屬性也會使用 *$ (spacePrefix)*、 *$ (rootSpacePrefix)* 和 *$ (hostSuffix)*，這些是控制器所提供的值。

*$ (spacePrefix)* 是子開發空間的名稱，它採用 *SPACENAME* 的形式。 *$ (rootSpacePrefix)* 是父空間的名稱。 例如，如果 *>azureuser* 是 *預設* 的子空間， *$ (rootSpacePrefix)* 的值為 *預設* 值，而 *$ (spacePrefix)* 的值為 *>azureuser。* 如果空間不是子空間， *$ (spacePrefix)* 空白。 例如，如果 *預設* 空間沒有父空間， *$ (rootSpacePrefix)* 的值為 *預設* 值，而 *$ (spacePrefix)* 的值為空白。 *$ (hostSuffix)* 是指向 AKS 叢集中執行之 Azure Dev Spaces 輸入控制器的 DNS 尾碼。 例如，此 DNS 尾碼會對應到萬用字元 DNS 專案 *\* 。* Azure Dev Spaces 控制器新增至 AKS 叢集時所建立的 RANDOM_VALUE eus. azds。

在上述檔案中 `azds.yaml` ，您也可以更新 *install* ，以變更應用程式的主機名稱。 例如，如果您想要簡化應用程式的主機名稱，請從 *$ (spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix)* 至 *$ (spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix)*。

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

控制器會使用 Dockerfile 來建立和執行您的應用程式。

*Build. coNtext* 屬性會列出 dockerfile 存在的目錄。 *build.dockerfile* 屬性會定義用來建立應用程式實際執行版本的 Dockerfile 名稱。 *configurations.develop.build.dockerfile* 屬性會設定應用程式開發版本的 Dockerfile 名稱。

針對開發和生產環境使用不同的 Dockerfile，可讓您在開發期間啟用某些專案，並停用這些專案以進行生產部署。 例如，您可以在開發期間啟用偵錯工具或更詳細的記錄，並在生產環境中停用。 如果您的 Dockerfile 命名方式不同或位於不同的位置，您也可以更新這些屬性。

為了協助您在開發期間快速進行反覆運算，Azure Dev Spaces 將會同步處理您的本機專案的變更，並以累加方式更新您的應用程式。 設定檔中的下一節 `azds.yaml` 會用來設定同步與更新：

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

將同步處理變更的檔案和目錄會列在 [設定]. [ *.sync* ] 屬性中。 當您執行命令以及偵測到變更時，就會一開始同步處理這些目錄 `up` 。 如果您想要將其他或不同的目錄同步至開發人員空間，您可以變更此屬性。

*BuildCommands* 屬性會指定如何在開發案例中建立應用程式。 [設定 *] 屬性會* 提供在開發案例中執行應用程式的命令。 如果您想要在開發期間使用額外的組建或執行時間旗標或參數，您可能會想要更新其中一個屬性。

*ProcessesToKill* 會列出要終止的進程，以停止應用程式。 如果您想要在開發期間變更應用程式的重新開機行為，您可能會想要更新此屬性。 例如，如果您更新了 *buildCommands* *或設定。若* 要變更建立或啟動應用程式的方式，您可能需要變更已停止的進程的程式。

使用命令準備程式碼時 `azds prep` ，您可以選擇新增 `--enable-ingress` 旗標。 新增旗標會 `--enable-ingress` 為您的應用程式建立可公開存取的 URL。 如果您省略此旗標，則只能在叢集內或使用 localhost 通道來存取應用程式。 執行 `azds prep` 命令之後，您可以變更此設定，以修改中的 [ *已啟用* ] 屬性 `charts/APPNAME/values.yaml` ：

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解網路功能以及如何在 Azure Dev Spaces 中路由要求，請參閱 [路由如何搭配 Azure Dev Spaces 運作][how-it-works-routing]。

若要深入瞭解如何使用 Kubernetes 快速逐一查看和開發，請參閱 [Kubernetes 的橋樑如何運作][how-it-works-bridge-to-kubernetes] ，以及 [如何使用 Azure Dev Spaces 進行遠端偵錯程式碼的運作方式][how-it-works-remote-debugging]。


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md