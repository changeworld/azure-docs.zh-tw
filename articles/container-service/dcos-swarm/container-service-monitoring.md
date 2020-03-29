---
title: (已淘汰) 監視 Azure DC/OS 叢集 - Datadog
description: 使用 Datadog 監視 Azure 容器服務叢集。 使用 DC/OS Web UI 將 Datadog 代理程式部署至您的叢集。
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275260"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(已淘汰) 使用 Datadog 監視 Azure Container Service DC/OS 叢集

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

本文中，我們會將 Datadog 代理程式部署到 Azure 容器服務叢集中的所有代理程式節點。 您將需要 Datadog 帳戶以進行這項設定。 

## <a name="prerequisites"></a>Prerequisites
[部署](container-service-deployment.md)和[連接](../container-service-connect.md) Azure Container Service 所設定的叢集。 瀏覽 [Marathon UI](container-service-mesos-marathon-ui.md)。 轉到[https://datadoghq.com](https://datadoghq.com)設置 Datadog 帳戶。 

## <a name="datadog"></a>Datadog
Datadog 是一項監視服務，會從 Azure 容器服務叢集內的容器收集監視資料。 Datadog 有 Docker 整合儀表板，可供您查看容器內的特定度量。 從容器收集到的度量會依 CPU、記憶體、網路和 I/O 來加以整理。 Datadog 會將度量分割成容器和映像。 以下是 CPU 使用量之 UI 樣貌的範例。

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>使用 Marathon 設定 Datadog 部署
這些步驟將說明如何使用 Marathon 設定並將 Datadog 應用程式部署到您的叢集。 

通過[http://localhost:80/](http://localhost:80/)訪問您的 DC/OS UI。 在進入 DC/OS UI 後，瀏覽至位於左下方的 [Universe]，然後搜尋「Datadog」並按一下 [安裝]。

![DC/OS Universe 內的 Datadog 封裝](./media/container-service-monitoring/datadog1.png)

現在，若要完成設定，您將需要 Datadog 帳戶或免費試用帳戶。 登入 Datadog 網站後請看左邊，然後移至 [整合] -> [API](https://app.datadoghq.com/account/settings#api)。 

![Datadog API 金鑰](./media/container-service-monitoring/datadog2.png)

接下來將 API 金鑰輸入到 DC/OS Universe 中的 Datadog 設定。 

![DC/OS Universe 中的 Datadog 設定](./media/container-service-monitoring/datadog3.png) 

在上面的設定中，執行個體會設為 10000000，因此每當有新節點新增至叢集時，Datadog 就會自動將代理程式部署到該新節點。 這是過渡解決方案。 一旦您安裝了封裝，請瀏覽回到 Datadog 網站並尋找「[儀表板](https://app.datadoghq.com/dash/list)」。 從該處，您會看到自訂和整合儀表板。 [Docker 儀表板](https://app.datadoghq.com/screen/integration/docker)會擁有為了監視叢集所需的所有容器計量。 

