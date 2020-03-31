---
title: Apache Hive 日誌填滿磁碟空間 - Azure HDInsight
description: Apache Hive 日誌正在填滿 Azure HDInsight 中頭節點上的磁碟空間。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943959"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>方案：Apache Hive 日誌正在填滿 Azure HDInsight 中頭節點上的磁碟空間

本文介紹了與 Azure HDInsight 群集中頭節點上的磁碟空間不足相關的問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

在 Apache Hive/LLAP 群集上，不需要的日誌佔用了頭節點上的整個磁碟空間。 因此，可以看到以下問題。

1. SSH 訪問失敗，因為頭節點上沒有空間。
2. Ambari 提供*HTTP ERROR： 503 服務不可用*。

出現問題`ambari-agent`時，日誌將顯示以下內容。
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

在高級 Hive-log4j 配置中，省略參數*log4j.appender.RFA.maxBackupIndex。* 它會導致日誌檔的無限生成。

## <a name="resolution"></a>解決方案

1. 導航到 Ambari 門戶上的 Hive 元件摘要`Configs`，然後按一下選項卡。

2. 轉到"`Advanced hive-log4j`高級設置"中的部分。

3. 將`log4j.appender.RFA`參數設置為滾動檔應用程式。 

4. 設置`log4j.appender.RFA.MaxFileSize`，`log4j.appender.RFA.MaxBackupIndex`如下所示。

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. 設置為`hive.root.logger``INFO,RFA`如下所示。 預設設置為 DEBUG，這使得日誌變得非常大。

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. 保存配置並重新啟動所需的元件。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
