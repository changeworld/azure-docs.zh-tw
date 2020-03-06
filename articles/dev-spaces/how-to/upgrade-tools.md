---
title: 如何升級 Azure Dev Spaces 工具
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: 瞭解如何升級 Azure Dev Spaces 命令列工具、Visual Studio Code 延伸模組，以及 Visual Studio 擴充功能
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375273"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>如何升級 Azure Dev Spaces 工具

如果有提供新的版本，而您目前已在使用 Azure Dev Spaces，則可能需要升級您的 Azure Dev Spaces 用戶端工具。

## <a name="update-the-azure-cli"></a>更新 Azure CLI

當您更新為最新的 Azure CLI 時，也會取得最新版本的 Dev Spaces CLI 擴充功能。

您不需要解除安裝舊版本，只需在 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 找到適合的下載項目。


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>更新 Dev Spaces CLI 擴充功能和命令列工具

執行以下命令：

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>更新 VS Code 擴充功能

安裝之後，會自動更新擴充功能。 您可能需要重新載入擴充功能以使用新功能。 在 VS Code 中，開啟 [擴充功能] 窗格，選擇 [Azure Dev Spaces] 擴充功能，然後選擇 [重新載入]。

## <a name="update-the-visual-studio-extension"></a>更新 Visual Studio 擴充功能

正如其他擴充功能和更新一樣，當適用於 Kubernetes 的 Visual Studio Tools 有可用的更新時 (包括 Azure Dev Spaces)，Visual Studio 會通知您。 在畫面右上角尋找旗標圖示。

若要更新 Visual Studio 中的工具，請選擇 [工具] > [擴充功能和更新] 功能表項目，然後在左側選擇 [更新]。 尋找 [適用於 Kubernetes 的 Visual Studio Tools] ，然後選擇 [更新] 按鈕。

## <a name="next-steps"></a>後續步驟

藉由建立新叢集以測試新工具。 嘗試 [Azure Dev Spaces](/azure/dev-spaces) 的快速入門與教學課程。
