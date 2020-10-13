---
title: Azure HDInsight 叢集的重新開機 Vm
description: 瞭解如何重新開機 Azure HDInsight 叢集的沒有回應的 Vm。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: 149a82526263f5e372db81b5a92a9ee90a2c76f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089967"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>重新開機 HDInsight 叢集的 Vm

Azure HDInsight 叢集包含一組虛擬機器， (Vm) 為叢集節點。 針對長時間執行的叢集，這些節點可能會因各種原因而沒有回應。 本文說明如何在 HDInsight 叢集中重新開機沒有回應的 Vm。

## <a name="when-to-reboot"></a>重新開機的時機

> [!WARNING]
> 當您在叢集中重新開機 Vm 時，節點無法使用，且節點上的服務必須重新開機。

當節點重新開機時，叢集可能會變成狀況不良，且工作可能會變慢或失敗。 如果您嘗試重新開機作用中的前端節點，將會停止所有執行中的工作。 在服務重新開機並執行之前，您將無法將作業提交到叢集。 基於這些理由，您應該只在必要時重新開機 Vm。 當下列情況時，請考慮重新開機 Vm：

- 您無法使用 SSH 來進入節點，但它會回應 ping。
- 在 Ambari UI 中，背景工作節點沒有任何信號。
- 節點上的暫存磁片已滿。
- VM 上的進程資料表有許多已完成進程的專案，但它是以「終止狀態」列出。

> [!WARNING]
> 當您重新開機 **HBase** 和 **Kafka** 叢集的 vm 時請小心，因為重新開機可能會導致資料遺失。

## <a name="use-powershell-to-reboot-vms"></a>使用 PowerShell 重新開機 Vm

使用節點重新開機作業需要兩個步驟：列出節點和重新開機節點。

1. 列出節點。 您可以在 [AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost)取得叢集節點清單。

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. 重新開機主機。 取得您想要重新開機的節點名稱之後，請使用 [AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)重新開機節點。

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>使用 REST API 重新開機 Vm

您可以使用 API **檔** 中的 [試用] 功能，將要求傳送到 HDInsight。 使用節點重新開機作業需要兩個步驟：列出節點和重新開機節點。

1. 列出節點。 您可以從 REST API 或 Ambari 中取得叢集節點清單。 如需詳細資訊，請參閱 [HDInsight 清單主機 REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. 重新開機主機。 取得您想要重新開機的節點名稱之後，請使用 REST API 重新開機節點，以重新開機節點。 節點名稱遵循 NodeType 的模式* (wn/hn/zk/gw) *  +  *x*叢集  +  *名稱的前六個字元*。 如需詳細資訊，請參閱 [HDInsight 重新開機主機 REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

您要重新開機之節點的實際名稱是在要求主體中的 JSON 陣列中指定。

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
