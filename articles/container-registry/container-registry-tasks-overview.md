---
title: ACR 工作概觀
description: ACR 工作的簡介，這是 Azure Container Registry 中的一組功能，可在雲端提供安全、自動化的容器映射組建、管理及修補。
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: b6df415bd55979ef00f6921321dbc254ef7a7e59
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562849"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>使用 ACR 工作自動化容器映射組建和維護

容器提供新的虛擬化層級，並且隔離應用程式和開發人員相依性與基礎結構和作業需求。 不過，仍然需要解決此應用程式虛擬化如何透過容器生命週期進行管理和修補。

## <a name="what-is-acr-tasks"></a>什麼是 ACR 工作？

「ACR 工作」是 Azure Container Registry 內的一套功能。 它提供以雲端為基礎的容器映射，適用于 [平臺](#image-platforms) （包括 Linux、WINDOWS 和 ARM），並可將 Docker 容器的 [作業系統和架構修補](#automate-os-and-framework-patching) 自動化。 ACR 工作不僅會使用隨需容器映射組建將您的「內部迴圈」開發週期延伸至雲端，還可讓您透過原始程式碼更新、容器的基底映射更新或計時器來觸發自動化的組建。 例如，使用基底映射更新觸發程式，您可以將作業系統和應用程式架構修補工作流程自動化，以維護安全的環境，同時遵守不可變容器的原則。

## <a name="task-scenarios"></a>工作案例

ACR 工作支援數個案例來建立和維護容器映射和其他成品。 請參閱本文中的下列各節，以取得詳細資料。

* **[快速](#quick-task)** 工作-在 Azure 中視需要建立單一容器映射並將其推送至容器登錄，而不需要本機 Docker 引擎安裝。 請思考一下雲端中的 `docker build`、`docker push`。
* **自動觸發** 的工作-啟用一或多個 *觸發* 程式以建立映射：
  * **[來源程式碼更新時觸發](#trigger-task-on-source-code-update)** 
  * **[基底映射更新時觸發](#automate-os-and-framework-patching)** 
  * **[依排程觸發](#schedule-a-task)** 
* **[多步驟](#multi-step-tasks)** 工作-使用多步驟、多容器式工作流程，擴充 ACR 工作的單一映射組建和推送功能。 

每個 ACR 工作都有相關聯的 [原始程式碼內容](#context-locations) ，也就是一組用來建立容器映射或其他成品的原始程式檔位置。 範例內容包含 Git 存放庫或本機檔案系統。

工作也可以利用 [執行變數](container-registry-tasks-reference-yaml.md#run-variables)，讓您可以重複使用工作定義，並將影像和成品的標記標準化。

## <a name="quick-task"></a>快速工作

內部迴圈開發週期 (在認可至原始檔控制前的撰寫程式碼、建置及測試應用程式的反覆程序) 是容器生命週期管理的開端。

「ACR 工作」的[快速工作](container-registry-tutorial-quick-task.md)功能可在您認可第一行程式碼之前，藉由將您的容器映像建置卸交給 Azure，提供一個整合式開發體驗。 使用快速工作時，您可以在認可程式碼之前，先確認您的自動化建置定義並攔截可能的問題。

Azure CLI 中的 [az acr build][az-acr-build]命令會使用熟悉的 `docker build` 格式來取得「內容」[](#context-locations)(要建置的一組檔案)、將它傳送給「ACR 工作」，然後依預設在完成時將所建置的映像推送至其登錄。

如需簡介，請參閱在 Azure Container Registry 中 [建立和執行容器映射](container-registry-quickstart-task-cli.md) 的快速入門。  

「ACR 工作」已設計為容器生命週期原始物件。 例如，您可以將「ACR 工作」整合到 CI/CD 解決方案中。 透過[服務主體][az-login-service-principal]執行 [az login][az-login]，您的 CI/CD 解決方案可接著發出 [az acr build][az-acr-build] 命令來開始進行映像建置。

若要了解如何使用快速工作，請參閱第一個「ACR 工作」教學課程：[使用 Azure Container Registry 工作在雲端中建置容器映像](container-registry-tutorial-quick-task.md)。

> [!TIP]
> 如果您想要直接從原始程式碼建立和推送映射（沒有 Dockerfile），Azure Container Registry 提供 [az acr pack build][az-acr-pack-build] 命令 (preview) 。 此工具會使用 [雲端原生 Buildpacks](https://buildpacks.io/)，從應用程式原始程式碼建立並推送映射。

## <a name="trigger-task-on-source-code-update"></a>來源程式碼更新時觸發程式工作

在 GitHub 或 Azure DevOps 的公用或私用 Git 存放庫中，在認可程式碼或提取要求或更新提取要求時觸發容器映射組建或多步驟工作。 例如，藉由指定 Git 存放庫和（選擇性）分支和 Dockerfile，以 Azure CLI 命令 [az acr task create][az-acr-task-create] 來設定組建工作。 當您的小組更新存放庫中的程式碼時，ACR 工作建立的 webhook 會觸發存放庫中所定義之容器映射的組建。 

當您將 Git 存放庫設定為工作內容時，ACR 工作支援下列觸發程式：

| 觸發程序 | 預設已啟用 |
| ------- | ------------------ |
| Commit | 是 |
| 提取要求 | 否 |

若要設定原始程式碼更新觸發程式，您必須提供工作 (PAT) 的個人存取權杖，以在公用或私人 GitHub 或 Azure DevOps 存放庫中設定 webhook。

> [!NOTE]
> 目前，ACR 工作不支援 GitHub Enterprise 存放庫中的認可或提取要求觸發程序。

若要了解如何在認可原始程式碼時觸發建置，請參閱第二個「ACR 工作」教學課程：[使用 Azure Container Registry 工作自動執行容器映像建置](container-registry-tutorial-build-task.md)。

## <a name="automate-os-and-framework-patching"></a>自動進行作業系統和架構修補

真正增強容器組建工作流程的 ACR 工作功能，來自于偵測 *基底映射* 更新的能力。 大部分容器映射的功能，基底映射是一個或多個應用程式映射所依據的父映射。 基底映射通常包含作業系統，有時也包含應用程式架構。 

您可以設定 ACR 工作，以在建立應用程式映射時追蹤基底映射的相依性。 當更新的基底映射推送至您的登錄，或在公用存放庫中更新基底映射（例如 Docker Hub）時，ACR 工作可以根據它自動建立任何應用程式映射。
透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

深入瞭解 ACR 工作的 [基底映射更新觸發](container-registry-tasks-base-images.md) 程式。 並瞭解如何在[Azure container registry 中的基底映射更新時](container-registry-tutorial-base-image-update.md)，將基底映射推送至容器登錄時觸發映射組建

## <a name="schedule-a-task"></a>排程工作

當您建立或更新工作時，可以設定一或多個 *計時器觸發* 程式，以選擇性地排程工作。 排程工作適用于依照定義的排程來執行容器工作負載，或在定期推送至登錄的映射上執行維護作業或測試。 如需詳細資訊，請參閱依 [定義的排程執行 ACR](container-registry-tasks-scheduled.md)工作。

## <a name="multi-step-tasks"></a>多步驟工作

多步驟工作提供適用於在雲端建置、測試及修補容器映像的步驟型工作定義與執行。 [YAML](container-registry-tasks-reference-yaml.md)檔中定義的工作步驟會指定容器映射或其他構件的個別組建和推送作業。 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

例如，您可以建立一個自動執行下列操作的多步驟工作：

1. 建置 Web 應用程式映像
1. 執行 Web 應用程式容器
1. 建置 Web 應用程式測試映像
1. 執行 web 應用程式測試容器，它會對執行中的應用程式容器執行測試
1. 如果測試通過，便建置 Helm 圖表封存套件
1. 使用新的 Helm 圖表封存套件來執行 `helm upgrade`

多步驟工作可讓您將映像的建置、執行及測試，分割成更多可組合且具有步驟間相依性支援的步驟。 藉由「ACR 工作」中的多步驟工作，您可以更細微地控制映像建置、測試及 OS 和架構修補工作流程。

如需了解多步驟工作，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

## <a name="context-locations"></a>內容位置

下表顯示 ACR 工作支援的內容位置範例：

| 內容位置 | 說明 | 範例 |
| ---------------- | ----------- | ------- |
| 本機檔案系統 | 本機檔案系統上目錄內的檔案。 | `/home/user/projects/myapp` |
| GitHub 主要分支 | 公用或私人 GitHub 存放庫的主要 (或其他預設) 分支內的檔案。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | 公用或私人 GitHub 存放庫的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子資料夾 | 公用或私人 GitHub 存放庫中的子資料夾內的檔案。 範例顯示分支與子資料夾規格的組合。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub 認可 | 公用或私人 GitHub 存放庫中的特定認可。 範例顯示 commit hash (SHA-1) 和子資料夾規格的組合。 | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps 子資料夾 | 公用或私人 Azure 存放庫中的子資料夾內的檔案。 範例顯示分支與子資料夾規格的組合。 | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| 遠端 Tarball | 遠端 Web 伺服器上壓縮封存中的檔案。 | `http://remoteserver/myapp.tar.gz` |
| Container registry 中的成品 | [OCI](container-registry-oci-artifacts.md) container registry 存放庫中的成品檔案。 | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> 使用私用 Git 存放庫做為工作的內容時，您需要提供個人存取權杖 (PAT) 。

## <a name="image-platforms"></a>映射平臺

根據預設，ACR 工作會建立 Linux OS 和 amd64 架構的映射。 指定 `--platform` 標記以建立其他架構的 Windows 映像或 Linux 映射。 以作業系統/架構格式指定作業系統和（選擇性）支援的架構 (例如 `--platform Linux/arm`) 。 針對 ARM 架構，您可以選擇性地指定作業系統/架構/變異格式的變異 (例如 `--platform Linux/arm64/v8`) ：

| OS | 架構|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>檢視工作輸出

每個工作執行都會產生記錄輸出，您可以檢查這些輸出以判斷工作步驟是否成功執行。 當您手動觸發工作時，工作執行的記錄輸出會串流處理到主控台，也會儲存以供稍後抓取。 當自動觸發工作（例如，由原始程式碼認可或基底映射更新）時，只會儲存工作記錄。 查看 Azure 入口網站中的執行記錄，或使用 [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) 命令。

深入瞭解如何 [查看和管理工作記錄](container-registry-tasks-logs.md)。

## <a name="next-steps"></a>後續步驟

當您準備好在雲端中自動化容器映射組建和維護時，請參閱 [ACR 工作的教學課程系列](container-registry-tutorial-quick-task.md)。

您可以選擇性地安裝[適用於 Visual Studio Code 的 Docker 擴充功能](https://code.visualstudio.com/docs/azure/docker)和 [Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)擴充功能，來搭配 Azure 容器登錄使用。 向 Azure 容器登錄提取及推送映像，或是執行 ACR 工作，都可以在 Visual Studio Code 內完成。

<!-- LINKS - External -->
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
