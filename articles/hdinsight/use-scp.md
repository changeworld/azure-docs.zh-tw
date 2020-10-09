---
title: 在 Azure HDInsight 中搭配使用 SCP 與 Apache Hadoop
description: 本檔提供使用 ssh 和 scp 命令連接到 HDInsight 的相關資訊。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 44c87a3415a236f614510aa3163ad2e9cbd1f64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856172"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>在 Azure HDInsight 中搭配使用 SCP 與 Apache Hadoop

本文提供的資訊可讓您安全地傳輸檔案與 HDInsight 叢集。

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

`scp` 只能存取叢集內個別節點的檔案系統。 它無法用來存取叢集的 HDFS 相容儲存體中的資料。

當您需要從 SSH 工作階段上傳資源以供使用時，請使用 `scp`。 例如，上傳 Python 指令碼，然後從 SSH 工作階段執行指令碼。

如需直接將資料載入 HDFS 相容儲存體的資訊，請參閱下列文件：

* [使用 Azure 儲存體的 HDInsight](hdinsight-hadoop-use-blob-storage.md)。
* [使用 Azure Data Lake Storage Gen1 的 HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)。

## <a name="next-steps"></a>後續步驟

* [搭配使用 SSH 與 HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [在 HDInsight 中使用邊緣節點](hdinsight-apps-use-edge-node.md#access-an-edge-node)
