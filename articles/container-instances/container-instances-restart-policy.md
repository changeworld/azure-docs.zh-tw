---
title: 重新開機運行一次任務的策略
description: 了解如何使用 Azure Container Instances 來執行工作，該工作會執行到完成為止，例如建置、測試或映像轉譯作業。
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131502"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>使用重新啟動原則執行容器化工作

在 Azure Container Instances 中部署容器的簡易和快速，提供令人信服的平台，可以用來進行執行一次的工作，例如在容器執行個體中建置、測試和映像轉譯。

使用可設定的重新啟動原則，您可以指定當容器的程序完成時，停止容器。 因為容器執行個體是由第二個開始計費，所以您只需支付當執行您的工作之容器執行時所使用的計算資源。

本文所呈現的範例是使用 Azure CLI。 您必須[在本機安裝][azure-cli-install] Azure CLI 版本 2.0.21 或更新版本，或者使用 [Azure Cloud Shell](../cloud-shell/overview.md) 中的 CLI。

## <a name="container-restart-policy"></a>容器重新啟動原則

當您在 Azure Container Instances 中建立[容器群組](container-instances-container-groups.md)時，您可以指定三個重新啟動原則設定的其中一個。

| 重新啟動原則   | 描述 |
| ---------------- | :---------- |
| `Always` | 容器群組中的容器一律會重新啟動。 這是在建立容器之際未指定重新啟動原則時，套用的**預設**設定。 |
| `Never` | 容器群組中的容器永不重新啟動。 容器最多執行一次。 |
| `OnFailure` | 容器群組中的容器只會在容器中的程序執行失敗時 (當它以非零結束代碼終止時) 重新啟動。 容器執行至少一次。 |

## <a name="specify-a-restart-policy"></a>指定重新啟動原則

您指定重新啟動原則的方式取決於如何建立容器執行個體，例如使用 Azure CLI、Azure PowerShell Cmdlet，或是 Azure 入口網站。 在 Azure CLI 中，當您呼叫 [az container create][az-container-create] 時指定 `--restart-policy` 參數。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>執行至完成範例

要查看重新開機策略的生效，請從 Microsoft [aci 字計數][aci-wordcount-image]映射創建容器實例，並指定`OnFailure`重新開機策略。 此範例容器會執行 Python 指令碼，根據預設，它會分析 Shakespeare 的 [Hamlet](http://shakespeare.mit.edu/hamlet/full.html) 的文字，將 10 個最常見的字詞寫入 STDOUT，然後結束。

使用下列 [az container create][az-container-create] 命令執行範例容器：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances 會啟動容器，然後在它的應用程式 (或者是這個案例中的指令碼) 結束時停止它。 當 Azure Container Instances 停止其重新啟動原則為 `Never` 或 `OnFailure` 的容器時，容器的狀態會設定為「已終止」****。 您可以使用 [az container show][az-container-show] 命令來檢查容器的狀態：

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

範例輸出︰

```bash
"Terminated"
```

一旦範例容器的狀態顯示「已終止」**，您可以藉由檢視容器記錄來查看其工作輸出。 執行 [az container logs][az-container-logs] 命令以檢視指令碼的輸出：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

輸出：

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

這個範例會顯示指令碼傳送到 STDOUT 的輸出。 您容器化工作，不過，可能會改為將其輸出寫入永續性儲存體以供日後擷取。 例如，寫入至 [Azure 檔案共用](container-instances-mounting-azure-files-volume.md)。

## <a name="next-steps"></a>後續步驟

基於任務的方案（如批次處理具有多個容器的大型資料集）可以利用運行時的自訂[環境變數](container-instances-environment-variables.md)或[命令列](container-instances-start-command.md)。

如需如何保存執行至完成之容器的輸入的詳細資訊，請參閱[使用 Azure Container Instances 來掛接 Azure 檔案共用](container-instances-mounting-azure-files-volume.md)。

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
