---
title: 在容器實例上設定準備就緒探查
description: 瞭解如何設定探查，以確保 Azure 容器實例中的容器只會在準備好時才接收要求
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935687"
---
# <a name="configure-readiness-probes"></a>設定整備度探查

針對服務流量的容器化應用程式，您可能會想要確認您的容器已準備好處理傳入的要求。 Azure 容器實例支援準備就緒探查以包含設定，因此在某些情況下無法存取您的容器。 準備就緒探查的行為就像是[Kubernetes 的準備探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。 例如，容器應用程式可能需要在啟動期間載入大型資料集，而且您不想讓它在這段時間內接收要求。

本文說明如何部署包含準備就緒探查的容器群組，讓容器只會在探查成功時收到流量。

Azure 容器實例也支援「[活動探查](container-instances-liveness-probe.md)」，您可以將其設定為導致狀況不良的容器自動重新開機。

> [!NOTE]
> 目前您無法在部署至虛擬網路的容器群組中使用準備就緒探查。

## <a name="yaml-configuration"></a>YAML 設定

例如，使用包含準備就緒探查的下列程式碼片段來建立 `readiness-probe.yaml` 檔案。 此檔案會定義由執行小型 web 應用程式的容器所組成的容器群組。 應用程式會從公用 `mcr.microsoft.com/azuredocs/aci-helloworld` 映射進行部署。 此容器化應用程式也會在使用 Azure CLI 和其他快速入門在[Azure 中部署容器實例](container-instances-quickstart.md)中示範。

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>啟動命令

部署包含一個 `command` 屬性，可定義在容器第一次開始執行時執行的啟動命令。 這個屬性會接受字串陣列。 此命令會模擬 web 應用程式執行的時間，但容器尚未就緒。 

首先，它會啟動 shell 會話，並執行 `node` 命令來啟動 web 應用程式。 它也會開始睡眠240秒，然後在 `/tmp` 目錄中建立名為 `ready` 的檔案：

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>準備就緒命令

這個 YAML 檔會定義一個 `readinessProbe`，其支援作為準備檢查的 `exec` 就緒性命令。 此範例準備就緒命令會測試 `/tmp` 目錄中是否存在 `ready` 檔案。

當 `ready` 檔案不存在時，準備就緒命令會以非零值結束;容器會繼續執行，但無法存取。 當命令成功結束，結束代碼為0時，就可以存取容器。 

`periodSeconds` 屬性會指定應該每5秒執行一次的準備就緒命令。 準備就緒探查會在容器群組的存留期內執行。

## <a name="example-deployment"></a>部署範例

執行下列命令，以使用先前的 YAML 設定來部署容器群組：

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>查看準備就緒檢查

在此範例中，在前240秒期間，準備就緒命令會在檢查 `ready` 檔案是否存在時失敗。 狀態碼傳回的信號表示容器尚未就緒。

這些事件可從 Azure 入口網站或 Azure CLI 來檢視。 例如，入口網站會顯示「準備就緒」命令失敗時所觸發 `Unhealthy` 類型的事件。 

![入口網站狀況不良的事件][portal-unhealthy]

## <a name="verify-container-readiness"></a>確認容器就緒

啟動容器之後，您可以確認它最初無法存取。 布建之後，取得容器群組的 IP 位址：

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

當準備就緒探查失敗時，嘗試存取該網站：

```bash
wget <ipAddress>
```

輸出顯示一開始無法存取網站：
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240秒之後，準備就緒命令會成功，通知容器已就緒。 現在，當您執行 `wget` 命令時，它會成功：

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

當容器準備就緒時，您也可以使用網頁瀏覽器流覽至 IP 位址來存取 web 應用程式。

> [!NOTE]
> 準備就緒探查會繼續在容器群組的存留期內執行。 如果準備就緒命令稍後失敗，則容器會再次變成無法存取。 
> 

## <a name="next-steps"></a>後續步驟

在涉及包含相依容器的多個容器群組的案例中，準備就緒探查可能會很有用。 如需多容器案例的詳細資訊，請參閱[Azure 容器實例中的容器群組](container-instances-container-groups.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
