---
title: 在容器實例中設置環境變數
description: 了解如何在執行於 Azure 容器執行個體中的容器內設定環境變數
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247224"
---
# <a name="set-environment-variables-in-container-instances"></a>在容器實例中設置環境變數

在您的容器執行個體中設定環境變數，可讓您提供由容器執行之應用程式或指令碼的動態設定。 這類似於 `--env` 命令列引數 `docker run`。 

若要在容器中設定環境變數，請在建立容器執行個體時加以指定。 本文顯示了使用[Azure CLI、Azure](#azure-cli-example) [PowerShell](#azure-powershell-example)和[Azure 門戶](#azure-portal-example)啟動容器時設置環境變數的示例。 

例如，如果運行 Microsoft [aciwordcount][aci-wordcount]容器映射，則可以通過指定以下環境變數來修改其行為：

*NumWords*：傳送至 STDOUT 的字詞數。

*MinLength*：列入計算之字詞中的字元數上限。 較高的數目會略過常用字詞，例如 "of" 和 "the"。

如果您需要將祕密當成環境變數來傳遞，Azure 容器執行個體支援適用於 Windows 和 Linux 容器的[安全值](#secure-values)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI 的範例

要查看[aciwordcount][aci-wordcount]容器的預設輸出，請首先使用此[az 容器創建][az-container-create]命令運行它（未指定環境變數）：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

要修改輸出，請啟動添加`--environment-variables`參數的第二個容器，指定*NumWords*和*MinLength*變數的值。 (此範例假設您在 Bash 殼層或 Azure Cloud Shell 中執行 CLI。 如果您使用 Windows 命令提示字元，請以雙引號指定變數，例如 `--environment-variables "NumWords"="5" "MinLength"="8"`。)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

一旦這兩個容器的狀態顯示為「已終止」** (使用 [az container show][az-container-show] 檢查狀態)，即可使用 [az container logs][az-container-logs] 來顯示其記錄，以查看輸出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

容器的輸出會顯示您如何藉由設定環境變數，來修改第二個容器的指令碼行為。

**我的容器1**
```output
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

**我的容器2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell 的範例

在 PowerShell 中設定環境變數類似於 CLI，但是使用 `-EnvironmentVariable` 命令列引數。

首先，使用此[New-AzContainerGroup][new-Azcontainergroup]命令在其預設配置中啟動[aci 字計數][aci-wordcount]容器：

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

現在運行以下[新阿茲集裝箱組][new-Azcontainergroup]命令。 此命令會在填入陣列變數 `envVars` 後，指定 NumWords** 和 MinLength** 環境變數：

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

兩個容器的狀態*終止*後（使用[Get-AzContainer 實例日誌][azure-instance-log]檢查狀態），使用[Get-AzContainer 實例日誌][azure-instance-log]命令拉取日誌。

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

每個容器的輸出會顯示您如何藉由設定環境變數，來修改容器執行的指令碼。

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure 入口網站範例

要在 Azure 門戶中啟動容器時設置環境變數，請在創建容器時在 **"高級"** 頁中指定它們。

1. 在 **"高級"** 頁上，將 **"重新開機"策略**設置為 *"打開失敗"*
2. 在 **"環境變數"** 下`NumWords`，輸入第一`5`個變數的值，然後輸入`MinLength`第二個變數的值`8`。 
1. 選擇 **"查看 + 創建**"以驗證並部署容器。

![顯示環境變數啟用按鈕和文字方塊的入口網站頁面][portal-env-vars-01]

要查看容器的日誌，請在 **"設置"** 下選擇**容器**，然後**選擇 "日誌**"。 類似於前面 CLI 和 PowerShell 區段中所示的輸出，您可以看到環境變數如何修改指令碼行為。 僅顯示五個字組，每個字組都至少有八個字元長。

![顯示容器記錄輸出的入口網站][portal-env-vars-02]

## <a name="secure-values"></a>安全值

具有安全值的物件可用來保存機密資訊，例如您應用程式的密碼或金鑰。 針對環境變數使用安全值，會比將其包含於容器映像中更安全且更具彈性。 另一個選項是使用祕密磁碟區，如[在 Azure 容器執行個體中掛接祕密磁碟區](container-instances-volume-secret.md)中所述。

在容器的屬性中看不到具有安全值的環境變數 - 只能從容器內存取其值。 例如，在 Azure 入口網站或 Azure CLI 中檢視的容器屬性只會顯示安全變數的名稱，而非其值。

藉由針對變數的類型指定 `secureValue` 屬性而不是一般的 `value` 來設定安全的環境變數。 下列 YAML 中定義的兩個變數會示範這兩個變數類型。

### <a name="yaml-deployment"></a>YAML 部署

使用下列程式碼片段來建立 `secure-env.yaml` 檔案。

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

執行下列命令，使用 YAML 來部署容器群組 (視需要調整資源群組名稱)：

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>確認環境變數

執行 [az container show][az-container-show] 命令來查詢容器的環境變數：

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON 回應會顯示非安全環境變數的索引鍵與值，但只會顯示安全環境變數的名稱：

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

使用 [az container exec][az-container-exec] 命令 (能夠在執行中的容器中執行命令)，您可以驗證是否已設定安全的環境變數。 執行下列命令，以在容器中啟動互動式 Bash 工作階段：

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

一旦在容器內開啟互動式殼層，您就可以存取`SECRET` 變數的值：

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>後續步驟

針對工作型案例，例如使用數個容器的大型資料集批次處理，可受益於執行階段上的自訂環境變數。 有關運行基於任務的容器的詳細資訊，請參閱[使用重新開機策略運行容器化任務](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
