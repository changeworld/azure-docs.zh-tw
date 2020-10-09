---
title: ACR 工作範例
description: 範例 Azure Container Registry 工作 (ACR 工作) 建立、執行和修補容器映射
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74456094"
---
# <a name="acr-tasks-samples"></a>ACR 工作範例

本文將連結至 `task.yaml` 數個 [Azure Container Registry 工作](container-registry-tasks-overview.md) (ACR 工作) 案例的範例檔案和相關聯的 dockerfile。 

如需其他範例，請參閱 [Azure 範例][task-examples] 存放庫。

## <a name="scenarios"></a>案例

* **組建映射**  - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml)、 [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **執行容器**  - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **組建和推送映射**  - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml)、 [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **建立並執行映射**  - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml)、 [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **建立和推送多個映射**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml)、 [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* 平行組建和**測試映射**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml)、 [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **建立映射並將其推送至多個**  -  登錄[YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml)、 [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>後續步驟

深入瞭解 ACR 工作：

* [多步驟](container-registry-tasks-multi-step.md) 工作-以 ACR 工作為基礎的工作流程，可在雲端中建立、測試及修補容器映射。
* [工作參考](container-registry-tasks-reference-yaml.md) - 工作步驟類型、其屬性及使用方式。
* [Cmd](https://github.com/AzureCR/cmd) 存放庫-容器的集合，做為 ACR 工作的命令。


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
