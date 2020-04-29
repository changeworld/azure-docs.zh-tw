---
title: 從 Kubernetes 叢集進行驗證
description: 瞭解如何藉由使用服務主體建立提取密碼，來提供可存取 Azure container registry 中映射的 Kubernetes 叢集
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77154890"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>將映射從 Azure container registry 提取到 Kubernetes 叢集

您可以使用 Azure container registry 作為任何 Kubernetes 叢集的容器映射來源，包括「本機」 Kubernetes 叢集，例如[minikube](https://minikube.sigs.k8s.io/)和[種類](https://kind.sigs.k8s.io/)。 本文說明如何建立以 Azure Active Directory 服務主體為基礎的 Kubernetes 提取密碼。 然後，使用密碼從 Kubernetes 部署中的 Azure container registry 提取映射。

> [!TIP]
> 如果您使用受控[Azure Kubernetes Service](../aks/intro-kubernetes.md)，您也可以將叢集與目標 Azure container registry[整合](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)，以進行映射提取。 

本文假設您已建立私人 Azure container registry。 您也必須擁有執行中的 Kubernetes 叢集，並可透過`kubectl`命令列工具存取。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

如果您不儲存或記住服務主體的密碼，您可以使用[az ad sp credential reset][az-ad-sp-credential-reset]命令來重設它：

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

此命令會為您的服務主體傳回新的有效密碼。

## <a name="create-an-image-pull-secret"></a>建立映射提取秘密

Kubernetes 會使用*映射提取密碼*來儲存向登錄進行驗證所需的資訊。 若要建立 Azure container registry 的提取密碼，請提供服務主體識別碼、密碼和登錄 URL。 

使用下列`kubectl`命令建立映射提取密碼：

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
| `secret-name` | 映射提取密碼的名稱，例如*acr-secret* |
| `namespace` | 要放入秘密的 Kubernetes 命名空間 <br/> 只有在您想要將秘密放在預設命名空間以外的命名空間中時，才需要 |
| `container-registry-name` | Azure container registry 的名稱 |
| `service-principal-ID` | Kubernetes 用來存取您的登錄之服務主體的識別碼 |
| `service-principal-password` | 服務主體密碼 |

## <a name="use-the-image-pull-secret"></a>使用映射提取秘密

建立映射提取密碼之後，您就可以使用它來建立 Kubernetes pod 和部署。 在部署檔案中提供下`imagePullSecrets`的密碼名稱。 例如：

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

在上述範例中， `your-awesome-app:v1`是要從 Azure container registry 提取的映射名稱，而`acr-secret`是您所建立用來存取登錄的提取密碼名稱。 當您部署 pod 時，Kubernetes 會自動從您的登錄提取映射（如果它尚未存在於叢集上）。


## <a name="next-steps"></a>後續步驟

* 如需使用服務主體和 Azure Container Registry 的詳細資訊，請參閱[使用服務主體 Azure Container Registry 驗證](container-registry-auth-service-principal.md)
* 在[Kubernetes 檔](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)中深入瞭解映射提取秘密


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset