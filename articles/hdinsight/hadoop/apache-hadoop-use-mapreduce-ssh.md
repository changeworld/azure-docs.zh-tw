---
title: 使用 Apache Hadoop 的 MapReduce 和 SSH 連線-Azure HDInsight
description: 了解如何使用 SSH，以利用 HDInsight 上的 Apache Hadoop 來執行 MapReduce 作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 2736d0cfe514252e36ba6d7e0d71efe09da15aae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86076295"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>搭配使用 MapReduce 與 HDInsight 上的 Apache Hadoop 和 SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

了解如何從 HDInsight 的安全殼層 (SSH) 連線提交 MapReduce 作業。

> [!NOTE]
> 如果您已熟悉使用以 Linux 為基礎的 Apache Hadoop 伺服器，但剛接觸 HDInsight，請參閱 [以 Linux 為基礎的 HDInsight 秘訣](../hdinsight-hadoop-linux-information.md)。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Hadoop 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="use-hadoop-commands"></a>使用 Hadoop 命令

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到您的叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

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

## <a name="next-steps"></a>接下來的步驟

如您所見，Hadoop 命令提供簡單的方法，在 HDInsight 叢集中執行 MapReduce 工作，然後檢視工作輸出。 如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)
* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hdinsight-use-hive.md)
