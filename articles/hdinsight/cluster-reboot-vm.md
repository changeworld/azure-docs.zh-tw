---
title: Azure HDInsight 叢集的重新開機 Vm
description: 瞭解如何重新開機未回應的 HDInsight 叢集 Vm。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077009"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>重新開機 HDInsight 叢集的 Vm

HDInsight 叢集包含 Vm 群組做為叢集節點。 針對長時間執行的叢集，這些節點可能因各種原因而變得沒有回應。 本文說明如何在 HDInsight 叢集中重新開機沒有回應的 Vm。

## <a name="when-to-reboot"></a>何時重新開機

> [!WARNING]  
> 重新開機叢集中的 Vm 會導致節點停機，並重新啟動節點上的服務。 

當節點正在重新開機時，叢集可能會變得狀況不良，作業可能會變慢或失敗。 如果您嘗試重新開機使用中的前端節點，所有執行中的作業都會被終止，而您必須等到服務再次啟動並執行之後，才能將作業提交至叢集。 您應該考慮只在必要時才重新開機 Vm。 以下是考慮何時應重新開機 Vm 的一些指引。

- 您無法透過 SSH 連線到節點，但它會回應 ping。
- 背景工作節點在 Ambari UI 中沒有任何心跳。
- 節點上的暫存磁片已滿。
- VM 上的進程資料表有許多專案，其中的進程已完成，但卻以「終止狀態」列出。

> [!WARNING]  
> 當您重新開機**HBase**和**Kafka** clustes 的 vm 時，應該更小心，因為這可能會導致資料遺失。

## <a name="use-powershell-to-reboot-vms"></a>使用 PowerShell 來重新開機 Vm

使用節點重新開機作業需要兩個步驟： [列出節點] 和 [重新開機] 節點。

1. 列出節點。 您可以透過[AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost)取得叢集節點清單。 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. 重新開機主機。 取得您想要重新開機的節點名稱之後，請使用[重新開機-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)重新開機節點。

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>使用 REST API 重新開機 Vm

您可以使用 API**檔**中的 [試用] 功能，將要求傳送至 HDInsight。 使用節點重新開機作業需要兩個步驟： [列出節點] 和 [重新開機] 節點。

1. 列出節點。 您可以從 REST API 或 Ambari 中取得叢集節點清單。 如需更多詳細資料，請參閱 REST API 作業的[HDInsight 清單主機](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. 重新開機主機。 取得您想要重新開機的節點名稱之後，請使用 [重新開機節點] REST API 重新開機節點。節點名稱遵循 **"NodeType （w)/hn/zk/gw）" + "x" + "「叢集名稱的前6個字元**」的模式。 如需更多詳細資料，請參閱 REST API 作業的[HDInsight 重新開機主機](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

您想要重新開機之節點的實際名稱是在要求主體的 JSON 陣列中指定。

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>後續步驟

* [重新開機-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight 虛擬機器 REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)
