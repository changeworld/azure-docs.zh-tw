---
title: Apache HBase REST 未回應 Azure HDInsight 中的要求
description: 解決 Apache HBase REST 未在 Azure HDInsight 中回應要求的問題。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: b42a37d752f245eb66f3952b56ee4e36ae954874
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936956"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>案例： Apache HBase REST 未回應 Azure HDInsight 中的要求

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

Apache HBase REST 服務不會回應 Azure HDInsight 中的要求。

## <a name="cause"></a>原因

此處可能的原因可能是 Apache HBase REST 服務正在流失通訊端，這在服務執行很長的時間時特別常見 (例如，月) 。 從用戶端 SDK，您可能會看到類似下列的錯誤訊息：

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>解決方案

Ssh 至主機之後，請使用下列命令重新開機 HBase REST。 您也可以使用腳本動作，在所有背景工作節點上重新開機此服務：

```bash
sudo service hdinsight-hbrest restart
```

此命令會停止相同主機上的 HBase 區域伺服器。 您可以透過 Ambari 手動啟動 HBase 區域伺服器，或讓 Ambari 自動重新開機功能自動復原 HBase 區域伺服器。

如果問題仍然持續發生，您可以在每個背景工作節點上，將下列緩和腳本安裝為每隔5分鐘執行一次的 CRON 作業。 此緩和腳本會偵測 REST 服務，並在 REST 服務沒有回應時將其重新開機。

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]