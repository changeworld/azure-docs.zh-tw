---
title: 包含檔案
description: 包含檔案
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 16c1dec9e52be98e5b4e0103a583bdc219bc2417
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88031767"
---
## <a name="push-image-to-registry"></a>將映像推送至登錄

若要推送映像到 Azure Container Registry，您必須先有映像。 如果您還沒有任何本機容器映像，請執行下列 [docker pull][docker-pull] 命令，從 Docker 中樞提取現有的映像。 在此範例中，請提取 `hello-world` 映像。

```
docker pull hello-world
```

您必須使用登錄登入伺服器的完整名稱來標記映像，才能將映像推送至您的登錄。 登入伺服器名稱的格式為 *\<registry-name\>.azurecr.io* (全部小寫)，例如 *mycontainerregistry.azurecr.io* 。

使用 [docker tag][docker-tag] 命令來標記映像。 將 `<login-server>` 取代為 ACR 執行個體的登入伺服器名稱。

```
docker tag hello-world <login-server>/hello-world:v1
```

範例：

```
docker tag hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


最後，使用 [docker push][docker-push] 將映像推送到登錄執行個體。 將 `<login-server>` 取代為登錄執行個體的登入伺服器名稱。 此範例會建立 **hello-world** 存放庫，其中包含 `hello-world:v1` 映像。

```
docker push <login-server>/hello-world:v1
```

將映像推送至您的容器登錄之後，請從您的本機 Docker 環境中移除 `hello-world:v1` 映像。 (請注意，此 [docker rmi][docker-rmi] 命令並不會從 Azure 容器登錄中的 **hello-world** 存放區移除映像。)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

