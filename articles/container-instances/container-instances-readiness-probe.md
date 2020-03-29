---
title: 在容器實例上設置就緒探測
description: 瞭解如何配置探測，以確保 Azure 容器實例中的容器僅在準備好時接收請求
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935687"
---
# <a name="configure-readiness-probes"></a>設定整備度探查

對於為流量提供服務的容器化應用程式，您可能需要驗證容器是否已準備好處理傳入請求。 Azure 容器實例支援就緒探測以包括配置，以便在某些情況下無法訪問容器。 準備探測就像[庫伯內斯準備探測器](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。 例如，容器應用可能需要在啟動期間載入大型資料集，並且不希望它在此期間接收請求。

本文介紹如何部署包含就緒探測的容器組，以便容器僅在探測成功時接收流量。

Azure 容器實例還支援[動態探測](container-instances-liveness-probe.md)，您可以將其配置為使不正常的容器自動重新開機。

> [!NOTE]
> 當前，您不能在部署到虛擬網路的容器組中使用就緒探測。

## <a name="yaml-configuration"></a>YAML 配置

例如，創建包含`readiness-probe.yaml`就緒探測的以下程式碼片段的檔。 此檔定義一個容器組，該容器組由運行小型 Web 應用的容器組成。 應用從公共`mcr.microsoft.com/azuredocs/aci-helloworld`映射部署。 此容器化應用在 Azure[中使用 Azure CLI](container-instances-quickstart.md)和其他快速啟動部署容器實例中也進行了演示。

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

部署包括定義`command`在容器首次開始運行時運行的啟動命令的屬性。 此屬性接受字串陣列。 此命令類比 Web 應用運行時，但容器未就緒。 

首先，它啟動 shell 會話並運行`node`命令以啟動 Web 應用。 它還啟動一個命令以休眠 240 秒，之後它將創建一個在`ready``/tmp`目錄中調用的檔：

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>就緒命令

此 YAML 檔定義`readinessProbe`支援作為`exec`就緒檢查的就緒命令的 。 此示例就緒命令測試`ready``/tmp`目錄中是否存在該檔。

當`ready`檔不存在時，就緒命令將退出，該值為非零;容器繼續運行，但無法訪問。 當命令使用結束代碼 0 成功退出時，容器已準備好被訪問。 

屬性`periodSeconds`指定就緒命令應每 5 秒執行一次。 就緒探測在容器組的存留期內運行。

## <a name="example-deployment"></a>部署範例

運行以下命令以部署具有上述 YAML 配置的容器組：

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>查看就緒檢查

在此示例中，在前 240 秒內，當準備命令檢查`ready`檔是否存在時，該命令將失敗。 返回的狀態碼表示容器未就緒。

這些事件可從 Azure 入口網站或 Azure CLI 來檢視。 例如，門戶顯示類型`Unhealthy`事件在就緒命令失敗時觸發。 

![入口網站狀況不良的事件][portal-unhealthy]

## <a name="verify-container-readiness"></a>驗證容器就緒性

啟動容器後，可以驗證它最初是否不可訪問。 預配後，獲取容器組的 IP 位址：

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

嘗試在就緒探測失敗時訪問網站：

```bash
wget <ipAddress>
```

輸出顯示最初無法訪問網站：
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240 秒後，就緒命令成功，發出容器已準備就緒的信號。 現在，當您運行該命令`wget`時，它成功：

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

容器準備就緒後，還可以使用 Web 瀏覽器流覽 IP 位址來訪問 Web 應用。

> [!NOTE]
> 就緒探測繼續運行容器組的存留期。 如果就緒命令稍後發生故障，則容器將再次變得不可訪問。 
> 

## <a name="next-steps"></a>後續步驟

在涉及由從屬容器組成的多容器組的情況下，就緒性探測可能很有用。 有關多容器方案的詳細資訊，請參閱[Azure 容器實例中的容器組](container-instances-container-groups.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
