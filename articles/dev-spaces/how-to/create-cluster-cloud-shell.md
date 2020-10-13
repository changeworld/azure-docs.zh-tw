---
title: 建立已啟用 Azure Dev Spaces 的 Kubernetes 叢集-Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何在不安裝任何項目的情況下，直接從瀏覽器快速建立針對 Azure Dev Spaces 所啟用的 Kubernetes 叢集。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: ce73f46a2451dfa87751d90f9cd6b31d5c80683f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963545"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>使用 Azure Cloud Shell 啟用 Azure Dev Spaces 建立 Kubernetes 叢集

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

您可以使用 [Azure Cloud Shell](/azure/cloud-shell) 建立 Azure Kubernetes Service 叢集，方法是使用此頁面中的 [ **試試看** ] 按鈕。 如果您未登入，請遵循提示使用 Azure 帳戶來登入，然後在 Azure Cloud Shell 提示字元出現時，於其中輸入命令。

## <a name="create-the-cluster"></a>建立叢集

首先，在 [支援 Azure Dev Spaces 的區域][supported-regions]中建立資源群組。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用下列命令來建立 Kubernetes 叢集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

建立叢集需要幾分鐘的時間。  完成時，輸出會以 JSON 格式顯示。 尋找 `provisioningState` 並確認它 `Succeeded`。

## <a name="next-steps"></a>後續步驟

如需完整教學課程的連結，請參閱 [Azure Dev Spaces](../index.yml)。

> [!IMPORTANT]
> 許多 Azure Dev Spaces 快速入門和教學課程都使用 Azure Dev Spaces CLI 來執行作業。 您無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI。


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
