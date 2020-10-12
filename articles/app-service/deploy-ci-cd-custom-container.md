---
title: CI/CD 至自訂 Linux 容器
description: 瞭解如何在 Azure App Service 中設定持續部署至自訂 Linux 容器。 Docker Hub 和 ACR 都支援持續部署。
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b73c7e62c6b621d3ac86cfb99b10b567460534f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961851"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>使用用於容器的 Web 應用程式進行持續部署

在本教學課程中，您可以從受控 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 存放庫或 [Docker Hub](https://hub.docker.com) 設定自訂容器映像的持續部署。

## <a name="enable-continuous-deployment-with-acr"></a>透過 ACR 啟用持續部署

![ACR Webhook 的螢幕擷取畫面](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頁面左側的 [App Service] **** 選項。
3. 選取您要設定持續部署的應用程式名稱。
4. 在 [容器設定]**** 頁面上，選取 [單一容器]****
5. 選取 [Azure Container Registry]****
6. 選取 [持續部署] > [開啟]****
7. 選取 [儲存]**** 以啟用持續部署。

## <a name="use-the-acr-webhook"></a>使用 ACR Webhook

啟用持續部署之後，您可以在 Azure Container Registry Webhook 頁面上檢視新建立的 Webhook。

![ACR Webhook 的螢幕擷取畫面](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

在 Container Registry 中，按一下 Webhook 以檢視目前的 Webhook。

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>透過 Docker Hub 啟用持續部署 (選用)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頁面左側的 [App Service] **** 選項。
3. 選取您要設定持續部署的應用程式名稱。
4. 在 [容器設定]**** 頁面上，選取 [單一容器]****
5. 選取 [Docker Hub]****
6. 選取 [持續部署] > [開啟]****
7. 選取 [儲存]**** 以啟用持續部署。

![應用程式設定的螢幕擷取畫面](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

複製 Webhook URL。 若要新增 Docker Hub 的 Webhook，請遵循 <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker Hub 的 Webhook</a>。

## <a name="automate-with-cli"></a>使用 CLI 自動化

若要使用 Azure CLI 設定 CI/CD，請執行 [az webapp deployment container config](/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) 命令以產生 webhook URL。 URL 可以用來設定您的 DockerHub 或 Azure Container Registry。

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>後續步驟

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 上的 App Service 中建立.NET Core Web 應用程式](quickstart-dotnetcore.md?pivots=platform-linux)
* [在 Linux 上的 App Service 中建立 Ruby Web 應用程式](quickstart-ruby.md)
* [快速入門：在 App Service 上執行自訂容器](quickstart-custom-container.md?pivots=container-linux)
* [Linux 上的 App Service 常見問題集](faq-app-service-linux.md)
* [設定自訂 Linux 容器](configure-custom-container.md)