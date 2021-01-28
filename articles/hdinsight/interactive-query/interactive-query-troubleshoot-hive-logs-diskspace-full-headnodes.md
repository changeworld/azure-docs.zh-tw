---
title: 疑難排解： Apache Hive 記錄填滿磁碟空間-Azure HDInsight
description: 本文提供當 Apache Hive 記錄填滿 Azure HDInsight 中前端節點的磁碟空間時，所要遵循的疑難排解步驟。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930959"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>案例： Apache Hive 記錄檔將會填滿前端節點上的磁碟空間 Azure HDInsight

本文說明 Azure HDInsight 叢集中前端節點的磁碟空間不足時的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

在 Apache Hive/LLAP 叢集上，不需要的記錄會佔用前端節點上的整個磁碟空間。 這種情況可能會導致下列問題：

- SSH 存取失敗，因為前端節點上沒有空格。
- Ambari 擲回 *HTTP 錯誤：503服務無法使用*。
- HiveServer2 Interactive 無法重新開機。

`ambari-agent`當問題發生時，記錄檔會包含下列專案：
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

在 advanced Hive log4j 設定中，目前的預設刪除排程是根據上次修改日期，刪除超過30天的檔案。

## <a name="resolution"></a>解決方案

1. 移至 Ambari 入口網站上的 Hive 元件摘要，然後選取 [ **配置] 索引** 標籤。

2. 移至 [ `Advanced hive-log4j` **Advanced settings**] 中的區段。

3. 將 `appender.RFA.strategy.action.condition.age` 參數設定為您選擇的年齡。 此範例會將年齡設定為14天： `appender.RFA.strategy.action.condition.age = 14D`

4. 如果您沒有看到任何相關的設定，請附加這些設定：
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. 設定 `hive.root.logger` 為 `INFO,RFA` ，如下列範例所示。 預設設定為 `DEBUG` ，這會使記錄檔變大。

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. 儲存設定，然後重新開機必要的元件。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
