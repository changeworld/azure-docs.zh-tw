---
title: 在容器實例上設定準備就緒探查
description: 瞭解如何設定探查，以確保 Azure 容器實例中的容器只會在已準備就緒時接收要求
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169658"
---
# <a name="configure-readiness-probes"></a>設定整備度探查

針對服務流量的容器化應用程式，您可能會想要確認容器是否已準備好處理傳入的要求。 Azure 容器實例支援準備就緒探查以包含設定，因此在某些情況下無法存取您的容器。 就緒探查的行為就像是 [Kubernetes 的準備探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。 例如，容器應用程式可能需要在啟動時載入大型資料集，而且您不希望它在這段時間內收到要求。

本文說明如何部署包含就緒探查的容器群組，讓容器只會在探查成功時收到流量。

Azure 容器實例也支援 [活動探查](container-instances-liveness-probe.md)，您可將其設定為使狀況不良的容器自動重新開機。

> [!NOTE]
> 您目前無法在已部署至虛擬網路的容器群組中使用就緒程度探查。

## <a name="yaml-configuration"></a>YAML 設定

例如，建立 `readiness-probe.yaml` 具有下列程式碼片段的檔案，其中包含就緒探查。 此檔案會定義容器群組，其中包含執行小型 web 應用程式的容器。 從公用映射部署應用程式 `mcr.microsoft.com/azuredocs/aci-helloworld` 。 此容器化應用程式也會示範如何使用 Azure CLI 和其他快速入門，在 [Azure 中部署容器實例](container-instances-quickstart.md) 。

```yaml
apiVersion: 2019-12-01
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

### <a name="start-command"></a>Start 命令

部署包含 `command` 定義開始命令的屬性，該命令會在容器第一次開始執行時執行。 這個屬性會接受字串的陣列。 此命令會模擬 web 應用程式執行的時間，但是容器尚未就緒。 

首先，它會啟動 shell 會話，並執行 `node` 命令來啟動 web 應用程式。 它也會啟動命令以睡眠240秒，之後它會在目錄內建立一個名為的檔案 `ready` `/tmp` ：

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>就緒命令

這個 YAML 檔會定義， `readinessProbe` 它支援可 `exec` 作為就緒狀態檢查的就緒命令。 此範例就緒命令會測試目錄中的檔案是否存在 `ready` `/tmp` 。

當檔案 `ready` 不存在時，就緒命令會以非零值結束; 容器會繼續執行，但無法存取。 當命令成功結束時，結束代碼為0，就可以開始存取容器。 

`periodSeconds`屬性指定每隔5秒執行一次就緒命令。 就緒探查會在容器群組的存留期執行。

## <a name="example-deployment"></a>部署範例

執行下列命令，以使用先前的 YAML 設定來部署容器群組：

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>查看就緒狀態檢查

在此範例中，在前240秒內，就緒命令會在檢查檔案 `ready` 是否存在時失敗。 狀態碼傳回容器未就緒的信號。

這些事件可從 Azure 入口網站或 Azure CLI 來檢視。 例如，入口網站 `Unhealthy` 會顯示在「就緒」命令失敗時觸發的事件種類。 

![入口網站狀況不良的事件][portal-unhealthy]

## <a name="verify-container-readiness"></a>確認容器是否就緒

啟動容器之後，您可以先確認它無法存取。 布建之後，取得容器群組的 IP 位址：

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

在準備就緒探查失敗時，嘗試存取網站：

```bash
wget <ipAddress>
```

輸出顯示無法一開始就存取網站：
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240秒後，成功的命令便會成功，並通知容器已就緒。 現在，當您執行 `wget` 命令時，它會成功：

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

當容器就緒時，您也可以使用網頁瀏覽器流覽至 IP 位址，以存取 web 應用程式。

> [!NOTE]
> 準備就緒探查會在容器群組的存留期繼續執行。 如果準備就緒命令于稍後失敗，則容器會再次變成無法存取。 
> 

## <a name="next-steps"></a>接下來的步驟

在包含相依容器的多個容器群組的案例中，可能會有一個就緒探查可用。 如需多容器案例的詳細資訊，請參閱 [Azure 容器實例中的容器群組](container-instances-container-groups.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
