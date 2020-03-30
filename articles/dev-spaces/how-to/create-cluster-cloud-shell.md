---
title: 創建啟用 Azure 開發空間的 Kubernetes 群集 - Azure 雲外殼
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何在不安裝任何項目的情況下，直接從瀏覽器快速建立針對 Azure Dev Spaces 所啟用的 Kubernetes 叢集。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605296"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>使用 Azure 雲外殼啟用 Azure 開發空間創建庫伯奈斯群集

您可以使用[Azure 雲外殼](/azure/cloud-shell)使用此頁面中的"**試用"** 按鈕創建 Azure 庫伯奈斯服務群集。 如果您未登入，請遵循提示使用 Azure 帳戶來登入，然後在 Azure Cloud Shell 提示字元出現時，於其中輸入命令。

## <a name="create-the-cluster"></a>建立叢集

首先，在[支援 Azure 開發空間的區域][supported-regions]中創建資源組。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用下列命令來建立 Kubernetes 叢集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

建立叢集需要幾分鐘的時間。  完成時，輸出會以 JSON 格式顯示。 尋找 `provisioningState` 並確認它 `Succeeded`。

## <a name="next-steps"></a>後續步驟

如需完整教學課程的連結，請參閱 [Azure Dev Spaces](/azure/dev-spaces/)。

> [!IMPORTANT]
> 許多 Azure 開發空間快速入門和教程使用 Azure 開發空間 CLI 執行操作。 您無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI。


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service