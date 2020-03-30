---
title: 覆蓋容器實例中的進入點
description: 設置命令列以在部署 Azure 容器實例時覆蓋容器映射中的進入點
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247120"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>在容器實例中設置命令列以覆蓋預設命令列操作

創建容器實例時，可以選擇指定一個命令來覆蓋烘焙到容器映射中的預設命令列指令。 此行為類似于`--entrypoint`命令`docker run`行參數。

與為容器實例設置[環境變數](container-instances-environment-variables.md)一樣，指定起始命令列對於需要使用特定于任務的配置動態準備每個容器的批次處理作業非常有用。

## <a name="command-line-guidelines"></a>命令列準則

* 預設情況下，命令列指定一個*進程，該進程在*容器中沒有 shell 啟動。 例如，命令列可能運行 Python 腳本或可執行檔。 進程可以指定其他參數或參數。

* 要執行多個命令，請通過設置容器作業系統中支援的 shell 環境來開始命令列。 範例：

  |作業系統  |預設外殼  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |高山     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  按照 shell 的約定合併多個命令以按順序運行。

* 根據容器配置，您可能需要設置命令列可執行檔或參數的完整路徑。

* 為容器實例設置適當的[重新開機策略](container-instances-restart-policy.md)，具體取決於命令列是指定長時間運行的任務還是運行一次的任務。 例如，對於運行一次的任務`Never`，`OnFailure`建議重新開機策略 或。 

* 如果需要有關容器映射中設置的預設進入點的資訊，請使用[Docker 映射檢查](https://docs.docker.com/engine/reference/commandline/image_inspect/)命令。

## <a name="command-line-syntax"></a>命令列語法

命令列語法因用於創建實例的 Azure API 或工具而異。 如果指定 shell 環境，則還要遵守 shell 的命令語法約定。

* [az 容器創建][az-container-create]命令：使用`--command-line`參數傳遞字串。 示例： `--command-line "python myscript.py arg1 arg2"`。

* [新 AzureRm 容器組][new-azurermcontainergroup]Azure PowerShell Cmdlet：傳遞帶有參數`-Command`的字串。 範例： `-Command "echo hello"`.

* Azure 門戶：在容器配置的命令**重寫**屬性中，提供逗號分隔的字串清單，而不提供引號。 示例： `python, myscript.py, arg1, arg2`。 

* 資源管理器範本或 YAML 檔或 Azure SDK 之一：將命令列屬性指定為字串陣列。 示例：資源管理器範本中的`["python", "myscript.py", "arg1", "arg2"]`JSON 陣列。 

  如果您熟悉[Dockerfile](https://docs.docker.com/engine/reference/builder/)語法，則此格式類似于 CMD 指令的*exec*形式。

### <a name="examples"></a>範例

|    |  Azure CLI   | 入口網站 | [範本] | 
| ---- | ---- | --- | --- |
| 單一命令 | `--command-line "python myscript.py arg1 arg2"` | **命令重寫**：`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 多個命令 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**命令重寫**：`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 的範例

例如，修改[Microsoft/ci 字計數][aci-wordcount]容器圖像的行為，該圖像分析莎士比亞的 *《哈姆雷特》* 中的文本，以查找最常出現的單詞。 您可以設置指向其他文本源的命令列，而不是分析*哈姆雷特*。

要查看[Microsoft/aci 字計數][aci-wordcount]容器在分析預設文本時的輸出，請使用以下[az 容器創建][az-container-create]命令運行它。 未指定 start 命令列，因此運行預設容器命令。 為了便於說明，本示例將[環境變數](container-instances-environment-variables.md)設置以查找至少五個字母的前 3 個單詞：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

一旦容器的狀態顯示為 *"已終止*"（使用[az 容器顯示][az-container-show]以檢查狀態），則使用[az 容器日誌][az-container-logs]顯示日誌以查看輸出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

輸出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

現在設置第二個示例容器，通過指定不同的命令列來分析不同的文本。 容器執行的 Python 腳本*wordcount.py*接受 URL 作為參數，並處理該頁面的內容而不是預設值。

例如，要確定羅密歐*與茱麗葉*中至少五個字母的前3個單詞：

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

基於任務的方案（如批次處理具有多個容器的大型資料集）可以從運行時的自訂命令列中獲益。 有關運行基於任務的容器的詳細資訊，請參閱[使用重新開機策略運行容器化任務](container-instances-restart-policy.md)。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
