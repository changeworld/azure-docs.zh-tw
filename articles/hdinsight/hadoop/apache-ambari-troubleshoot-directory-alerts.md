---
title: Azure HDInsight 中的 Apache Ambari 目錄警示
description: HDInsight 中 Apache Ambari 目錄警示的可能原因和解決方案的討論與分析。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998306"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 Apache Ambari 目錄警示

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

您會收到來自 Apache Ambari 的錯誤，如下所示：

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>原因

受影響的背景工作角色節點 () 上缺少提及的 Ambari 警示目錄。

## <a name="resolution"></a>解決方法

在受影響的背景工作節點上手動建立遺漏的目錄， (s) 。

1. 透過 SSH 連線到相關的背景工作節點。

1. 取得根使用者： `sudo su` 。

1. 以遞迴方式建立所需的目錄。

1. 變更這些目錄的擁有者和群組。

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. 從 Apache Ambari UI 中，停用，然後啟用警示。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]