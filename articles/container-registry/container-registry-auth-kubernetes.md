---
title: 從庫伯奈斯群集進行身份驗證
description: 瞭解如何通過使用服務主體創建拉取金鑰，為 Kubernetes 群集提供訪問 Azure 容器註冊表中圖像的存取權限
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154890"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>將映射從 Azure 容器註冊表拉至庫伯奈斯群集

您可以將 Azure 容器註冊表用作任何 Kubernetes 群集的容器映射源，包括"本地"庫伯奈斯群集（如[minikube](https://minikube.sigs.k8s.io/)和[類型](https://kind.sigs.k8s.io/)）。 本文演示如何基於 Azure 活動目錄服務主體創建 Kubernetes 拉取金鑰。 然後，使用 該機密從 Kubernets 部署中的 Azure 容器註冊表中提取映射。

> [!TIP]
> 如果使用託管[的 Azure Kubernetes 服務](../aks/intro-kubernetes.md)，還可以將群集與目標 Azure 容器註冊表[集成](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)以進行映射提取。 

本文假定您已經創建了專用 Azure 容器註冊表。 您還需要運行 Kubernetes 群集並通過`kubectl`命令列工具進行訪問。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

如果不保存或記住服務主體密碼，可以使用[az ad sp 憑據重置][az-ad-sp-credential-reset]命令重置密碼：

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

此命令返回服務主體的新有效密碼。

## <a name="create-an-image-pull-secret"></a>創建圖像拉取金鑰

Kubernetes 使用*映射提取金鑰*來存儲向註冊表進行身份驗證所需的資訊。 要為 Azure 容器註冊表創建拉取金鑰，請提供服務主體 ID、密碼和註冊表 URL。 

使用以下`kubectl`命令創建圖像拉取金鑰：

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
其中：

| 值 | 描述 |
| :--- | :--- |
| `secret-name` | 圖像拉取機密的名稱，例如 *，acr-機密* |
| `namespace` | 庫伯內斯命名空間，把秘密 <br/> 僅當要將機密放置在預設命名空間以外的命名空間中時，才需要 |
| `container-registry-name` | Azure 容器註冊表的名稱 |
| `service-principal-ID` | Kubernetes 將用來訪問註冊表的服務主體的 ID |
| `service-principal-password` | 服務主體密碼 |

## <a name="use-the-image-pull-secret"></a>使用圖像拉取密碼

創建映射拉取金鑰後，可以使用它創建 Kubernetes pod 和部署。 在部署檔中提供機密`imagePullSecrets`的名稱。 例如：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

在前面的示例中，`your-awesome-app:v1`是從 Azure 容器註冊表中提取的圖像的名稱，是`acr-secret`您為訪問註冊表而創建的拉取金鑰的名稱。 部署 Pod 時，Kubernetes 會自動從註冊表中拉出映射（如果群集上尚未存在）。


## <a name="next-steps"></a>後續步驟

* 有關使用服務主體和 Azure 容器註冊表，請參閱[具有服務主體的 Azure 容器註冊表身份驗證](container-registry-auth-service-principal.md)
* 在[庫伯內斯文檔中](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)瞭解有關圖像拉取秘密的更多資料


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset