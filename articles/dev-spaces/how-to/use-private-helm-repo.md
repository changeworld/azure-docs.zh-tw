---
title: 如何在 Azure 開發人員空間中使用專用 Helm 存儲庫
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: 在 Azure 開發人員空間中使用專用 Helm 存儲庫。
keywords: 碼頭機、庫伯奈斯、Azure、AKS、Azure 容器服務、容器、頭盔
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240461"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>在 Azure 開發人員空間中使用專用 Helm 存儲庫

[赫爾姆][helm]是庫伯內斯的包經理。 Helm 使用[圖表][helm-chart]格式來打包依賴項。 Helm 圖表存儲在存儲庫中，存儲庫可以是公共的，也可以是私有的。 Azure 開發人員空間僅在運行應用程式時從公共存儲庫檢索 Helm 圖表。 如果 Helm 存儲庫是私有的或 Azure 開發人員空間無法訪問它，則可以將該存儲庫中的圖表直接添加到應用程式。 直接添加圖表可使 Azure 開發人員空間運行應用程式，而無需訪問專用 Helm 存儲庫。

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>將專用 Helm 存儲庫添加到本地電腦

使用[helm 存儲庫添加][helm-repo-add]和[掌舵存儲庫更新][helm-repo-update]從本地電腦訪問專用 Helm 存儲庫。

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>將圖表添加到應用程式

導航到專案的目錄並運行`azds prep`。

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep` 命令會嘗試為您的專案產生 [Dockerfile 和 Helm 圖表](../how-dev-spaces-works-prep.md#prepare-your-code)。 Azure Dev Spaces 會使用這些檔案來建置和執行您的程式碼，但如果您想要變更專案的建置和執行方式，可以修改這些檔案。

在應用程式的圖表目錄中使用圖表創建[要求.yaml][helm-requirements]檔。 例如，如果應用程式名為*app1*，則創建*圖表/app1/要求。*

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

導航到應用程式的圖表目錄，並使用[舵依賴項更新][helm-dependency-update]更新應用程式的 Helm 依賴項，並從專用存儲庫下載圖表。

```cmd
helm dependency update
```

驗證具有*tgz*檔的*圖表*子目錄已添加到應用程式的圖表目錄中。 例如，*圖表/app1/圖表/mychart-0.1.0.tgz*。

來自私有 Helm 存儲庫的圖表已下載並添加到您的專案中。 刪除*要求.yaml*檔，以便開發人員空間不會嘗試更新此依賴項。

## <a name="run-your-application"></a>執行您的應用程式

導航到專案的根目錄並運行`azds up`以驗證應用程式在開發空間中成功運行。

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>後續步驟

瞭解更多關於[赫爾姆及其工作原理][helm]。

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
