---
title: 從 Kubernetes 叢集進行驗證
description: 瞭解如何使用服務主體建立提取秘密，以提供可存取 Azure container registry 中映射的 Kubernetes 叢集
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 05/28/2020
ms.openlocfilehash: fbf5dfd68b823b600b11cad3643e5d4004b85ff5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84309810"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>將映射從 Azure container registry 提取至 Kubernetes 叢集

您可以使用 Azure container registry 作為具有任何 Kubernetes 叢集的容器映射來源，包括「本機」 Kubernetes 叢集，例如 [minikube](https://minikube.sigs.k8s.io/) 和 [種類](https://kind.sigs.k8s.io/)。 本文說明如何根據 Azure Active Directory 服務主體來建立 Kubernetes 提取秘密。 然後，使用秘密從 Kubernetes 部署中的 Azure container registry 提取映射。

> [!TIP]
> 如果您使用受控 [Azure Kubernetes Service](../aks/intro-kubernetes.md)，也可以將您的叢集與目標 Azure container registry [整合](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) ，以進行映射提取。 

本文假設您已建立私人 Azure container registry。 您也必須有執行中的 Kubernetes 叢集，並可透過 `kubectl` 命令列工具存取。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

如果您未儲存或記住服務主體密碼，則可以使用 [az ad sp credential reset][az-ad-sp-credential-reset] 命令來重設它：

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

此命令會傳回新的有效密碼給您的服務主體。

## <a name="create-an-image-pull-secret"></a>建立影像提取秘密

Kubernetes 使用 *影像提取秘密* 來儲存驗證登錄所需的資訊。 若要建立 Azure container registry 的提取密碼，請提供服務主體識別碼、密碼和登錄 URL。 

使用下列命令建立映射提取密碼 `kubectl` ：

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
其中：

| 值 | 說明 |
| :--- | :--- |
| `secret-name` | 映射提取密碼的名稱，例如 *acr-secret* |
| `namespace` | 將秘密放入其中的 Kubernetes 命名空間 <br/> 只有當您想要將秘密放在預設命名空間以外的命名空間中時，才需要 |
| `container-registry-name` | 您的 Azure container registry 名稱，例如， *myregistry*<br/><br/>`--docker-server`是登錄登入伺服器的完整名稱  |
| `service-principal-ID` | Kubernetes 將用來存取您登錄的服務主體識別碼 |
| `service-principal-password` | 服務主體密碼 |

## <a name="use-the-image-pull-secret"></a>使用映射提取密碼

建立映射提取密碼之後，您可以使用它來建立 Kubernetes pod 和部署。 在部署檔案中提供秘密的名稱 `imagePullSecrets` 。 例如：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

在上述範例中， `my-awesome-app:v1` 是要從 Azure container registry 提取的映射名稱，而且  `acr-secret` 是您所建立用來存取登錄的提取秘密名稱。 當您部署 pod 時，Kubernetes 會自動從您的登錄提取映射（若該映射尚未存在於叢集中）。


## <a name="next-steps"></a>接下來的步驟

* 如需使用服務主體和 Azure Container Registry 的詳細資訊，請參閱 [Azure Container Registry 驗證與服務主體](container-registry-auth-service-principal.md)
* 深入瞭解[Kubernetes 檔](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)中的影像提取秘密


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset