---
title: 基底映射更新-工作
description: 瞭解應用程式容器映射的基底映射，以及基底映射更新如何觸發 Azure Container Registry 工作。
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617927"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>關於 ACR 工作的基底映射更新

本文提供應用程式基底映射更新的背景資訊，以及這些更新可以如何觸發 Azure Container Registry 工作。

## <a name="what-are-base-images"></a>什麼是基底映射？

定義大部分容器映射的 dockerfile 會指定映射所依據的父映射，通常稱為其*基底映射*。 基底映像通常包含會套用容器其餘各層的作業系統，例如 [Alpine Linux][base-alpine] 或 [Windows Nano Server][base-windows]。 此外也可能包含應用程式架構，例如 [Node.js][base-node] 或 [.NET Core][base-dotnet]。 這些基底映射本身通常是以公用上游映射為基礎。 您有數個應用程式映像可能會共用一個通用基底映像。

基底映像常會由映像維護程式進行更新，以在映像中納入作業系統或架構的新功能或增強功能。 安全性修補程式是基底映像進行更新的另一個常見原因。 當這些上游更新發生時，您也必須更新您的基底映射，以包含重大修正程式。 接著，還必須重建每個應用程式映像，以包含現在包含在基底映像中的這些上游修正。

在某些情況下，例如私用開發小組，基底映射可能會指定高於 OS 或架構。 例如，基底映射可能是需要追蹤的共用服務元件映射。 小組成員可能需要追蹤此基底映射以進行測試，或需要在開發應用程式映射時定期更新映射。

## <a name="track-base-image-updates"></a>追蹤基底映射更新

ACR 工作具有在容器的基底映像更新時自動為您建置映像的能力。

ACR 工作會在建立容器映射時動態探索基底映射相依性。 因此，它可以偵測到應用程式映射的基底映射何時更新。 使用一個預先設定的組建工作，ACR 工作可以自動重建參考基底映射的每個應用程式映射。 透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

## <a name="base-image-locations"></a>基底映射位置

針對來自 Dockerfile 的映像組建，ACR 工作會偵測下列位置中的基底映像相依性：

* 執行工作所在的相同 Azure 容器登錄
* 相同或不同區域中的另一個私人 Azure container registry 
* Docker Hub 中的公用存放庫 
* Microsoft 容器登錄中的公用存放庫

如果`FROM`語句中指定的基底映射位於這些位置的其中一個，則 ACR 工作會加入勾點，以確保在其基底更新時重建映射。

## <a name="additional-considerations"></a>其他考量

* **應用程式映射的基底映射**-目前，ACR 工作只會追蹤應用程式（*運行*時間）映射的基底映射更新。 不會針對用於多階段 Dockerfile 的中繼 (建置階段**) 映像追蹤基底映像更新。  

* **預設啟用**-當您使用[az ACR task CREATE][az-acr-task-create]命令建立 ACR 工作時，根據預設，基底映射更新會*啟用*觸發程式的工作。 也就是 `base-image-trigger-enabled` 屬性設定為 True。 如果您想要在工作中停用此行為，請將屬性更新為 False。 例如，執行以下 [az acr task update][az-acr-task-update] 命令：

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* 用**來追蹤**相依性的觸發程式-若要讓 ACR 工作判斷和追蹤容器映射的相依性（包括其基底映射），您必須先觸發工作**至少建立一次**映射。 例如，使用 [az acr task run][az-acr-task-run] 命令以手動觸發工作。

* **基底映射的穩定**標籤-若要在基底映射更新時觸發工作，基底映射必須具有*穩定*的`node:9-alpine`標記，例如。 這個標籤通常適用於隨著 OS 和架構修補程式更新到最新穩定版本的基底映像。 如果基底映像是隨著新版本標籤更新，它就不會觸發工作。 如需有關映像標籤的詳細資訊，請參閱[最佳做法指引](container-registry-image-tag-version.md)。 

* **其他工作觸發**程式-在基底映射更新所觸發的工作中，您也可以根據[原始碼認可](container-registry-tutorial-build-task.md)或[排程](container-registry-tasks-scheduled.md)來啟用觸發程式。 基底映射更新也可以觸發[多步驟](container-registry-tasks-multi-step.md)工作。

## <a name="next-steps"></a>後續步驟

請參閱下列教學課程，以瞭解在更新基底映射之後自動執行應用程式映射組建的案例：

* [當相同登錄中的基底映射更新時，自動執行容器映射組建](container-registry-tutorial-base-image-update.md)

* [當基底映射在不同的登錄中更新時，自動執行容器映射組建](container-registry-tutorial-base-image-update.md)


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
