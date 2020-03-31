---
title: ACR 工作概觀
description: ACR 任務簡介，Azure 容器註冊表中的一組功能，在雲中提供安全、自動化的容器映射生成、管理和修補。
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087287"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>使用 ACR 任務自動執行容器映射生成和維護

容器提供新的虛擬化層級，並且隔離應用程式和開發人員相依性與基礎結構和作業需求。 但是，仍然存在的是需要解決如何在容器生命週期中管理和修補此應用程式虛擬化。

## <a name="what-is-acr-tasks"></a>什麼是 ACR 工作？

「ACR 工作」**** 是 Azure Container Registry 內的一套功能。 它為 Linux、Windows 和 ARM 等[平臺](#image-platforms)提供基於雲的容器映射構建，並可自動為 Docker 容器[進行作業系統和框架修補](#automate-os-and-framework-patching)。 ACR 任務不僅通過按需容器映射生成將"內部迴圈"開發週期擴展到雲，還支援由原始程式碼更新、容器基本映射更新或計時器觸發的自動生成。 例如，使用基本映射更新觸發器，您可以自動執行作業系統和應用程式框架修補工作流，在遵守不可變容器原則的同時維護安全環境。

## <a name="task-scenarios"></a>任務方案

ACR 任務支援多個方案來生成和維護容器映射和其他專案。 有關詳細資訊，請參閱本文中的以下部分。

* **[快速任務](#quick-task)**- 在 Azure 中按需生成單個容器映射並將其推送到容器註冊表，而無需安裝本地 Docker 引擎。 請思考一下雲端中的 `docker build`、`docker push`。
* **自動觸發的任務**- 啟用一個或多個*觸發器*來生成映射：
  * **[在原始程式碼更新時觸發](#trigger-task-on-source-code-update)** 
  * **[基於基本映射更新觸發](#automate-os-and-framework-patching)** 
  * **[按計劃觸發](#schedule-a-task)** 
* **[多步驟任務](#multi-step-tasks)**- 使用多步、多容器的工作流擴展 ACR 任務的單映射構建和推送功能。 

每個 ACR 任務都有一個關聯的[原始程式碼上下文](#context-locations)- 用於生成容器映射或其他工件的一組原始檔案的位置。 示例上下文包括 Git 存儲庫或本地檔案系統。

任務還可以利用[運行變數](container-registry-tasks-reference-yaml.md#run-variables)，因此您可以重用任務定義並標準化圖像和工件的標記。

## <a name="quick-task"></a>快速工作

內部迴圈開發週期 (在認可至原始檔控制前的撰寫程式碼、建置及測試應用程式的反覆程序) 是容器生命週期管理的開端。

「ACR 工作」的[快速工作](container-registry-tutorial-quick-task.md)功能可在您認可第一行程式碼之前，藉由將您的容器映像建置卸交給 Azure，提供一個整合式開發體驗。 使用快速工作時，您可以在認可程式碼之前，先確認您的自動化建置定義並攔截可能的問題。

Azure CLI 中的 [az acr build][az-acr-build]命令會使用熟悉的 `docker build` 格式來取得「內容」[](#context-locations)(要建置的一組檔案)、將它傳送給「ACR 工作」，然後依預設在完成時將所建置的映像推送至其登錄。

有關簡介，請參閱在 Azure 容器註冊表中[生成和運行容器映射](container-registry-quickstart-task-cli.md)的快速入門。  

「ACR 工作」已設計為容器生命週期原始物件。 例如，您可以將「ACR 工作」整合到 CI/CD 解決方案中。 透過[服務主體][az-login-service-principal]執行 [az login][az-login]，您的 CI/CD 解決方案可接著發出 [az acr build][az-acr-build] 命令來開始進行映像建置。

若要了解如何使用快速工作，請參閱第一個「ACR 工作」教學課程：[使用 Azure Container Registry 工作在雲端中建置容器映像](container-registry-tutorial-quick-task.md)。

> [!TIP]
> 如果要直接從原始程式碼生成和推送映射，而不使用 Dockerfile，Azure 容器註冊表提供[az acr 包生成][az-acr-pack-build]命令（預覽）。 此工具使用[雲本機構建包](https://buildpacks.io/)從應用程式原始程式碼生成和推送映射。

## <a name="trigger-task-on-source-code-update"></a>在原始程式碼更新時觸發任務

當向 GitHub 或 Azure DevOps 中的公共或私有 Git 存儲庫提交或發出拉取請求時，觸發容器映射生成或多步驟任務。 例如，通過指定 Git 存儲庫和可選地指定分支和 Dockerfile 來配置使用 Azure CLI 命令[az acr 任務創建的生成任務][az-acr-task-create]。 當團隊更新存儲庫中的代碼時，ACR 任務創建的 Webhook 將觸發存儲庫中定義的容器映射的生成。 

當您將 Git 存儲庫設置為任務的上下文時，ACR 任務支援以下觸發器：

| 觸發程序 | 預設已啟用 |
| ------- | ------------------ |
| Commit | 是 |
| 提取要求 | 否 |

要配置原始程式碼更新觸發器，您需要為任務提供個人訪問權杖 （PAT）， 以在公共或私有 GitHub 或 Azure DevOps 存儲庫中設置 Webhook。

> [!NOTE]
> 目前，ACR 工作不支援 GitHub Enterprise 存放庫中的認可或提取要求觸發程序。

若要了解如何在認可原始程式碼時觸發建置，請參閱第二個「ACR 工作」教學課程：[使用 Azure Container Registry 工作自動執行容器映像建置](container-registry-tutorial-build-task.md)。

## <a name="automate-os-and-framework-patching"></a>自動進行作業系統和架構修補

ACR 任務真正增強容器生成工作流的強大功能來自于它檢測*基本映射*的更新的能力。 大多數容器映射的一個功能，基本映射是一個或多個應用程式映射基於的父映射。 基本映射通常包含作業系統，有時包括應用程式框架。 

您可以設置 ACR 任務，以在生成應用程式映射時跟蹤對基本映射的依賴項。 將更新的基本映射推送到註冊表，或在諸如 Docker Hub 中等公共回購中更新基本映射時，ACR 任務可以基於它自動生成任何應用程式映射。
透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

瞭解有關 ACR 任務[的基本映射更新觸發器](container-registry-tasks-base-images.md)的詳細資訊。 瞭解如何在教程中將基本映射推送到容器註冊表時觸發映射[生成，在 Azure 容器註冊表中更新基本映射時自動生成容器映射](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>排程工作

在創建或更新任務時，可以選擇設置一個或多個*計時器觸發器來*安排任務。 計畫任務對於按計劃運行容器工作負載，或在定期推送到註冊表的映射上運行維護操作或測試非常有用。 有關詳細資訊，請參閱[在定義的計畫上運行 ACR 任務](container-registry-tasks-scheduled.md)。

## <a name="multi-step-tasks"></a>多步驟工作

多步驟工作提供適用於在雲端建置、測試及修補容器映像的步驟型工作定義與執行。 在[YAML 檔中](container-registry-tasks-reference-yaml.md)定義的任務步驟指定容器映射或其他工件的單個生成和推送操作。 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

例如，您可以建立一個自動執行下列操作的多步驟工作：

1. 建置 Web 應用程式映像
1. 執行 Web 應用程式容器
1. 建置 Web 應用程式測試映像
1. 運行 Web 應用程式測試容器，該容器對正在運行的應用程式容器執行測試
1. 如果測試通過，便建置 Helm 圖表封存套件
1. 使用新的 Helm 圖表封存套件來執行 `helm upgrade`

多步驟工作可讓您將映像的建置、執行及測試，分割成更多可組合且具有步驟間相依性支援的步驟。 藉由「ACR 工作」中的多步驟工作，您可以更細微地控制映像建置、測試及 OS 和架構修補工作流程。

如需了解多步驟工作，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

## <a name="context-locations"></a>上下文位置

下表顯示「ACR 工作」的幾個所支援內容位置範例：

| 內容位置 | 描述 | 範例 |
| ---------------- | ----------- | ------- |
| 本機檔案系統 | 本機檔案系統上目錄內的檔案。 | `/home/user/projects/myapp` |
| GitHub 主要分支 | 公共或私有 GitHub 存儲庫的主分支（或其他預設）分支中的檔。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | 公共或私有 GitHub 存儲庫的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子資料夾 | 公共或私有 GitHub 存儲庫中的子資料夾中的檔。 示例顯示分支和子資料夾規範的組合。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub 提交 | 公共或私有 GitHub 存儲庫中的特定提交。 示例顯示提交雜湊 （SHA） 和子資料夾規範的組合。 | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps 子資料夾 | 公共或專用 Azure 回購中的子資料夾中的檔。 示例顯示分支和子資料夾規範的組合。 | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| 遠端 Tarball | 遠端 Web 伺服器上壓縮封存中的檔案。 | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> 使用私有 Git 存儲庫作為任務的上下文時，需要提供個人訪問權杖 （PAT）。

## <a name="image-platforms"></a>圖像平臺

預設情況下，ACR 任務為 Linux 作業系統和 amd64 體系結構生成映射。 指定用於`--platform`為其他體系結構生成 Windows 映像或 Linux 映射的標記。 以作業系統/體系結構格式（例如，）`--platform Linux/arm`指定作業系統，並選擇支援體系結構。 對於 ARM 體系結構，可以選擇以作業系統/體系結構/變體格式指定變體（例如， `--platform Linux/arm64/v8`

| OS | 架構|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>臂64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>檢視工作輸出

每個任務運行都會生成日誌輸出，您可以檢查這些日誌輸出以確定任務步驟是否成功運行。 手動觸發任務時，任務運行的日誌輸出將資料流到主控台，並存儲以供以後檢索。 當任務自動觸發時（例如由原始程式碼提交或基本映射更新），僅存儲任務日誌。 在 Azure 門戶中查看運行日誌，或使用[az acr 任務日誌](/cli/azure/acr/task#az-acr-task-logs)命令。

查看[和管理工作日誌](container-registry-tasks-logs.md)。

## <a name="next-steps"></a>後續步驟

當您準備好在雲中自動執行容器映射生成和維護時，請查看[ACR 任務教程系列](container-registry-tutorial-quick-task.md)。

您可以選擇性地安裝[適用於 Visual Studio Code 的 Docker 擴充功能](https://code.visualstudio.com/docs/azure/docker)和 [Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)擴充功能，來搭配 Azure 容器登錄使用。 向 Azure 容器登錄提取及推送映像，或是執行 ACR 工作，都可以在 Visual Studio Code 內完成。

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
