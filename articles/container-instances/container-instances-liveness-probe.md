---
title: 在容器實例上設置活動探測
description: 了解如何在 Azure 容器執行個體中設定活躍度探查，以重新啟動狀況不良的容器
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934169"
---
# <a name="configure-liveness-probes"></a>設定活躍度探查

容器化應用程式可能會長時間運行，從而導致通過重新開機容器來修復的損壞狀態。 Azure 容器實例支援動態探測，以便在關鍵功能不起作用時，可以在容器組中配置容器以重新開機。 活度探頭就像[一個庫伯內斯活度探測器](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

本文說明如何部署包含活躍度探查的容器群組，示範如何自動重新啟動模擬的狀況不良容器。

Azure 容器實例還支援[就緒探測](container-instances-readiness-probe.md)，您可以對其進行配置，以確保流量僅在容器準備就緒時到達該容器。

> [!NOTE]
> 當前，您不能在部署到虛擬網路的容器組中使用活動探測。

## <a name="yaml-deployment"></a>YAML 部署

使用下列程式碼片段來建立 `liveness-probe.yaml` 檔案。 此檔案所定義的容器群組是由最終會變成狀況不良的 NGNIX 容器所組成的。

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

執行下列命令，使用上述 YAML 設定來部署此容器群組：

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>啟動命令

部署包括定義`command`在容器首次開始運行時運行的啟動命令的屬性。 此屬性接受字串陣列。 此命令類比進入不正常狀態的容器。

首先，它啟動 bash 會話並創建在`healthy``/tmp`目錄中調用的檔。 然後，在刪除檔之前，它會睡 30 秒，然後進入 10 分鐘的睡眠時間：

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>活躍度命令

此部署定義`livenessProbe`支援充當活動性檢查`exec`的活度命令。 如果此命令退出時具有非零值，則容器將被殺死並重新啟動，無法找到檔`healthy`的信號。 如果此命令在結束代碼 0 時成功退出，則不執行任何操作。

`periodSeconds` 屬性會指定活躍度命令應該每隔 5 秒執行一次。

## <a name="verify-liveness-output"></a>確認活躍度輸出

在第一個 30 秒內，啟動命令所建立的 `healthy` 就會存在。 當活動性命令檢查`healthy`檔是否存在時，狀態碼返回 0，表示成功，因此不會發生重新開機。

30 秒後，`cat /tmp/healthy`該命令開始失敗，導致發生不正常和終止事件。

這些事件可從 Azure 入口網站或 Azure CLI 來檢視。

![入口網站狀況不良的事件][portal-unhealthy]

通過在 Azure 門戶中查看事件，類型`Unhealthy`事件在活動命令失敗時觸發。 後續事件的類型`Killing`為 ，表示容器刪除，以便可以開始重新開機。 每次發生此事件時，容器的重新開機計數都會遞增。

重新開機就地完成，以便保留公共 IP 位址和特定于節點的內容等資源。

![入口網站重新啟動計數器][portal-restart]

如果活動探測持續失敗並觸發太多重新開機，則容器將進入指數級回退延遲。

## <a name="liveness-probes-and-restart-policies"></a>活躍度探查和重新啟動原則

重新啟動原則會取代活躍度探查所觸發的重新啟動行為。 例如，如果設置了`restartPolicy = Never`*和*活度探測，則容器組不會因為活動檢查失敗而重新開機。 容器組則遵循容器組的重新開機策略`Never`。

## <a name="next-steps"></a>後續步驟

工作型案例可能需要活躍度探查，以在必要功能未正常運作時啟用自動重新啟動。 如需有關執行工作型容器的詳細資訊，請參閱[在 Azure 容器執行個體中執行容器化工作](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
