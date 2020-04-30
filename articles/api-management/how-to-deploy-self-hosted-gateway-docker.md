---
title: 將自我裝載閘道部署到 Docker |Microsoft Docs
description: 瞭解如何將 Azure API 管理的自我裝載閘道元件部署至 Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205088"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>將 Azure API 管理自我裝載閘道部署至 Docker

本文提供將 Azure API 管理的自我裝載閘道元件部署到 Docker 環境的步驟。

> [!NOTE]
> 在 Docker 中裝載自我裝載閘道最適合用於評估和開發使用案例。 建議使用 Kubernetes 來進行生產環境。 請參閱[這](how-to-deploy-self-hosted-gateway-kubernetes.md)份檔，以瞭解如何將自我裝載閘道部署至 Kubernetes。

## <a name="prerequisites"></a>先決條件

- 完成下列快速入門：[建立 AZURE API 管理實例](get-started-create-service-instance.md)
- 建立 Docker 環境。 [Docker For Desktop](https://www.docker.com/products/docker-desktop)是適用于開發和評估用途的絕佳選項。 如需有關 docker 的所有版本、其功能，以及 Docker 本身的完整檔，請參閱[docker 檔](https://docs.docker.com)。
- [在您的 API 管理實例中布建閘道資源](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> 自我裝載閘道會封裝為 x86-64 以 Linux 為基礎的 Docker 容器。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>將自我裝載閘道部署到 Docker

1. 從 [**部署和基礎結構**] 底下選取 [**閘道**]。
2. 選取您想要部署的閘道資源。
3. 選取 [**部署**]。
4. 請注意，[**權杖**] 文字方塊中的存取權杖會使用預設的**到期**和**秘密金鑰**值自動產生。 如有需要，請在其中一個或兩個控制項中挑選所需的值，以產生新的權杖。
4. 請確定已在 [**部署腳本**] 底下選取 [ **Docker** ]。
5. 選取**環境**旁的 [ **env**檔案] 連結以下載檔案。
6. 選取位於 [**執行**] 文字方塊右邊的**複製**圖示，將 Docker 命令複製到剪貼簿。
7. 將命令貼到 [終端機] （或命令）視窗。 視需要調整埠對應和容器名稱。 請注意，此命令會假設下載的環境檔案存在於目前的目錄中。
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. 執行命令。 命令會指示您的 Docker 環境使用從 Microsoft Container Registry 下載的[容器映射](https://aka.ms/apim/sputnik/dhub)來執行容器，並將容器的 HTTP （8080）和 HTTPS （8081）埠對應至主機上的埠80和443。
9. 執行下列命令來檢查閘道容器是否正在執行：
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. 返回 Azure 入口網站，按一下 **[總覽**]，並確認您剛才部署的自我裝載閘道容器回報狀況良好狀態。

![閘道狀態](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> 使用<code>console docker container logs <gateway-name></code>命令來查看自我裝載閘道記錄的快照集。
>
> 使用<code>docker container logs --help</code>命令來查看所有記錄檔的查看選項。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)。
* [設定自我裝載閘道的自訂功能變數名稱](api-management-howto-configure-custom-domain-gateway.md)。
