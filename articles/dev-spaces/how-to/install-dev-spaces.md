---
title: 在 AKS 上啟用 Azure Dev Spaces 並安裝用戶端工具
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: 瞭解如何在 AKS 叢集上啟用 Azure Dev Spaces 並安裝用戶端工具。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: b62c4a4861529c19363f159b8cc64a32a0ba11e8
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996256"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>在 AKS 叢集上啟用 Azure Dev Spaces 並安裝用戶端工具

本文說明在 AKS 叢集上啟用 Azure Dev Spaces 以及安裝用戶端工具的數種方式。

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>使用 CLI 啟用或移除 Azure Dev Spaces

需先達成以下條件，方能使用 CLI 啟用 Dev Spaces：
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶][az-portal-create-account]。
* [已安裝 Azure CLI][install-cli]。
* [支援的區域][supported-regions]中的 [AKS 叢集][create-aks-cli]。

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

上列命令會在 *myResourceGroup* 群組中的 *myAKSCluster* 叢集上啟用 Dev Spaces，並建立預設開發空間。

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces` 命令也會安裝 Azure Dev Spaces CLI。

若要從您的 AKS 叢集中移除 Azure Dev Spaces，請使用 `azds remove` 命令。 例如：

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

上述命令會從 *MyResourceGroup*中的 *MyAKS* 叢集移除 Azure Dev Spaces。 以 Azure Dev Spaces 建立的所有命名空間，都會保留在其工作負載中，但這些命名空間中的新工作負載將不會使用 Azure Dev Spaces 進行檢測。 此外，如果您重新啟動任何以 Azure Dev Spaces 檢測的現有 Pod，可能會看到錯誤。 必須在不使用 Azure Dev Spaces 工具處理的情況下，重新部署這些 Pod。 若要從您的叢集中完全移除 Azure Dev Spaces，請在所有啟用 Azure Dev Spaces 的命名空間中，刪除所有 Pod。

## <a name="install-the-client-side-tools"></a>安裝用戶端工具

您可以使用 Azure Dev Spaces 的用戶端工具，從您的本機電腦與 AKS 叢集上的開發空間互動。 有幾種方式可以安裝用戶端工具：

* 在 [Visual Studio Code][vscode] 中，安裝 [Azure Dev Spaces 擴充功能][vscode-extension]。
* 在 [Visual Studio 2019][visual-studio] 中，安裝 Azure 開發工作負載。
* 下載並安裝 [Windows][cli-win]、[Mac][cli-mac] 或 [Linux][cli-linux] CLI。

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
