---
title: Azure HDInsight 中的 Apache Ambari 活動訊號問題
description: 在 Azure HDInsight 中檢查 Apache Ambari 信號問題的各種原因
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: adc5dfcef8cce269b6b6d982178433b8ee163f92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998258"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Ambari 活動訊號問題

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="scenario-high-cpu-utilization"></a>案例：高 CPU 使用率

### <a name="issue"></a>問題

Ambari 代理程式具有高 CPU 使用率，這會導致 Ambari UI 的警示，而某些節點會遺失 Ambari 代理程式的信號。 警示遺失警示通常是暫時性的。

### <a name="cause"></a>原因

由於各種 ambari 代理程式的錯誤，在罕見的情況下，您的 ambari 代理程式可以有高 (接近100的 CPU 使用率) 百分比。

### <a name="resolution"></a>解決方法

1. 識別 ambari 代理程式的處理序識別碼 (pid) ：

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 然後執行下列命令來顯示 CPU 使用率：

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 重新開機 ambari 代理程式以減輕問題：

    ```bash
    service ambari-agent restart
    ```

1. 如果重新開機無法運作，請終止 ambari 代理程式進程，然後啟動它：

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>案例： Ambari 代理程式未啟動

### <a name="issue"></a>問題

Ambari 代理程式未啟動，這會導致 Ambari UI 的警示，而導致某些節點的 Ambari 代理程式心跳遺失。

### <a name="cause"></a>原因

警示是由 Ambari 代理程式未執行所造成。

### <a name="resolution"></a>解決方法

1. 確認 ambari 代理程式的狀態：

    ```bash
    service ambari-agent status
    ```

1. 確認容錯移轉控制器服務是否正在執行：

    ```bash
    ps -ef | grep failover
    ```

    如果容錯移轉控制器服務未執行，可能是因為發生問題，導致 hdinsight 代理程式無法啟動容錯移轉控制器。 檢查 hdinsight 代理程式記錄檔中的記錄檔 `/var/log/hdinsight-agent/hdinsight-agent.out` 。

## <a name="scenario-heartbeat-lost-for-ambari"></a>案例： Ambari 的心跳已遺失

### <a name="issue"></a>問題

Ambari 的心跳代理程式已中斷。

### <a name="cause"></a>原因

OMS 記錄會造成高 CPU 使用率。

### <a name="resolution"></a>解決方法

* 使用 [AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell Cmdlet 停用 Azure 監視器記錄。
* 刪除 `mdsd.warn` 記錄檔

---

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]