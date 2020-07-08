---
title: 覆寫容器實例中的進入點
description: 設定命令列，以在部署 Azure 容器實例時覆寫容器映射中的進入點
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84693051"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>在容器實例中設定命令列，以覆寫預設的命令列操作

當您建立容器實例時，可選擇性地指定命令，將預設命令列指令內建覆寫至容器映射。 這個行為類似于的 `--entrypoint` 命令列引數 `docker run` 。

如同設定容器實例的[環境變數](container-instances-environment-variables.md)，指定起始命令列對於需要使用工作特定設定動態準備每個容器的批次作業很有用。

## <a name="command-line-guidelines"></a>命令列指導方針

* 根據預設，命令列會指定在容器中沒有 shell 的情況下*啟動的單一進程*。 例如，命令列可能會執行 Python 腳本或可執行檔。 進程可以指定其他參數或引數。

* 若要執行多個命令，請在容器作業系統中設定支援的 shell 環境，以開始您的命令列。 範例：

  |作業系統  |預設 shell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  遵循 shell 的慣例，結合多個要依序執行的命令。

* 視容器設定而定，您可能需要設定命令列可執行檔或引數的完整路徑。

* 根據命令列是否指定長時間執行的工作或一次性的工作，設定容器實例的適當[重新開機原則](container-instances-restart-policy.md)。 例如， `Never` `OnFailure` 針對執行一次工作，建議使用或的重新開機原則。 

* 如果您需要在容器映射中設定預設進入點的相關資訊，請使用[docker 映射檢查](https://docs.docker.com/engine/reference/commandline/image_inspect/)命令。

## <a name="command-line-syntax"></a>命令列語法

命令列語法會根據用來建立實例的 Azure API 或工具而有所不同。 如果您指定 shell 環境，也請觀察 shell 的命令語法慣例。

* [az container create][az-container-create]命令：使用參數傳遞字串 `--command-line` 。 範例： `--command-line "python myscript.py arg1 arg2"` ）。

* [新增-get-azurermcontainergroup][new-azurermcontainergroup]Azure PowerShell Cmdlet：傳遞具有參數的字串 `-Command` 。 範例： `-Command "echo hello"`.

* Azure 入口網站：在容器設定的 [**命令覆寫**] 屬性中，提供以逗號分隔的字串清單，不含引號。 範例： `python, myscript.py, arg1, arg2` ）。 

* Resource Manager 範本或 YAML 檔案，或其中一個 Azure Sdk：指定命令列屬性做為字串陣列。 範例： `["python", "myscript.py", "arg1", "arg2"]` Resource Manager 範本中的 JSON 陣列。 

  如果您很熟悉[Dockerfile](https://docs.docker.com/engine/reference/builder/)語法，此格式類似于 CMD 指令的*exec*形式。

### <a name="examples"></a>範例

|    |  Azure CLI   | 入口網站 | [範本] | 
| ---- | ---- | --- | --- |
| 單一命令 | `--command-line "python myscript.py arg1 arg2"` | **命令覆寫**：`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 多個命令 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**命令覆寫**：`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 的範例

例如，修改[microsoft/aci-wordcount][aci-wordcount]容器映射的行為，它會分析 Shakespeare 的*hamlet 文字*中的文字，以尋找最常出現的文字。 您可以設定指向不同文字來源的命令列，而不是分析*hamlet 文字*。

若要在分析預設文字時查看[microsoft/aci-wordcount][aci-wordcount]容器的輸出，請使用下列[az container create][az-container-create]命令來執行。 未指定 start 命令列，因此會執行預設的容器命令。 為了方便說明，這個範例會設定[環境變數](container-instances-environment-variables.md)，以尋找至少五個字母長的前3個單字：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

容器的狀態顯示為「已*終止*」（使用[az container show][az-container-show]來檢查狀態）後，請使用[az container logs][az-container-logs]來顯示記錄檔，以查看輸出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

輸出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

現在設定第二個範例容器，藉由指定不同的命令列來分析不同的文字。 容器（ *wordcount.py*）所執行的 Python 腳本會接受 URL 做為引數，並處理該頁面的內容，而不是預設的。

例如，若要判斷*Romeo 和 Juliet*中長度至少為五個字母的前3個單字：

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

## <a name="next-steps"></a>後續步驟

以工作為基礎的案例（例如批次處理含有數個容器的大型資料集）可在執行時間受益于自訂命令列。 如需有關執行以工作為基礎之容器的詳細資訊，請參閱[使用重新開機原則執行容器](container-instances-restart-policy.md)化工作。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
