---
title: 使用 Azure 開發空間運行代碼的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 開發人員空間在 Azure 庫伯內斯服務上運行代碼的過程
keywords: azds.yaml， Azure 開發空間， 開發空間， Docker， 庫伯奈斯， Azure， AKS， Azure 庫伯奈斯服務， 容器
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241357"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>使用 Azure 開發空間運行代碼的工作原理

Azure 開發人員空間為您提供了多種方法來快速反覆運算和調試 Kubernetes 應用程式，並與您的團隊協作處理 Azure Kubernetes 服務 （AKS） 群集。 一旦[專案準備在開發空間中運行][how-it-works-prep]，您可以使用開發人員空間在 AKS 群集中生成和運行專案。

本文介紹了在具有開發空間的 AKS 中運行代碼的情況。

## <a name="run-your-code"></a>運行代碼

要在開發空間中運行代碼，`up`請發出與`azds.yaml`檔相同的目錄中的命令：

```cmd
azds up
```

該`up`命令將生成和運行專案所需的應用程式原始檔案和其他專案上載到開發空間。 從那裡，開發空間中的控制器：

1. 創建庫伯奈斯物件以部署應用程式。
1. 為應用程式生成容器。
1. 將應用程式部署到開發空間。
1. 如果配置了應用程式終結點，則為應用程式終結點創建可公開訪問的 DNS 名稱。
1. 使用*埠轉發*提供對應用程式終結點的訪問。 http://localhost
1. 將穩貞和穩穩地轉發到用戶端工具。


## <a name="starting-a-service"></a>啟動服務

在開發空間中啟動服務時，用戶端工具和控制器協調工作，以同步原始檔案、創建容器和 Kubernetes 物件以及運行應用程式。

在更精細的級別上，下面是運行`azds up`時發生的情況：

1. [檔][sync-section]從使用者的電腦同步到使用者 AKS 群集獨有的 Azure 檔存儲。 將上載原始程式碼、Helm 圖表和設定檔。
1. 控制器創建啟動新會話的請求。 此請求包含多個屬性，包括唯一 ID、空格名稱、原始程式碼路徑和調試標誌。
1. 控制器將 Helm 圖表中的 *$（標記）* 預留位置替換為唯一的會話 ID，並為您的服務安裝 Helm 圖表。 向 Helm 圖表添加對唯一會話 ID 的引用允許將部署到此特定會話的 AKS 群集的容器綁定回會話請求和相關資訊。
1. 在安裝 Helm 圖表期間，Kubernetes Webhook 准入伺服器向應用程式的窗格中添加了其他容器，以便進行檢測並訪問專案的原始程式碼。 添加開發空間代理和開發空間代理-init 容器以提供 HTTP 跟蹤和空間路由。 添加 devspace-build 容器是為了向 pod 提供對 Docker 實例和專案原始程式碼的訪問，以便生成應用程式的容器。
1. 啟動應用程式的 pod 時，使用開發空間生成容器和開發空間代理 -init 容器來生成應用程式容器。 然後啟動應用程式容器和開發空間代理容器。
1. 應用程式容器啟動後，用戶端功能使用 Kubernetes*埠轉發*功能通過http://localhost提供對應用程式的 HTTP 訪問。 此埠轉發將開發電腦連接到開發空間中的服務。
1. 當窗格中的所有容器都啟動時，服務正在運行。 此時，用戶端功能開始資料流 HTTP 跟蹤、抖痕和穩穩。 此資訊由開發人員的用戶端功能顯示。

## <a name="updating-a-running-service"></a>更新正在運行的服務

在服務運行時，如果任何專案原始檔案出現更改，Azure 開發人員空間可以更新該服務。 Dev Spaces 還會根據更改的檔案類型以不同的方式處理更新服務。 開發空間可通過三種方式更新正在運行的服務：

* 直接更新檔
* 在正在運行的應用程式的容器內重建和重新開機應用程式的進程
* 重建和重新部署應用程式的容器

![Azure 開發空間檔同步](media/how-dev-spaces-works/file-sync.svg)

某些靜態資源的專案檔案（如 html、css 和 cshtml 檔）可以直接在應用程式的容器中更新，而無需重新開機任何內容。 如果靜態資產發生更改，則新檔將同步到開發空間，然後由正在運行的容器使用。

可以通過在正在運行的容器中重新開機應用程式的進程來應用對檔（如原始程式碼或應用程式佈建檔）的更改。 同步這些檔後，將使用*devhostagent*進程在正在運行的容器中重新開機應用程式的進程。 最初創建應用程式的容器時，控制器將應用程式的啟動命令替換為稱為*devhostagent*的不同進程。 然後，應用程式的實際進程在*devhostagent*下作為子進程運行，並且其輸出使用*devhostagent*的輸出進行管道輸出。 *devhostagent*進程也是開發空間的一部分，可以代表開發人員空間在正在運行的容器中執行命令。 執行重新開機時 *，devhostagent*：

* 停止與應用程式關聯的當前進程或進程
* 重建應用程式
* 重新開機與應用程式關聯的進程或進程

在[`azds.yaml`中配置][azds-yaml-section]*了 devhostagent*執行上述步驟的方式。

對專案檔案（如 Dockerfile、csproj 檔）或 Helm 圖表的任何部分的更新都需要重新生成和重新部署應用程式的容器。 當其中一個檔同步到開發空間時，控制器將運行[掌舵升級][helm-upgrade]命令，並重新生成和重新部署應用程式的容器。

## <a name="file-synchronization"></a>檔同步

第一次在開發空間中啟動應用程式時，將上載應用程式的所有原始檔案。 當應用程式正在運行並在以後重新開機時，僅上載已更改的檔。 兩個檔用於協調此過程：用戶端檔和控制器端檔。

用戶端檔存儲在臨時目錄中，並根據在開發人員空間中運行的專案目錄的雜湊值命名。 例如，在 Windows 上，您將有一個檔，如*使用者\USERNAME_AppData_Local_Temp_1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog。* 您的專案。 在 Linux 上，用戶端檔存儲在 */tmp*目錄中。 您可以通過運行`echo $TMPDIR`命令在 macOS 上找到目錄。

此檔採用 JSON 格式，包含：

* 與開發空間同步的每個專案檔案的條目
* 同步 ID
* 上次同步操作的時間戳記

每個專案檔案條目都包含檔的路徑及其時間戳記。

控制器端檔存儲在 AKS 群集上。 它包含同步 ID 和上次同步的時間戳記。

當同步時間戳記在用戶端和控制器端檔之間不匹配時，將發生同步。 在同步期間，用戶端工具會遍遍用戶端檔中的檔條目。 如果檔的時間戳記位於同步時間戳記之後，則該檔將同步到開發空間。 同步完成後，在用戶端和控制器端檔上更新同步時間戳記。

如果用戶端檔不存在，則所有專案檔案都同步。 此行為允許您通過刪除用戶端檔來強制完全同步。

## <a name="how-running-your-code-is-configured"></a>如何配置代碼的運行方式

Azure 開發空間使用`azds.yaml`該檔來安裝和佈建服務。 控制器使用`install``azds.yaml`檔中的屬性來安裝 Helm 圖表並創建 Kubernetes 物件：

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

預設情況下，該`prep`命令將生成 Helm 圖表。 它還將*安裝.chart*屬性設置為 Helm 圖表的目錄。 如果要在不同位置使用 Helm 圖表，可以更新此屬性以使用該位置。

安裝 Helm 圖表時，Azure 開發人員空間提供了一種覆蓋 Helm 圖表中的值的方法。 Helm 圖表的預設值在`charts/APP_NAME/values.yaml`中。

使用*install.value*屬性，可以列出一個或多個檔，這些檔定義要在 Helm 圖表中替換的值。 例如，如果需要主機名稱或資料庫配置，特別是在開發空間中運行應用程式時，則可以使用此覆蓋功能。 您還可以添加 *？* 在任何檔案名的末尾將其設置為可選。

*安裝.set*屬性允許您配置要在 Helm 圖表中替換的一個或多個值。 在*install.set*中配置的任何值都將覆蓋*在 install.值*中列出的檔中配置的值。 *install.set*下的屬性取決於 Helm 圖表中的值，並且可能因生成的 Helm 圖表而異。

在上面的示例中 *，install.set.replicaCount*屬性告訴控制器在開發空間中運行應用程式有多少實例。 根據您的方案，可以增加此值，但它會影響將調試器附加到應用程式的 pod。 有關詳細資訊，請參閱[故障排除文章][troubleshooting]。

在生成的 Helm 圖表中，容器圖像設置為 *{ } 。值.image.repository_：]。值.image.tag]*。 預設情況下`azds.yaml`，該檔將*安裝.set.image.tag*屬性定義為 *$（標記），* 它用作 *{ 的值。值.image.tag]*。 通過以這種方式設置*install.set.image.tag*屬性，它允許在運行 Azure 開發人員空間時以不同方式標記應用程式的容器映射。 在此特定情況下，圖像被標記為*\<圖像中的值>。* 您必須使用 *$（標記）* 變數作為*安裝.set.image.tag*的值，以便開發人員空間識別和定位 AKS 群集中的容器。

在上面的示例中，`azds.yaml`定義*安裝.set.ingress.hosts*。 *安裝.set.ingress.hosts*屬性為公共終結點定義主機名稱格式。 此屬性還使用 *$（空格首碼）$（**根空間首碼）* 和 *$（主機Suffix），* 它們是控制器提供的值。

*$（空格首碼）* 是子開發空間的名稱，它採用*SPACENAME.s*的形式。 *$（根空間首碼）* 是父空格的名稱。 例如，如果*Azureuser*是*預設*的子空間，則 *$（rootSpace首碼）* 的值*為預設值*，*並且值 $（空格首碼）* 為*azureuser.s*。 如果空格不是子空格，*則 $（空格首碼）* 為空。 例如，如果*預設*空間沒有父空間，則 *$（根空間首碼）* 的值為*預設值*，並且值 *$（空格首碼）為*空。 *$（主機Suffix）* 是指向在 AKS 群集中運行的 Azure 開發空間入口控制器的 DNS 尾碼。 此 DNS 尾碼對應于萬用字元 DNS 條目，*\*例如 。RANDOM_VALUE.eus.azds.io*）是在將 Azure 開發人員空間控制器添加到 AKS 群集時創建的。

在上面`azds.yaml`的檔中，您還可以更新*安裝.set.ingress.hosts*來更改應用程式的主機名稱。 例如，如果要將應用程式的主機名稱從 *$（空格首碼）$（根空間首碼）Webfrontend$（主機Suffix）* 簡化為 *$（空格首碼）$（rootSpace首碼）web$（主機素綴）。*

要為應用程式構建容器，控制器使用設定檔的`azds.yaml`以下部分：

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

控制器使用 Dockerfile 生成和運行應用程式。

*build.coNtext*屬性列出了 Dockerfile 存在的目錄。 *build.dockerfile*屬性定義用於生成應用程式的生產版本的 Dockerfile 的名稱。 *配置.開發.build.dockerfile*屬性為應用程式的開發版本配置 Dockerfile 的名稱。

具有不同的 Dockerfile 進行開發和生產允許您在開發期間啟用某些內容，並禁用這些項以進行生產部署。 例如，您可以在開發期間啟用調試或更詳細的日誌記錄，並在生產環境中禁用。 如果 Dockerfile 的命名方式不同或位於其他位置，也可以更新這些屬性。

為了説明您在開發過程中快速反覆運算，Azure 開發人員空間將同步本地專案的更改並逐步更新應用程式。 設定檔中的`azds.yaml`以下部分用於配置同步和更新：

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

將同步更改的檔和目錄列在*配置.d.d.s.container.sync*屬性中。 在運行命令時以及檢測到更改時，`up`最初會同步這些目錄。 如果希望將其他目錄或不同目錄同步到開發空間，則可以更改此屬性。

*配置.dd.容器.itit.buildCommands*屬性指定如何在開發方案中生成應用程式。 *配置.dd.容器.命令*屬性提供在開發方案中運行應用程式的命令。 如果要在開發期間使用其他生成或運行時標誌或參數，則可能需要更新這些屬性之一。

*配置.dd.容器.iterate.進程ToKill*列出了要終止的進程以停止應用程式。 如果要在開發期間更改應用程式的重新開機行為，則可能需要更新此屬性。 例如，如果您更新了*配置.d.d.s.容器.itite.buildCommand*或*配置.d.is.container.命令*屬性來更改應用程式的生成或啟動方式，則可能需要更改停止的進程。

使用`azds prep`命令準備代碼時，可以選擇添加`--enable-ingress`標誌。 添加該`--enable-ingress`標誌可為應用程式創建可公開訪問的 URL。 如果省略此標誌，則應用程式只能在群集中或使用本地主機隧道訪問。 運行命令`azds prep`後，可以在 中更改更改*ininin.啟用*屬性的`charts/APPNAME/values.yaml`此設置。

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>後續步驟

要瞭解有關網路以及如何在 Azure 開發人員空間中路由有關，請參閱[路由如何與 Azure 開發空間一起工作][how-it-works-routing]。

要瞭解有關使用 Azure 開發人員空間快速反覆運算和開發有關詳細資訊，請參閱[如何將開發電腦連接到開發空間工作，][how-it-works-connect]以及使用[Azure 開發人員空間遠端偵錯代碼的工作原理][how-it-works-remote-debugging]。

要開始使用 Azure 開發空間來運行專案，請參閱以下快速入門：

* [使用視覺化工作室代碼和 JAVA 快速反覆運算和調試][quickstart-java]
* [使用視覺化工作室代碼和 .NET 快速反覆運算和調試][quickstart-netcore]
* [使用視覺化工作室代碼和 Node.js 快速反覆運算和調試][quickstart-node]
* [使用 Visual Studio 和 .NET 核心快速反覆運算和調試][quickstart-vs]
* [使用 CLI 在庫伯奈斯開發應用程式][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md