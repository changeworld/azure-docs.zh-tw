---
title: CI/CD 到自訂 Linux 容器
description: 瞭解如何在 Azure 應用服務中設置對自訂 Linux 容器的連續部署。 碼頭中心和 ACR 支援持續部署。
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687622"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>使用用於容器的 Web 應用程式進行持續部署

在本教學課程中，您可以從受控 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 存放庫或 [Docker Hub](https://hub.docker.com) 設定自訂容器映像的持續部署。

## <a name="enable-continuous-deployment-with-acr"></a>透過 ACR 啟用持續部署

![ACR Webhook 的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取頁面左側的 [App Service] **** 選項。
3. 選取您要設定持續部署的應用程式名稱。
4. 在 [容器設定]**** 頁面上，選取 [單一容器]****
5. 選取 [Azure Container Registry]****
6. 選取 [持續部署] > [開啟]****
7. 選取 [儲存]**** 以啟用持續部署。

## <a name="use-the-acr-webhook"></a>使用 ACR Webhook

啟用持續部署之後，您可以在 Azure Container Registry Webhook 頁面上檢視新建立的 Webhook。

![ACR Webhook 的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

在 Container Registry 中，按一下 Webhook 以檢視目前的 Webhook。

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>透過 Docker Hub 啟用持續部署 (選用)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取頁面左側的 [App Service] **** 選項。
3. 選取您要設定持續部署的應用程式名稱。
4. 在 [容器設定]**** 頁面上，選取 [單一容器]****
5. 選取 [Docker Hub]****
6. 選取 [持續部署] > [開啟]****
7. 選取 [儲存]**** 以啟用持續部署。

![應用程式設定的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

複製 Webhook URL。 若要新增 Docker Hub 的 Webhook，請遵循 <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker Hub 的 Webhook</a>。

## <a name="next-steps"></a>後續步驟

* [Linux 上的 Azure App Service 簡介](./app-service-linux-intro.md)
* [Azure 容器註冊表](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 上的 App Service 中建立.NET Core Web 應用程式](quickstart-dotnetcore.md)
* [在 Linux 上的 App Service 中建立 Ruby Web 應用程式](quickstart-ruby.md)
* [在 Web App for Containers 中部署 Docker/Go Web 應用程式](quickstart-docker-go.md)
* [Linux 上的 Azure App Service 常見問題集](./app-service-linux-faq.md)
* [使用 Azure CLI 管理 Web App for Containers](./app-service-linux-cli.md)
