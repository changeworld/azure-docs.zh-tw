---
title: 覆寫容器實例中的進入點
description: 設定命令列，以在您部署 Azure 容器實例時覆寫容器映射中的進入點
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169624"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>在容器實例中設定命令列以覆寫預設的命令列操作

當您建立容器實例時，可選擇性地指定命令，以覆寫容器映射中的預設命令列指令內建。 此行為類似于的 `--entrypoint` 命令列引數 `docker run` 。

如同設定容器實例的 [環境變數](container-instances-environment-variables.md) ，指定啟動命令列適用于您需要使用工作專屬設定來動態準備每個容器的批次作業。

## <a name="command-line-guidelines"></a>命令列指導方針

* 根據預設，命令列指定的 *單一進程會在容器中不含 shell* 的情況下啟動。 例如，命令列可能會執行 Python 腳本或可執行檔。 進程可以指定其他參數或引數。

* 若要執行多個命令，請藉由設定容器作業系統所支援的 shell 環境來開始命令列。 範例：

  |作業系統  |預設 shell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  遵循 shell 的慣例來合併多個命令，以依序執行。

* 根據容器設定，您可能需要設定命令列可執行檔或引數的完整路徑。

* 根據命令列是否指定長時間執行的工作或執行一次的工作，為容器實例設定適當的 [重新開機原則](container-instances-restart-policy.md) 。 例如，建議將或的重新開機原則 `Never` `OnFailure` 用於執行一次的工作。 

* 如果您需要容器映射中預設 entrypoint 集合的相關資訊，請使用 [docker image 檢查](https://docs.docker.com/engine/reference/commandline/image_inspect/) 命令。

## <a name="command-line-syntax"></a>命令列語法

命令列語法會根據用來建立實例的 Azure API 或工具而有所不同。 如果您指定 shell 環境，也請觀察 shell 的命令語法慣例。

* [az container create][az-container-create] 命令：使用參數傳遞字串 `--command-line` 。 範例： `--command-line "python myscript.py arg1 arg2"`) 。

* [新 >remove-azurermcontainergroup][new-azurermcontainergroup] Azure PowerShell Cmdlet：使用參數傳遞字串 `-Command` 。 範例： `-Command "echo hello"`.

* Azure 入口網站：在容器設定的 [ **命令覆寫** ] 屬性中，提供以逗號分隔的字串清單（不含引號）。 範例： `python, myscript.py, arg1, arg2`) 。 

* Resource Manager 範本或 YAML 檔或其中一個 Azure Sdk：將命令列屬性指定為字串陣列。 範例： `["python", "myscript.py", "arg1", "arg2"]` Resource Manager 範本中的 JSON 陣列。 

  如果您熟悉 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 語法，此格式類似于 CMD 指令的 *exec* 形式。

### <a name="examples"></a>範例

|    |  Azure CLI   | 入口網站 | [範本] | 
| ---- | ---- | --- | --- |
| **單一命令** | `--command-line "python myscript.py arg1 arg2"` | **命令覆寫**： `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **多個命令** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**命令覆寫**： `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 的範例

例如，修改 [microsoft/aci-wordcount][aci-wordcount] 容器映射的行為，此映射會分析 Shakespeare 的 *hamlet 文字* 中的文字，以找出最常出現的文字。 您可以設定指向不同文字來源的命令列，而不是分析 *hamlet 文字*。

若要在分析預設文字時查看 [microsoft/aci-wordcount][aci-wordcount] 容器的輸出，請使用下列 [az container create][az-container-create] 命令來執行它。 未指定任何起始命令列，因此預設的容器命令會執行。 為了方便說明，此範例會設定 [環境變數](container-instances-environment-variables.md) ，以找出長度至少為五個字母的前3個單字：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

一旦容器的狀態顯示為已 *終止* (請使用 [az container show][az-container-show] 來檢查狀態) ，以 [az 容器記錄][az-container-logs] 檔顯示記錄檔以查看輸出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

輸出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

現在設定第二個範例容器，藉由指定不同的命令列來分析不同的文字。 容器所執行的 Python 腳本 *wordcount.py*會接受 URL 作為引數，並處理該頁面的內容，而不是預設值。

例如，若要判斷 *Romeo 和 Juliet*中至少有五個字母的前3個單字：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

同樣地，一旦容器是「已終止」**，藉由顯示容器的記錄來檢視輸出：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

輸出：

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>接下來的步驟

以工作為基礎的案例（例如批次處理含有數個容器的大型資料集），可以在執行時間從自訂命令列受益。 如需執行工作型容器的詳細資訊，請參閱 [使用重新開機原則執行容器](container-instances-restart-policy.md)化工作。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
