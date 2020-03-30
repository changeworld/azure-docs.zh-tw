---
title: Azure HDInsight 中的 Apache Ambari 活動訊號問題
description: 回顧 Azure HDInsight 中阿帕奇安巴里心跳問題的各種原因
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057068"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Ambari 活動訊號問題

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="scenario-high-cpu-utilization"></a>方案：CPU 利用率高

### <a name="issue"></a>問題

Ambari 代理具有較高的 CPU 利用率，這會導致來自 Ambari UI 的警報，即對於某些節點，Ambari 代理活動訊號將丟失。 失去的心跳警報通常是暫時的。

### <a name="cause"></a>原因

由於各種 ambari 代理錯誤，在極少數情況下，您的 ambari 代理可以具有高（接近 100） 百分比 CPU 利用率。

### <a name="resolution"></a>解決方案

1. 識別 ambari 代理的進程 ID （pid）：

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 然後執行下列命令來顯示 CPU 使用率：

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 重新開機 ambari 代理以緩解問題：

    ```bash
    service ambari-agent restart
    ```

1. 如果重新開機不起作用，請終止 ambari 代理進程，然後啟動它：

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>方案：Ambari 代理未啟動

### <a name="issue"></a>問題

Ambari 代理尚未啟動，導致來自 Ambari UI 的警報，即對於某些節點，Ambari 代理活動訊號將丟失。

### <a name="cause"></a>原因

警報是由 Ambari 代理未運行引起的。

### <a name="resolution"></a>解決方案

1. 確認安巴里代理的狀態：

    ```bash
    service ambari-agent status
    ```

1. 確認容錯移轉控制器服務是否正在運行：

    ```bash
    ps -ef | grep failover
    ```

    如果容錯移轉控制器服務未運行，則可能是由於問題阻止 hdinsight 代理啟動容錯移轉控制器。 從`/var/log/hdinsight-agent/hdinsight-agent.out`檔檢查 hdinsight 代理日誌。

## <a name="scenario-heartbeat-lost-for-ambari"></a>場景：安巴里的心跳丟失

### <a name="issue"></a>問題

安巴里心跳劑丟失。

### <a name="cause"></a>原因

OMS 日誌導致 CPU 利用率高。

### <a name="resolution"></a>解決方案

* 使用[禁用 AzHDInsight 監視](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)電源 Cmdlet 禁用 Azure 監視器日誌記錄。
* 刪除`mdsd.warn`日誌檔

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
