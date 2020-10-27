---
title: 填滿磁碟空間的 Apache Hive 記錄-Azure HDInsight
description: Apache Hive 記錄檔會將 Azure HDInsight 中前端節點的磁碟空間填滿。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 5554a66927fc70f22ec552b938ae62038a04acb9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533014"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>案例： Apache Hive 記錄檔將會填滿前端節點上的磁碟空間 Azure HDInsight

本文說明 Azure HDInsight 叢集中前端節點的磁碟空間不足的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

在 Apache Hive/LLAP 叢集上，不需要的記錄會佔用前端節點上的整個磁碟空間。 基於此原因，可能會出現下列問題。

1. SSH 存取失敗，因為前端節點上沒有空格。
2. Ambari 提供 *HTTP 錯誤：503服務無法使用* 。
3. HiveServer2 Interactive 無法重新開機。

`ambari-agent`當問題發生時，記錄檔會顯示下列各項。
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

在 advanced hive log4j 設定中，會根據上次修改日期，針對超過30天的檔案設定目前的預設刪除排程。

## <a name="resolution"></a>解決方案

1. 流覽至 Ambari 入口網站上的 Hive 元件摘要，然後按一下 [] 索引標籤 `Configs` 。

2. 移至 [ `Advanced hive-log4j` Advanced settings] 中的區段。

3. 將 `appender.RFA.strategy.action.condition.age` 參數設定為您選擇的年齡。 14天的範例： `appender.RFA.strategy.action.condition.age = 14D`

4. 如果您沒有看到任何相關的設定，請附加下列設定。
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. 將設定 `hive.root.logger` 為， `INFO,RFA` 如下所示。 預設設定為 DEBUG，讓記錄變得非常大。

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. 儲存這些參數，並重新啟動必要的元件。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。