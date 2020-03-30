---
title: 回應註冊表操作的 Web 鉤子
description: 瞭解如何在註冊表存儲庫中發生推送或拉取操作時使用 Webhook 觸發事件。
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454368"
---
# <a name="using-azure-container-registry-webhooks"></a>使用 Azure Container Registry Webhook

Azure Container Registry 會儲存和管理私人 Docker 容器映像，其方式類似於 Docker 中樞儲存公用 Docker 映像。 它還可以託管[Helm 圖表](container-registry-helm-repos.md)（預覽）存儲庫，這是一種打包格式，用於將應用程式部署到庫伯內特斯。 在其中一個登錄存放庫發生特定動作時，您可以使用 Webhook 來觸發事件。 Webhook 可以回應登錄層級的事件，或可將範圍縮小至特定的存放庫標記。 使用[異地複製](container-registry-geo-replication.md)註冊表，您可以配置每個 Webhook 以回應特定區域副本中的事件。

如需 Webhook 要求的詳細資訊，請參閱 [Azure Container Registry Webhook 結構描述參考](container-registry-webhook-reference.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 容器登錄 - 在 Azure 訂用帳戶中建立容器登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 [Azure 容器註冊表 SKU](container-registry-skus.md)具有不同的 Webhook 配額。
* Docker CLI - 要將本地電腦設置為 Docker 主機並訪問 Docker CLI 命令，請安裝[Docker 引擎](https://docs.docker.com/engine/installation/)。

## <a name="create-webhook---azure-portal"></a>創建網頁鉤子 - Azure 門戶

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 瀏覽至您要在其中建立 Webhook 的容器登錄。
1. 在 **"服務**"下，選擇 **"網路鉤子**"。
1. 選取 [Webhook] 工具列中的 [新增]****。
1. 使用以下資訊填寫 *"創建 Webhook"* 表單：

| 值 | 描述 |
|---|---|
| 網鉤名稱 | 您想要提供給 Webhook 的名稱。 它只能包含字母和數位，長度必須為 5-50 個字元。 |
| Location | 對於[異地複製](container-registry-geo-replication.md)的註冊表，請指定註冊表副本的 Azure 區域。 
| 服務 URI | Webhook 需在當中傳送 POST 通知的 URI。 |
| 自訂標頭 | 您想要與 POST 要求一起傳遞的標頭。 它們應該為「金鑰：值」的格式。 |
| 觸發程序動作 | 觸發 Webhook 的動作。 操作包括圖像推送、圖像刪除、Helm 圖表推送、Helm 圖表刪除和圖像隔離。 您可以選擇一個或多個操作來觸發 Webhook。 |
| 狀態 | Webhook 建立之後的狀態。 此選項預設為啟用狀態。 |
| 影響範圍 | Webhook 的運作範圍。 如果未指定，則作用域適用于註冊表中的所有事件。 可以使用存儲庫下的所有標記的格式"存儲庫：標記"或"存儲庫：*"為存儲庫或標記指定它。 |

範例 Webhook 表單：

![在 Azure 入口網站中建立 ACR Webhook](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>創建網路掛鉤 - Azure CLI

若要使用 Azure CLI 建立 Webhook，請使用 [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) 命令。 以下命令為註冊表*我的容器註冊表*中的所有圖像刪除事件創建 Webhook ：

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>測試 Webhook

### <a name="azure-portal"></a>Azure 入口網站

在使用 Webhook 之前，可以使用**Ping**按鈕對其進行測試。 Ping 會將泛型 POST 要求傳送至指定的端點，並記錄回應。 使用 Ping 功能可協助您確認您已正確設定 Webhook。

1. 選取您想要測試的 Webhook。
2. 在頂端工具列中，選取 [Ping]****。
3. 檢查 HTTP STATUS**** 資料行中的端點回應。

![在 Azure 入口網站中建立 ACR Webhook](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 測試 ACR Webhook，請使用 [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) 命令。

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

若要查看結果，請使用 [az acr webhook list-events](/cli/azure/acr/webhook) 命令。

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>刪除 Webhook

### <a name="azure-portal"></a>Azure 入口網站

依序選取 Azure 入口網站上的 [Webhook] 和 [刪除]**** 按鈕，就可以將每個 Webhook 刪除。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>後續步驟

### <a name="webhook-schema-reference"></a>Webhook 結構描述參考

若要深入了解 Azure Container Registry 所發出的 JSON 事件裝載格式與內容，請參閱 Webhook 結構描述參考：

[Azure Container Registry Webhook 結構描述參考](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>事件方格的事件

除了本文所討論的原生登錄 Webhook 事件，Azure Container Registry 可將事件發送至事件方格：

[快速入門：將容器登錄事件傳送至事件方格](container-registry-event-grid-quickstart.md)
