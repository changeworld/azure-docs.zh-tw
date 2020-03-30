---
title: Map 減少和 SSH 連接與阿帕奇哈多普 - Azure HDInsight
description: 了解如何使用 SSH，以利用 HDInsight 上的 Apache Hadoop 來執行 MapReduce 作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 543bc29adc85bd767de9479607d067fadf7b0078
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934707"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>搭配使用 MapReduce 與 HDInsight 上的 Apache Hadoop 和 SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

了解如何從 HDInsight 的安全殼層 (SSH) 連線提交 MapReduce 作業。

> [!NOTE]
> 如果您已熟悉使用以 Linux 為基礎的 Apache Hadoop 伺服器，但剛接觸 HDInsight，請參閱 [以 Linux 為基礎的 HDInsight 秘訣](../hdinsight-hadoop-linux-information.md)。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Hadoop 叢集。 請參閱[使用 Azure 門戶創建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="use-hadoop-commands"></a>使用 Hadoop 命令

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到群集。 通過將 CLUSTERNAME 替換為群集的名稱來編輯下面的命令，然後輸入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 連線到 HDInsight 叢集之後，請使用下列命令來啟動 MapReduce 作業：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    此命令會啟動 `hadoop-mapreduce-examples.jar` 檔案中包含的 `wordcount` 類別。 它會使用 `/example/data/gutenberg/davinci.txt` 文件作為輸入，而輸出會儲存在 `/example/data/WordCountOutput`。

    > [!NOTE]
    > 如需關於此 MapReduce 工作和範例資料的詳細資訊，請參閱 [在 HDInsight 上的 Apache Hadoop 中使用 MapReduce](hdinsight-use-mapreduce.md)。

    作業會在處理時發出詳細資料，並於作業完成時傳回類似下列文字的資訊：

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. 作業完成時，請使用下列命令來列出輸出檔案︰

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    此命令會顯示兩個檔案︰`_SUCCESS` 和 `part-r-00000`。 `part-r-00000` 檔案包含此作業的輸出。

    > [!NOTE]  
    > 某些 MapReduce 工作可能會將結果分成多個 **part-r-#####** 檔案。 若是如此，請使用 ##### 尾碼指出檔案的順序。

1. 若要檢視輸出，請使用下列命令：

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    此命令會顯示 **wasbs://example/data/gutenberg/davinci.txt** 檔案中所含的單字清單和每個單字的出現次數。 下列文字此檔案所包含之資料的範例：

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>後續步驟

如您所見，Hadoop 命令提供簡單的方法，在 HDInsight 叢集中執行 MapReduce 工作，然後檢視工作輸出。 如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)
* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hdinsight-use-hive.md)
