---
title: 在 Azure HDInsight 中搭配使用 SCP 與 Apache Hadoop
description: 本檔提供使用 ssh 和 scp 命令連線到 HDInsight 的相關資訊。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 559746a817442602c76ba91f12c195be1d7f3cc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188424"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>在 Azure HDInsight 中搭配使用 SCP 與 Apache Hadoop

本文提供有關使用 HDInsight 叢集安全地傳輸檔案的資訊。

## <a name="copy-files"></a>複製檔案

`scp` 公用程式可用於雙向複製叢集中個別節點的檔案。 例如，下列命令可將 `test.txt` 目錄從本機系統複製到主要前端節點：

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

因為未在 `:` 之後指定路徑，所以此檔案會置於 `sshuser` 主目錄中。

下列範例可將 `test.txt` 檔案從主要前端節點上的 `sshuser` 主目錄複製到本機系統：

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` 只能存取叢集內個別節點的檔案系統。 它無法用來存取叢集之 HDFS 相容儲存體中的資料。

當您需要從 SSH 工作階段上傳資源以供使用時，請使用 `scp`。 例如，上傳 Python 指令碼，然後從 SSH 工作階段執行指令碼。

如需直接將資料載入 HDFS 相容儲存體的資訊，請參閱下列文件：

* [使用 Azure 儲存體的 HDInsight](hdinsight-hadoop-use-blob-storage.md)。
* [使用 Azure Data Lake Storage 的 HDInsight](hdinsight-hadoop-use-data-lake-store.md)。

## <a name="next-steps"></a>後續步驟

* [搭配使用 SSH 與 HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [在 HDInsight 中使用邊緣節點](hdinsight-apps-use-edge-node.md#access-an-edge-node)
