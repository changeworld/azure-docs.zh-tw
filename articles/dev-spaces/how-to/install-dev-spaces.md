---
title: 在 AKS 上啟用 Azure 開發空間&安裝用戶端工具
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: 瞭解如何在 AKS 群集上啟用 Azure 開發空間並安裝用戶端工具。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898957"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>在 AKS 群集上啟用 Azure 開發空間並安裝用戶端工具

本文介紹在 AKS 群集上啟用 Azure 開發空間以及安裝用戶端工具的幾種方法。

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>使用 CLI 啟用或刪除 Azure 開發空間

在使用 CLI 啟用開發空間之前，您需要：
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶][az-portal-create-account]。
* [已安裝 Azure CLI。][install-cli]
* [受支援區域][supported-regions]中的[AKS 群集][create-aks-cli]。

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

上述命令支援*myAAKSCluster 組中的 myAKSCluster 群集*上的開發人員空間，並創建*預設*開發空間。 *myResourceGroup*

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

該`use-dev-spaces`命令還安裝 Azure 開發空間 CLI。

要從 AKS 群集中刪除 Azure 開發空間`azds remove`，請使用 命令。 例如：

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

上述命令從*MyResourceGroup*中的*MyAKS*群集中刪除 Azure 開發空間。 使用 Azure 開發人員空間創建的任何命名空間都將與其工作負荷一起保留，但這些命名空間中的新工作負荷將不會使用 Azure 開發空間進行檢測。 此外，如果重新開機使用 Azure 開發人員空間檢測的任何現有窗格，可能會看到錯誤。 在沒有 Azure 開發空間工具的情況下，必須重新部署這些窗格。 要從群集中完全刪除 Azure 開發人員空間，請刪除啟用 Azure 開發空間的所有命名空間中的所有窗格。

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>使用 Azure 門戶啟用或刪除 Azure 開發空間

在使用 Azure 門戶啟用開發空間之前，需要：
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶][az-portal-create-account]。
* [受支援區域][supported-regions]中的[AKS 群集][create-aks-portal]。

要使用 Azure 門戶啟用 Azure 開發空間，請使用以下因素：
1. 登錄到 Azure[門戶][az-portal]。
1. 導航到 AKS 群集。
1. 選擇 *"開發空間"* 功能表項目。
1. 將 [啟用 Dev Spaces]** 變更為 [是]**，然後按一下 [儲存]**。

![在 Azure 入口網站中啟用 Dev Spaces](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

使用 Azure 門戶啟用 Azure 開發空間**不會**為 Azure 開發空間安裝任何用戶端工具。

要從 AKS 群集中刪除 Azure 開發空間，請將*啟用開發空間*更改為 *"否*"，然後按一下"*保存*"。 使用 Azure 開發人員空間創建的任何命名空間都將與其工作負荷一起保留，但這些命名空間中的新工作負荷將不會使用 Azure 開發空間進行檢測。 此外，如果重新開機使用 Azure 開發人員空間檢測的任何現有窗格，可能會看到錯誤。 在沒有 Azure 開發空間工具的情況下，必須重新部署這些窗格。 要從群集中完全刪除 Azure 開發人員空間，請刪除啟用 Azure 開發空間的所有命名空間中的所有窗格。

## <a name="install-the-client-side-tools"></a>安裝用戶端工具

可以使用 Azure 開發人員空間用戶端工具從本地電腦與 AKS 群集上的開發空間進行交互。 有幾種方法可以安裝用戶端工具：

* 在[視覺化工作室代碼][vscode]中，安裝[Azure 開發空間擴展][vscode-extension]。
* 在[Visual Studio 2019][visual-studio]中，安裝 Azure 開發工作負荷。
* 在 Visual Studio 2017 中，[為 Kubernetes][visual-studio-k8s-tools]安裝 Web 開發工作負載和視覺化工作室工具。
* 下載並安裝[Windows、Mac][cli-win]或[Linux][cli-linux] CLI。 [Mac][cli-mac]

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
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
