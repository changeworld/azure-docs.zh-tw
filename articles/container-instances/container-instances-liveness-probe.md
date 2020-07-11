---
title: 在容器實例上設定活動探查
description: 了解如何在 Azure 容器執行個體中設定活躍度探查，以重新啟動狀況不良的容器
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: befe9693be1413abf455d915814c53aab20db53c
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169692"
---
# <a name="configure-liveness-probes"></a>設定活躍度探查

容器化應用程式可能會長時間執行，因而導致可能需要藉由重新開機容器來修復的狀態中斷。 Azure 容器實例支援活動探查，讓您可以設定容器群組中的容器，以便在重要功能無法運作時重新開機。 活動探查的行為就像是[Kubernetes 的活動探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

本文說明如何部署包含活躍度探查的容器群組，示範如何自動重新啟動模擬的狀況不良容器。

Azure 容器實例也支援[就緒探查](container-instances-readiness-probe.md)，您可以進行設定，以確保只有當流量準備好時，才會到達容器。

> [!NOTE]
> 目前您無法在部署至虛擬網路的容器群組中使用活動探查。

## <a name="yaml-deployment"></a>YAML 部署

使用下列程式碼片段來建立 `liveness-probe.yaml` 檔案。 此檔案所定義的容器群組是由最終會變成狀況不良的 NGNIX 容器所組成的。

```yaml
apiVersion: 2019-12-01
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

### <a name="start-command"></a>Start 命令

此部署包含 `command` 定義啟動命令的屬性，該命令會在容器第一次開始執行時執行。 這個屬性會接受字串陣列。 此命令會模擬進入狀況不良狀態的容器。

首先，它會啟動 bash 會話，並在目錄中建立名為的檔案 `healthy` `/tmp` 。 然後，它會在刪除檔案之前睡眠30秒，然後進入10分鐘的睡眠狀態：

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>活躍度命令

這個部署會定義 `livenessProbe` ，它支援 `exec` 做為活動檢查的活動命令。 如果此命令以非零值結束，容器將會終止並重新啟動，併發出信號， `healthy` 找不到檔案。 如果此命令成功結束，結束代碼為0，則不會採取任何動作。

`periodSeconds` 屬性會指定活躍度命令應該每隔 5 秒執行一次。

## <a name="verify-liveness-output"></a>確認活躍度輸出

在第一個 30 秒內，啟動命令所建立的 `healthy` 就會存在。 當活動命令檢查檔案 `healthy` 是否存在時，狀態碼會傳回0，表示信號成功，因此不會重新開機。

30秒之後，命令就會 `cat /tmp/healthy` 開始失敗，而導致發生狀況不良和終止的事件。

這些事件可從 Azure 入口網站或 Azure CLI 來檢視。

![入口網站狀況不良的事件][portal-unhealthy]

藉由在 Azure 入口網站中查看事件， `Unhealthy` 會在活動命令失敗時觸發類型的事件。 後續事件的類型為 `Killing` ，表示容器刪除，因此可以開始重新開機。 每次發生此事件時，容器的重新開機計數會遞增。

重新開機會就地完成，因此會保留公用 IP 位址和節點特定內容之類的資源。

![入口網站重新啟動計數器][portal-restart]

如果活動探查持續失敗並觸發太多次重新開機，則您的容器會進入指數退避延遲。

## <a name="liveness-probes-and-restart-policies"></a>活躍度探查和重新啟動原則

重新啟動原則會取代活躍度探查所觸發的重新啟動行為。 例如，如果您設定 `restartPolicy = Never` *和*活動探查，容器群組將不會因為失敗的活動檢查而重新開機。 容器群組會改為遵守的容器群組重新開機原則 `Never` 。

## <a name="next-steps"></a>後續步驟

工作型案例可能需要活躍度探查，以在必要功能未正常運作時啟用自動重新啟動。 如需有關執行工作型容器的詳細資訊，請參閱[在 Azure 容器執行個體中執行容器化工作](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
