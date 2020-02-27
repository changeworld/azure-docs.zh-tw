---
title: Azure 上的 Kubernertes 教學課程 - 部署應用程式
description: 在本 Azure Kubernetes Service (AKS) 教學課程中，您會使用 Azure Container Registry 中儲存的自訂映像，將多容器應用程式部署至您的叢集。
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: 3b614fcb6692f35884af2fc4e19210267ab8ab04
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593269"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>教學課程：執行 Azure Kubernetes Service (AKS) 中的應用程式

Kubernetes 會提供容器化應用程式的分散式平台。 您會建置自己的應用程式和服務，並將其部署至 Kubernetes 叢集，並讓該叢集管理可用性和連線能力。 在本教學課程 (七個章節的第四部分) 中，已在 Kubernetes 叢集中部署一個應用程式範例。 您會了解如何：

> [!div class="checklist"]
> * 更新 Kubernetes 資訊清單檔
> * 在 Kubernetes 中執行應用程式
> * 測試應用程式

在其他教學課程中，此應用程式會相應放大並且更新。

本快速入門假設您已有 Kubernetes 概念的基本知識。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝成容器映像、將此映像上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。

若要完成本教學課程中的內容，您需要預先建立的 `azure-vote-all-in-one-redis.yaml` Kubernetes 資訊清單檔。 在先前的教學課程中，此檔案已連同應用程式原始程式碼一起下載。 請確認您擁有複製的存放庫，而且已將目錄變更為複製的目錄。 如果您尚未完成這些步驟，而且想要跟著做，請從[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]開始。

在本教學課程中，您必須執行 Azure CLI 2.0.53 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="update-the-manifest-file"></a>更新資訊清單檔

在這些教學課程中，Azure Container Registry (ACR) 執行個體會儲存範例應用程式的容器映像。 若要部署應用程式，您必須更新 Kubernetes 資訊清單檔中的映像名稱，以納入 ACR 登入伺服器名稱。

使用 [az acr list][az-acr-list] 命令取得 ACR 登入伺服器名稱，如下所示：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

在第一個教學課程中叢 git 存放庫複製的範例資訊清單檔，會使用登入伺服器名稱 *microsoft*。 請確定您位於複製的 *azure-voting-app-redis* 目錄，然後使用文字編輯器開啟資訊清單檔，例如 `vi`：

```console
vi azure-vote-all-in-one-redis.yaml
```

將 *microsoft* 取代為您的 ACR 登入伺服器名稱。 您可以在資訊清單檔的第 51 行找到映像名稱。 下列範例顯示預設映像名稱：

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

提供您自己的 ACR 登入伺服器名稱，使您的資訊清單檔看起來類似於下列範例：

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

儲存並關閉檔案。 在 `vi` 中使用 `:wq`。

## <a name="deploy-the-application"></a>部署應用程式

若要部署應用程式，請使用 [kubectl apply][kubectl-apply] 命令。 此命令會剖析資訊清單檔，並建立已定義的 Kubernetes 物件。 指定範例資訊清單檔，如下列範例所示：

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

下列範例輸出顯示在 AKS 叢集中成功建立的資源：

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```console
kubectl get service azure-vote-front --watch
```

一開始，*azure-vote-front* 服務的 *EXTERNAL-IP* 會顯示為 *pending*：

```
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

若要查看應用程式的實際運作情況，請開啟網頁瀏覽器並瀏覽至服務的外部 IP 位址：

![Azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-tutorials/azure-vote.png)

如果應用程式並未載入，可能是因為您的映像登錄發生授權問題。 若要檢視容器的狀態，請使用 `kubectl get pods` 命令。 如果無法提取容器映像，請參閱[從 Azure Kubernetes Service 對 Azure Container Registry 進行驗證](cluster-container-registry-integration.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，範例 Azure 投票應用程式已部署到 AKS 中的 Kubernetes 叢集。 您已了解如何︰

> [!div class="checklist"]
> * 更新 Kubernetes 資訊清單檔
> * 在 Kubernetes 中執行應用程式
> * 測試應用程式

請繼續進行下一個教學課程，以了解如何調整 Kubernetes 應用程式和基礎 Kubernetes 基礎架構。

> [!div class="nextstepaction"]
> [調整 Kubernetes 應用程式和基礎結構][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
