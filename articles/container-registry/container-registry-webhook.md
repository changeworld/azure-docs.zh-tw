---
title: 使用 Webhook 以回應登錄動作
description: 了解當登錄存放庫發生推送或提取動作時，如何使用 Webhook 來觸發事件。
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 3d0a12b6e742b0209cbb746a70686423f2fb5627
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685027"
---
# <a name="using-azure-container-registry-webhooks"></a>使用 Azure Container Registry Webhook

Azure Container Registry 會儲存和管理私人 Docker 容器映像，其方式類似於 Docker 中樞儲存公用 Docker 映像。 也可以裝載 [Helm 圖表](container-registry-helm-repos.md) (預覽) 的存放庫，這是將應用程式部署至 Kubernetes 的封裝格式。 在其中一個登錄存放庫發生特定動作時，您可以使用 Webhook 來觸發事件。 Webhook 可以回應登錄層級的事件，或可將範圍縮小至特定的存放庫標記。 [異地複寫](container-registry-geo-replication.md)登錄可讓您設定每個 Webhook 來回應特定區域複本中的事件。

如需 Webhook 要求的詳細資訊，請參閱 [Azure Container Registry Webhook 結構描述參考](container-registry-webhook-reference.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 容器登錄 - 在 Azure 訂用帳戶中建立容器登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 [Azure Container Registry 服務層](container-registry-skus.md)具有不同的 Webhook 配額。
* Docker CLI - 若要將您的本機電腦設定為 Docker 主機並存取 Docker CLI 命令，請安裝 [Docker 引擎](https://docs.docker.com/engine/installation/)。

## <a name="create-webhook---azure-portal"></a>建立 Webhook - Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您要在其中建立 Webhook 的容器登錄。
1. 在 [服務] 下選取 [Webhook]。
1. 選取 [Webhook] 工具列中的 [新增]。
1. 填入下列資訊以完成 [建立 Webhook] 表單：

| 值 | 描述 |
|---|---|
| Webhook 名稱 | 您想要提供給 Webhook 的名稱。 只能包含字母和數字，且長度必須為 5-50 個字元。 |
| Location | 針對[異地複寫](container-registry-geo-replication.md)登錄，請指定登錄複本的 Azure 區域。 
| 服務 URI | Webhook 需在當中傳送 POST 通知的 URI。 |
| 自訂標頭 | 您想要與 POST 要求一起傳遞的標頭。 它們應該為「金鑰：值」的格式。 |
| 觸發程序動作 | 觸發 Webhook 的動作。 動作包括映像推送、映像刪除、Helm 圖表推送、Helm 圖表刪除和映像隔離。 您可以選擇一或多個動作來觸發 Webhook。 |
| 狀態 | Webhook 建立之後的狀態。 此選項預設為啟用狀態。 |
| 影響範圍 | Webhook 的運作範圍。 如果未指定，則以登錄中的所有事件為範圍。 您可以針對一個存放庫或標記來指定範圍，格式為 "repository:tag"，或 "repository:*"，代表存放庫下的所有標記。 |

範例 Webhook 表單：

![在 Azure 入口網站中建立 ACR Webhook](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>建立 Webhook - Azure CLI

若要使用 Azure CLI 建立 Webhook，請使用 [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) 命令。 下列命令針對登錄 *mycontainerregistry* 中的所有映像刪除事件，建立 Webhook：

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>測試 Webhook

### <a name="azure-portal"></a>Azure 入口網站

使用 Webhook 之前，您可以使用 [Ping] 按鈕進行測試。 Ping 會將泛型 POST 要求傳送至指定的端點，並記錄回應。 使用 Ping 功能可協助您確認您已正確設定 Webhook。

1. 選取您想要測試的 Webhook。
2. 在頂端工具列中，選取 [Ping]。
3. 檢查 HTTP STATUS 資料行中的端點回應。

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

依序選取 Azure 入口網站上的 [Webhook] 和 [刪除] 按鈕，就可以將每個 Webhook 刪除。

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
