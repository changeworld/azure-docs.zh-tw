---
title: 將自託管的 Azure API 管理閘道部署到 Docker |微軟文檔
description: 瞭解如何將自託管的 Azure API 管理閘道部署到 Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768498"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>將 Azure API 管理自承載閘道部署到 Docker

本文提供了將自託管 Azure API 管理閘道部署到 Docker 環境的步驟。

> [!NOTE]
> 自託管閘道功能處於預覽狀態。 在預覽期間，自託管閘道僅在開發人員和高級層中可用，無需支付額外費用。 開發人員層僅限於單個自託管閘道部署。

## <a name="prerequisites"></a>Prerequisites

- 完成以下快速入門：[創建 Azure API 管理實例](get-started-create-service-instance.md)
- 創建 Docker 環境。 對於開發和評估目的[，桌面 Docker](https://www.docker.com/products/docker-desktop)是一個不錯的選擇。 有關所有 Docker 版本、其功能以及有關 Docker 本身的全面文檔的資訊，請參閱[Docker 文檔](https://docs.docker.com)。
- [在 API 管理實例中預配閘道資源](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> 自託管閘道打包為基於 x86-64 的基於 Linux 的 Docker 容器。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>將自託管閘道部署到 Docker

1. 從 **"設置"** 下選擇**閘道**。
2. 選擇要部署的閘道資源。
3. 選擇**部署**。
4. 請注意，使用預設**的"過期"** 和"**金鑰**"值自動生成**權杖**文字方塊中的新權杖。 根據需要調整任一或兩者，然後選擇 **"生成"** 以創建新權杖。
4. 確保**Docker**在 **"部署"腳本**下被選中。
5. 選擇**環境**旁邊的**env.conf**檔連結以下載該檔。
6. 選擇位於 **"運行**文本"框右端**的副本**圖示，將 Docker 命令保存到剪貼簿。
7. 將命令粘貼到終端（或命令）視窗。 根據需要調整埠映射和容器名稱。 請注意，該命令希望下載的環境檔存在於目前的目錄中。
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. 執行命令。 該命令指示 Docker 環境使用從 Microsoft 容器註冊表下載的自託管閘道映射運行容器，並將容器的 HTTP （8080） 和 HTTPS （8081） 埠映射到主機上的埠 80 和 443。
9. 運行以下命令以檢查閘道窗格是否正在運行：
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. 返回 Azure 門戶並確認剛剛部署的閘道節點報告正常狀態。

![閘道狀態](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> 使用<code>console docker container logs <gateway-name></code>命令查看自承載閘道日誌的快照。
>
> 使用<code>docker container logs --help</code>命令查看所有日誌查看選項。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關自託管閘道的詳細資訊，請參閱 Azure [API 管理自託管閘道概述](self-hosted-gateway-overview.md)。
* [為自託管閘道配置自訂功能變數名稱](api-management-howto-configure-custom-domain-gateway.md)。
