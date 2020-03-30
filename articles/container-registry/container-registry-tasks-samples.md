---
title: ACR 任務示例
description: 示例 Azure 容器註冊表任務（ACR 任務），用於生成、運行和修補容器映射
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456094"
---
# <a name="acr-tasks-samples"></a>ACR 任務示例

本文連結到多個 Azure `task.yaml` [容器註冊表任務](container-registry-tasks-overview.md)（ACR 任務） 方案的示例檔和關聯的 Dockerfile。 

有關其他示例，請參閱[Azure 示例][task-examples]回購。

## <a name="scenarios"></a>案例

* **生成映射** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **運行容器** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **構建和推送圖像** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **生成和運行映射** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **構建和推送多個圖像** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **在並行** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml)中生成和測試映射[，Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **構建映射並將其推送到多個註冊表** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml)， [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>後續步驟

瞭解有關 ACR 任務的更多詳細資訊：

* [多步驟任務](container-registry-tasks-multi-step.md)- 基於 ACR 任務的工作流，用於在雲中構建、測試和修補容器映射。
* [工作參考](container-registry-tasks-reference-yaml.md) - 工作步驟類型、其屬性及使用方式。
* [Cmd 回購](https://github.com/AzureCR/cmd)- 作為 ACR 任務命令的容器集合。


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
