---
title: 基本映射更新 - 任務
description: 瞭解應用程式容器映射的基本映射，以及基本映射更新如何觸發 Azure 容器註冊表任務。
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617927"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>關於 ACR 任務的基本映射更新

本文提供有關應用程式基本映射的更新以及這些更新如何觸發 Azure 容器註冊表任務的背景資訊。

## <a name="what-are-base-images"></a>什麼是基本圖像？

定義大多數容器圖像的 Dockerfile 指定圖像所基於的父映射，通常稱為其*基本映射*。 基底映像通常包含會套用容器其餘各層的作業系統，例如 [Alpine Linux][base-alpine] 或 [Windows Nano Server][base-windows]。 此外也可能包含應用程式架構，例如 [Node.js][base-node] 或 [.NET Core][base-dotnet]。 這些基本映射本身通常基於公共上游圖像。 您有數個應用程式映像可能會共用一個通用基底映像。

基底映像常會由映像維護程式進行更新，以在映像中納入作業系統或架構的新功能或增強功能。 安全性修補程式是基底映像進行更新的另一個常見原因。 當發生這些上游更新時，還必須更新基本映射以包括關鍵修復。 接著，還必須重建每個應用程式映像，以包含現在包含在基底映像中的這些上游修正。

在某些情況下，例如私有開發團隊，基本映射可能指定的不僅僅是作業系統或框架。 例如，基本映射可以是需要跟蹤的共用服務元件映射。 團隊成員可能需要跟蹤此基本映射以進行測試，或者在開發應用程式映射時需要定期更新映射。

## <a name="track-base-image-updates"></a>跟蹤基本映射更新

ACR 工作具有在容器的基底映像更新時自動為您建置映像的能力。

ACR 任務在生成容器映射時動態地發現基本映射依賴關係。 因此，它可以檢測何時更新應用程式映射的基本映射。 使用一個預配置的生成任務，ACR 任務可以自動重建引用基本映射的每個應用程式映射。 透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

## <a name="base-image-locations"></a>基本圖像位置

針對來自 Dockerfile 的映像組建，ACR 工作會偵測下列位置中的基底映像相依性：

* 執行工作所在的相同 Azure 容器登錄
* 同一區域或不同區域中的另一個專用 Azure 容器註冊表 
* Docker Hub 中的公用存放庫 
* Microsoft 容器登錄中的公用存放庫

如果`FROM`語句中指定的基本映射位於這些位置之一，ACR 任務將添加一個掛鉤，以確保在更新映射基時重建映射。

## <a name="additional-considerations"></a>其他考量

* **應用程式映射的基本映射**- 目前，ACR 任務僅跟蹤應用程式（*運行時*）映射的基本映射更新。 不會針對用於多階段 Dockerfile 的中繼 (建置階段**) 映像追蹤基底映像更新。  

* **預設情況下啟用**- 當您使用[az acr 任務創建][az-acr-task-create]命令創建 ACR 任務時，預設情況下，該任務通過基本映射更新*啟用*以觸發。 也就是 `base-image-trigger-enabled` 屬性設定為 True。 如果您想要在工作中停用此行為，請將屬性更新為 False。 例如，執行以下 [az acr task update][az-acr-task-update] 命令：

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **觸發器以跟蹤依賴項**- 要使 ACR 任務確定和跟蹤容器映射的依賴項（包括其基本映射），必須首先觸發該任務以**至少生成映射一次**。 例如，使用 [az acr task run][az-acr-task-run] 命令以手動觸發工作。

* **基本映射的穩定標記**- 要在基本映射更新時觸發任務，基本映射必須具有*穩定*標記，如`node:9-alpine`。 這個標籤通常適用於隨著 OS 和架構修補程式更新到最新穩定版本的基底映像。 如果基底映像是隨著新版本標籤更新，它就不會觸發工作。 如需有關映像標籤的詳細資訊，請參閱[最佳做法指引](container-registry-image-tag-version.md)。 

* **其他任務觸發器**- 在由基本映射更新觸發的任務中，還可以基於[原始程式碼提交](container-registry-tutorial-build-task.md)或[計畫](container-registry-tasks-scheduled.md)啟用觸發器。 基本映射更新還可以觸發[多步驟任務](container-registry-tasks-multi-step.md)。

## <a name="next-steps"></a>後續步驟

有關在更新基本映射後自動執行應用程式映射生成的方案，請參閱以下教程：

* [在同一註冊表中更新基本映射時自動生成容器映射](container-registry-tutorial-base-image-update.md)

* [當其他註冊表中更新基本映射時，自動生成容器映射](container-registry-tutorial-base-image-update.md)


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
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
